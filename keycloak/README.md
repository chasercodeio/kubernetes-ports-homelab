# Keycloak

## Files in Dir

- README.md
- configmap.yaml
- pv.yaml
- pvc.yaml
- keycloak-dep.yaml
- postgres-dep.yaml
- service.yaml
- kustomization.yaml

## Keycloak Setup

Benefits of this setup:

- Communication with a postgres database
- More env options set / given in the configmap (better for configuration)
- For all configuration options [https://www.keycloak.org/server/all-config#category-cache](https://www.keycloak.org/server/all-config#category-cache)

---

1. Edit configmap to suit your needs
2. Make sure the paths in both the postgres deployment and the keycloak deployment are created and usable (change the PV and PVC)
3. Edit the postgres-dep file and change the envs to a stronger login creds (make sure to also change the configmap variables associated with db login)
4. Edit the service.yaml to suit your configuration needs (NodePort rather than ClusterIP maybe)

## Run KeyCloak

After the setup, to run (if you are in the directory):

```bash
kubectl apply -k .
```

## Teardown KeyCloak

To teardown the full application, just run:

```bash
kubectl delete -k .
```

To delete just the keycloak app (leaving the database and service ports):

```bash
kubectl delete -f keycloak-dep.yaml
```

## Application Auth Workflow (Since instructions were not too clear for me)

### Pre-Req

- Change Admin Password so it is not as insecure

### 1. Create a new realm

1. Login to master realm using the default admin credentials (admin:admin unless you changed them)
1. Admin login portal: [http://localhost:30001](http://localhost:30001/)
1. Click "Keycloak:master" dropdown menu on the upper left of the sidebar and click on "create realm"
1. Name the realm (using "homelab" as the example) and make sure that "Enabled" is on
1. Make sure you are in the newly created realm "homelab" in my case by clicking the dropdown again and clicking on the new realm (might default to it after creation)

### 2. Create a new user

1. Make sure you are in the new realm
2. Click on "Users" option in the sidebar to the left
3. Select "Add User" blue button near the top middle of the page
4. Add all the options under "General" and select "Create" at the bottom of the screen
5. To make sure you select the newly created user, select the "Users" option again and click on the created user
6. Under the "credentials" tab towards the middle of the middle of the page, select the "Create Password" button and assign the user a new password
7. Make sure to toggle off the "Temporary Password" button since we do not wanna bother with setting a new password on user login
8. To verify a new user has been created and to login as the user:
9. As the admin, go to the "Clients" menu in the sidebar (Making sure you are on the right realm and as the admin)
10. You can view all the endpoints under the "Home Url" column, select the "home URL" link for the first option "account" (e.g: [http://localhost:30001/realms/homelab/account/](http://localhost:30001/realms/homelab/account/))
11. Use the user creds and login. As you login to other applications, the names of those applications will populate here.

### 3. Finding all the important endpoints for application authentication **IMPORTANT**

- Extra information: [https://www.keycloak.org/docs/25.0.6/securing_apps/index.html](https://www.keycloak.org/docs/25.0.6/securing_apps/index.html)
- More Info: [https://www.keycloak.org/guides#securing-apps](https://www.keycloak.org/guides#securing-apps)

1. For applications that support generic OIDC, you will (usually) need the following:

```yaml
OIDC_CLIENT_ID: ""
OIDC_CLIENT_SECRET: ""
OIDC_AUTH_URI: ""
OIDC_TOKEN_URI: ""
OIDC_USERINFO_URI: ""
OIDC_LOGOUT_URI: ""
```

2. To find these information, the configuration page recommends using the `/realms/{realm-name}/.well-known/openid-configuration` web route to get this information however, it is hard to find as the json is not formatted. To fix this, use the following below (keep in mind, the example below is for the "homelab" realm):

```bash
curl http://localhost:30001/realms/homelab/.well-known/openid-configuration | json_pp -json_opt pretty,canonical
```

- To output this to a file, (to make it easier to search):

```bash
curl http://localhost:30001/realms/homelab/.well-known/openid-configuration | json_pp -json_opt pretty,canonical > homelab_known_edpoints.json
```

3. Find the following to populate the above environment variables:

- `OIDC_AUTH_URI` = `authorization_endpoint` (search)
- `OIDC_TOKEN_URI` = `token_endpoint`
- `OIDC_USERINFO_URI` = `userinfo_endpoint`
- `OIDC_LOGOUT_URI` = `end_session_endpoint`

For other two environment vars `OIDC_CLIENT_ID` and `OIDC_CLIENT_SECRET`, we will need to create a new client in the admin interface in Keycloak.

1. Login as admin using the console url [http://localhost:30001/](http://localhost:30001/)
2. Change the realm to the newly created one ("homelab" for me)
3. Select the first menu option "Clients" in the sidebar to the left
4. Select "Create Client" blue option towards the middle of the page
5. Fill in the options to your configuration:
   1. Make sure the "client type" is set to "OpenID Connect"
   2. Name the Client ID something rememberable and application specific (for an application like outline it would be `outline-OIDC`) (This is what you put for the `OIDC_CLIENT_ID` env)
   3. Name the "name" field with the name of the application (optional)
   4. Similarly for the "description" field (optional)
   5. No need to select the "Always display in UI" toggle
6. Select "Next" towards the bottom of the page
   1. Make sure "Client Authentication" option is selected **important**
   2. You can keep everything else default
7. Select "Next" again to get to the next section
   1. You can skip the first two URL fields (_Root URL_ and _Home URL_)
   2. For the `Valid redirect URIs`, make sure you add the base url of the application and add an "\*" at the end to make sure all subroutes for it are allowed e.g: `http://localhost:30010/*` (just another application which has auth)
   3. For the `Valid post logout redirect URIs`, just add the same base url for the application except without the "\*". e.g: `http://localhost:30010/`
   4. Same thing for the `web origins` field. e.g: `http://localhost:30010`
8. Now hit **Save** at the bottom of the page and it will direct you to the full settings page for the client you just created
9. Verify all the settings are configured properly and scroll down to the "Login Settings" section
   1. For the **Login Theme** option, select something (I chose keycloak.v2)
   2. Toggle the **Consent required** toggle (_optional_)
   3. Everything else is optional and do not need to be tinkered with
10. Select "Save" and navigate to the "Credentials" tab for the client. (**Will not show up unless "Client Authentation" option was enabled earlier**)
    1. Under **Client Authenticator**, make sure the default `Client ID and Secret` option is selected
    2. Copy the `Client Secret` and add that to the `OIDC_CLIENT_SECRET` env.
    3. Do not need to touch the **Registration access token** option.
    4. We are now done with the auth interface.

Final configuration for this section should look something like this:

```yaml
OIDC_CLIENT_ID: "outline-OIDC"
OIDC_CLIENT_SECRET: "CLIENT_SECRET"
OIDC_AUTH_URI: "http://localhost:30001/realms/homelab/protocol/openid-connect/auth/"
OIDC_TOKEN_URI: "http://localhost:30001/realms/homelab/protocol/openid-connect/token/"
OIDC_USERINFO_URI: "http://localhost:30001/realms/homelab/protocol/openid-connect/userinfo/"
OIDC_LOGOUT_URI: "http://localhost:30001/realms/homelab/protocol/openid-connect/logout/"
```

This should work now and the application which required auth will redirect to the login page for KeyCloak (where you will login with the user you created)

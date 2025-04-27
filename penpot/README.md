# PENPOT

[Hosting on kubernetes](https://help.penpot.app/technical-guide/getting-started/kubernetes/)

**Note: The tutorial above uses helm to install penpot, however, I want to use kustomize and have something like DIUN let me know when a newer version comes out. So, I have converted the docker compose version to kubernetes**

[Hosting on Docker docs](https://help.penpot.app/technical-guide/getting-started/docker/)

[Configuration](https://help.penpot.app/technical-guide/configuration/)

## NOTES:

- `ingress.yaml` provided for routing if needed. Will work without 

- Using DEMO is quite buggy and after first one, the create demo button no longer works even after a db reset

- Tested with basic login / password but should work pretty easily with keycloak auth (OIDC)

## USING THE POSTGRES DATABASE

If you have created a demo user and the site won't allow you to register another one (DEMO testing), time to reset the demo user.

1. Exec into the postgres database container

```bash
kubectl exec -it penpot-postgres-<rand> -- bash
```

2. Start the postgres database (inside the container) and query the db

```bash
# (These commands are inside the container)
$ psql -U penpot

penpot=# SELECT email FROM profile

# EXAMPLE:
#                email                
# -------------------------------------
#   demo-1745758271567.demo@example.com
# (1 row)

```

3. Now delete these users to reset the db
```bash
penpot=# UPDATE profile SET is_demo=false, deleted_at=null WHERE email = 'demo-1745758271567.demo@example.com';
# UPDATE 1  (output)
```

4. Now exit the database

```bash
penpot=# \q
exit
```

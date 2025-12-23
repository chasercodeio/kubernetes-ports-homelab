# COBALT

> best way to save what you love

## To Run

- [Official cobalt github](https://github.com/imputnet/cobalt/tree/main)
- [Processing Instance Docs github](https://github.com/imputnet/cobalt/blob/main/docs/run-an-instance.md)
- [Front end github](https://github.com/imputnet/cobalt/tree/main/web)

### Kubernetes Kustomization

This only loads the local processing instance and posts endpoints to use for processing. Need the front-end for the actual UI

```bash
# From this directory,
kubectl apply -k .
```

Read the docs for more options + environment variables. Might be interesting to add Watchtower

## [Cobalt-UI](./ui/README.md)

To know more about getting the UI working on kubernetes, have a look at the [readme for the ui](./ui/README.md).

## ISSUES

- For some reason, even if the instance is accessible in the browser using the pod ip and port, the web ui refuses to connect to the instance. I have tried everything and could not get it to work with anything other than localhost.
  - This is with service set to nodeport and cillium networking allow all connections to and from.

## TODO

- [x] Bundle the web into its own docker image / dockerfile

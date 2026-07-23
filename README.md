# Nginx Proxy service for Kubernetes on Wodby

Run Nginx Proxy as a reusable Kubernetes application service with Wodby.

This repository defines the Wodby service manifests and operational
configuration for Nginx Proxy.

- [Browse Wodby services](https://wodby.com/services)
- [Wodby service documentation](https://wodby.com/docs/2.0/services/)
- [Service manifest reference](https://wodby.com/docs/2.0/services/template/)

## Service overview

| Property | Manifest configuration |
| --- | --- |
| Service name | `nginx-proxy` |
| Type | Application service |
| Versions | `1.29` by default; also available: `1.28`, `1.27`, `1.25` |
| Workloads | `main` (Deployment), primary; scalable |
| Containers | `nginx` using `wodby/nginx` |
| Endpoints | `http`: HTTP 80 (main) |
| Service links | Backend, required |
| Helm | chart `oci://registry-1.docker.io/wodby/nginx`; version `0.2.0` |
| Configuration | 2 configuration files |

## Use this service

Reference `nginx-proxy` from a Wodby stack to use this service.

A service is a reusable component and does not deploy by itself. The stack
defines its links, settings, versions, resources, and relationship to the rest
of the application.

## Maintain a custom version

1. Fork this repository.
2. Edit the service manifest and referenced files.
3. Import the repository as a [Git-backed service](https://wodby.com/docs/2.0/services/create/#create-a-git-backed-service).
4. Reference the service from a stack manifest.

Keep service, workload, container, endpoint, link, volume, config, and
derivative names stable unless dependent stacks and app-level overrides are
updated at the same time.

Validate the manifests with:

```bash
wodby service validate-manifest service.yml --org <org-id>
```

See the [service manifest reference](https://wodby.com/docs/2.0/services/template/) and the [managed services index](https://github.com/wodby/services).

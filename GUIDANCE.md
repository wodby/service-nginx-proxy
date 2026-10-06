# Nginx Proxy on Wodby

An Nginx server that passes HTTP requests to one linked service. It serves no application files and is not built from a repository.

## How it works

- The required backend link sets `NGINX_BACKEND_HOST` and `NGINX_BACKEND_PORT` to the linked service's host and port. They form the only upstream.
- The service sets `NGINX_VHOST_PRESET` to `http-proxy`. Requests are passed to the backend over HTTP with the headers `Host`, `X-Forwarded-For` and `X-Forwarded-Proto`. The backend must listen on the port of its endpoint and should read the scheme and client address from those headers.
- The preset sets no `Upgrade` or `Connection` headers.

The configuration is rendered from templates and environment variables on every start, into `/etc/nginx/nginx.conf`, `/etc/nginx/conf.d/vhost.conf`, `/etc/nginx/preset.conf` and `/etc/nginx/upstream.conf`. Never edit these files in a running container.

## Requests that do not reach the backend

The image's default rules are answered by Nginx itself:

- `/.healthz` returns 204. It reports on the proxy, not on the backend.
- `/favicon.ico`, `/robots.txt`, `/humans.txt`, `/ads.txt`, `/llms.txt`, everything under `/.well-known/`, and paths ending in `.flv`, `.mp4`, `.m4a` or `.mov` are looked up on the proxy's own disk, where there are no application files: a missing favicon gets an empty image, the others are not found.
- Other paths that start with a dot are denied, as are `wodby.yml` and `Makefile`.

Set `NGINX_VHOST_NO_DEFAULTS` on the service when the backend must answer these paths itself.

Nginx also adds the response headers `X-XSS-Protection`, `X-Frame-Options`, `X-Content-Type-Options` and a `Content-Security-Policy` that forbids framing. Set `NGINX_HEADERS_CONTENT_SECURITY_POLICY` to change the policy, or `NGINX_NO_DEFAULT_HEADERS` to leave headers to the backend.

## Changing configuration

- Environment variables on the service. The ones that matter most for a proxied application: `NGINX_CLIENT_MAX_BODY_SIZE` (largest accepted request body), `NGINX_BACKEND_FAIL_TIMEOUT`, `NGINX_SET_REAL_IP_FROM` and `NGINX_REAL_IP_HEADER`, `NGINX_GZIP`.
- The two declared config files: the main config template and the virtual host template.

Other services reach the proxy over HTTP on port 80 at the app service's name inside the environment.

## Check the result

- `nginx -T` prints the configuration in effect, including the upstream.
- `curl -sI localhost/.healthz` returns 204 from inside the container.

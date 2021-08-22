# Note

This is a demo for using nginx as reverse proxy with self-signed SSL.

## Create self-signed SSL

First, to create a self-signed SSL, run below command on the local machine:

```
openssl req -x509 -nodes -days 365 \
  -subj "/C=AU/ST=NSW/O=TTL, Inc./CN=localhost" \
  -newkey rsa:2048 \
  -keyout ./certs/nginx-selfsigned.private-key \
  -out ./certs/nginx-selfsigned.crt
```

It should create 2 files:

- `./certs/nginx-selfsigned.crt`
- `./certs/nginx-selfsigned.private-key`

The options references:
- `-x509` means we want to create a self-signed certificate instead of generating a certificate signing request.
- `-nodes`  Do not encrypt the private key with a passphrase so that nginx can read it.
- `subj`: `/C` for country, `/ST` for state, `/O` for organization, `/CN` for common name

## Spin up docker containers

Run

```
docker-compose up
```

The command creates 2 containers:

- `docker-nginx-self-signed-ssl_nginx-proxy_1`: this is the main nginx container which
  servers as the reverse proxy. The `443` port of this container is exposed to
  the docker's host machine on port `6443`.
- `docker-nginx-self-signed-ssl_another-service_1`: this nginx container serves the simple
  html file `another-service.html` for demo. This container doesn't expose any
  port to its host machine. Instead, we access the web server via the previous
  nginx proxy.

## Result

- Open https://localhost:6443/ on the browser. It should show the text `This is an awesome app.`
  from the `docker-nginx-self-signed-ssl_another-service_1` container.
- Open https://localhost:6443/test on the browser. It should show the default
  nginx's html content from the nginx server running on the `docker-nginx-self-signed-ssl_nginx-proxy_1`
  container.


# Docker.Hera

Setup for the [Hera](https://github.com/aschzero/hera) utility to manage [Cloudflare Argo Tunnels](https://developers.cloudflare.com/argo-tunnel/reference/how-it-works/) automatically in Docker.

<img alt="Hera" src="https://s3-us-west-2.amazonaws.com/aschzero-hera/hera.png" width="400px">

## Quickstart

```bash
# Clone this repo (can be anywhere, doesn't have to be in /opt)
git clone https://github.com/Monadical-SAS/docker.hera /opt/docker.hera
cd /opt/docker.hera

# Place your cloudflare cert into data/hera/<yourdomain>.pem
open 'https://www.cloudflare.com/a/warp'
mv ~/Downloads/cert.pem /opt/docker.hera/data/certs/example.com.pem

# Create the hera network on the host
docker network create hera

# Start the hera management container + test http server container
docker-compose up -d
```

## Getting a Certificate

Cloudflare uses a certificate to authenticate your host, so you must generate one so that Hera can manage tunnels on your behalf.

1. Download a new certificate from https://www.cloudflare.com/a/warp
2. Rename the certificate to match your domain + `.pem`, and move it into the data folder `docker.hera/data/hera/yourdomain.com.pem`

## Example Usage

In another docker project on your server, you can now expose containers and Hera will automatically set up the ingress DNS+SSL based on the container labels.

```yaml
version: '3.7'

services:
    demo:
        image: python:3.8-alpine
        working_dir: /var/www
        entrypoint: bash -c 'echo "Hera is up" > index.html; python3 -m http.server 8000'
        networks:
            - hera
        labels:
            # replace this with your actual domain
            hera.hostname: demo.example.com
            hera.port: 8000

networks:
    hera:
        external:
            name: hera
```

You can test this project by running:
```bash
docker-compose up -d

# Then visit the domain you specified in the hera.hostname label
open http://demo.example.com
# it should say "Hera is up"
```

## Further Reading

- https://www.cloudflare.com/products/argo-tunnel/
- https://github.com/aschzero/hera
- https://developers.cloudflare.com/argo-tunnel/reference/how-it-works/

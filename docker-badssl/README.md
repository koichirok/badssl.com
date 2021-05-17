# koichirok/badssl.test

Serve your [badssl.com](https://github.com/chromium/badssl.com) container without ubuntu VM.

## How to use this image

### Running badssl.com with default domain name (badssl.test)

```
docker run -p 80:80 -p 443:443 --rm --name badssl -d koichirok/badssl.test
```

### Running badssl.com with custom domain name

```
docker run -p 80:80 -p 443:443 --rm --name badssl -e TEST_DOAMIN=your.custom.domain  -d koichirok/badssl.test
```

### list hosts

in /etc/hosts format:

```
docker exec badssl make list-hosts
```

as docker-compose network aliases:

```
docker exec badssl make list-as-compose-network-aliases
```

### Share certificates with other containers

If CERTS_DIR environment variables is specified, this image copies current certificates to CERTS_DIR.

So you can share generated certificates with other contianers like below:

```
# run image with CERTS_DIR environment variable
docker run -d -v ./current-certs:/current-certs -e CERTS_DIR=/current-certs koichirok/badssl.com
# run other container
docker run -v ./current-certs:/badssl-certs DOCKER_IMAGE_YOU_WANT_TO_RUN
```

## For developers

To generage Dockerfile for this image, run following command:

```
make docker-badssl/Dockerfile
```

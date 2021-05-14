# koichirok/badssl.test

Serve your [badssl.com](https://github.com/chromium/badssl.com) container without ubuntu VM.

## How to use this image

### Running badssl.com with default domain name (badssl.test)

```
docker run -p 80:80 -p 443:443 --rm --name badssl.test -d koichirok/badssl.test
```

### Running badssl.com with custom domain name

```
docker run -p 80:80 -p 443:443 --rm --name badssl.test -d koichirok/badssl.test
```

## For developers

To generage Dockerfile for this image, run following command:

```
make -f Makefile.docker-badssl.test
```

# Start with Ubuntu 16.04 (LTS), and build badssl.com up from there
FROM ubuntu:16.04
MAINTAINER April King <april@pokeinthe.io>
EXPOSE 80 443
RUN apt-get update && apt-get install -y --no-install-recommends apt-transport-https \
        software-properties-common; \
    apt-add-repository ppa:brightbox/ruby-ng; \
    apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        libffi-dev \
        make \
        nginx \
        ruby2.4 \
        ruby2.4-dev; \
    gem update --system 2.7.11; \
    gem install jekyll; \
    ln -sf /dev/stderr /var/log/nginx/error.log; \
    ln -sf /dev/stdout /var/log/nginx/access.log; \
    rm -rf /var/lib/apt/lists/*

# Install badssl.com
ADD . badssl.com
WORKDIR badssl.com

# Start things up!
CMD ["make", "certs-then-serve"]

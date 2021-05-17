OUT_DOCKERFILE	=docker-badssl/Dockerfile
IMAGE_NAME	?= badssl.com
DOCKER_COMPOSE_INDENT_WIDTH ?= 2
DOCKER_COMPOSE_NETWORK_NAME ?= default

# store original TEST_DOMAIN before include (workaround for hardcoded TEST_DOMAIN)
ifdef TEST_DOMAIN
_TEST_DOMAIN = $(shell echo $$TEST_DOMAIN)
endif
include Makefile
ifdef _TEST_DOMAIN
export TEST_DOMAIN = $(_TEST_DOMAIN)
endif

# sed expression to generate fully dockernized version of Dockerfile
define DOCKERFILE_GENERATOR
/RUN apt/,/RUN .*jekyll/ {
    s/^RUN apt-get install -y /    /;
    s/apt-get install -y/& --no-install-recommends/;
    /apt-transport-https/{s/$$/ \\/; t;}
    /git/{d; t;}
    s/^/    /;
    s/gem update --system$$/& 2.7.11/;
    s/[^\\]$$/&; \\/;
    s/RUN //;
    /gem install jekyll/ {
	a \ \ \ \ ln -sf /dev/stderr /var/log/nginx/error.log; \\
        a \ \ \ \ ln -sf /dev/stdout /var/log/nginx/access.log; \\
        a \ \ \ \ rm -rf /var/lib/apt/lists/*
    };
};
t;
/RUN make inside-docker.*/d;
/^CMD/ {
    s/nginx.*/["make", "certs-then-serve"]/;
}
endef
export DOCKERFILE_GENERATOR

.DEFAULT_GOAL = $(OUT_DOCKERFILE)
$(OUT_DOCKERFILE):
	@sed -e "$$DOCKERFILE_GENERATOR" Dockerfile > "$(OUT_DOCKERFILE)"

# workaround for hardcoded badssl.test in original "list-hosts" target
list-hosts:
	@make -s -f Makefile list-hosts | sed -e 's/badssl\.test/$(TEST_DOMAIN)/g'

.PHONY: certs-then-serve
certs-then-serve: clean certs-test inside-docker
	@if [ -d "$$CERTS_DIR" ]; then \
		rm -rf "$$CERTS_DIR"/*; \
		cd certs/sets/current && cp -R . "$$CERTS_DIR"/; \
	fi
	@printf "=%.0s" $$(seq 80); echo
	@echo 'You can list all $(TEST_DOMAIN) hosts in /etc/hosts format by:'
	@echo
	@echo '  docker exec YOUR_CONTAINER make list-hosts'
	@echo
	@echo "You can also list all $(TEST_DOMAIN) hosts as docker-compose's network aliases by:"
	@echo
	@echo '  docker exec YOUR_CONTAINER make list-as-compose-network-aliases'
	@printf "=%.0s" $$(seq 80); echo
	@nginx -g "daemon off;"

.PHONY: list-as-compose-network-aliases
list-as-compose-network-aliases:
	@\
		indent() {\
			printf \
				"$$(printf ' %.0s' $$(seq $(DOCKER_COMPOSE_INDENT_WIDTH)))%.0s" \
				$$(seq $${1-1});\
		};\
		echo "$$(indent 2)networks:";\
		echo "$$(indent 3)$(DOCKER_COMPOSE_NETWORK_NAME):";\
		echo "$$(indent 4)aliases:";\
		make -s list-hosts 2>/dev/null \
			| sed -e "s/^/$$(indent 5)/" -e "s/127.0.0.1/-/" -e "/####/s/hosts/aliases/"

.PHONY: badssl.test-docker-image
badssl.test-docker-image: $(OUT_DOCKERFILE)
	docker build -t $(IMAGE_NAME) -f $(OUT_DOCKERFILE) .

# You can set these variables from the command line, and also
# from the environment for the first two.
SHELL               =/bin/bash
DOCKER_BUILD       ?= docker build -f
SOURCEDIR           = .
BUILDDIR            = build

VERSIONS           ?=1.34.1
LIBSODIUM_VERSION   ?=66f017f1
# Put it first so that "make" without argument is like "make help".
help:
	@$(DOCKER) --help

.PHONY: help Makefile clean


all: $(VERSIONS)
clean:
node: all
force:

$(SUBDIRS):force
	$(MAKE) -C $@ $(MAKECMDGOALS)


$(VERSIONS): force
	@$(DOCKER_BUILD) Dockerfile \
		--build-arg VERSION=$@ \
		--build-arg LIBSODIUM_VERSION=${LIBSODIUM_VERSION} \
		--tag gen2-pool/cardano-node:$@ \
		--tag gen2-pool/cardano-node:latest .




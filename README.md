# container-user-check

> Check the image, not the Dockerfile that claims to have built it.

**Status:** 🚧 In development

## Overview

Fail CI on images that run as root, declare no USER, or ship a shell in a distroless-claimed base, checked against the built image rather than the Dockerfile alone.

## Features

- Reads the image config from the registry or a local tarball and resolves the effective UID after every layer, including a numeric USER that maps to root
- Fails an image with no USER instruction at all, which defaults to root regardless of intent
- Walks the layer filesystem for `/bin/sh`, `/bin/bash`, busybox and package managers, and fails a base that advertises distroless while shipping them
- Resolves a USER that names a user absent from `/etc/passwd`, which silently runs as root on some runtimes
- Checks the whole multi-stage result, so a hardened final stage is not undone by a copied-in binary that needs a shell wrapper
- Policy file for per-image exceptions with a required reason, and SARIF output with a non-zero exit code

## Stack

Go + go-containerregistry for image and layer access, cobra, and spf13/viper for the policy file.

## Usage

```bash
container-user-check --image ghcr.io/myorg/api:1.4.2 \
  --deny-root --require-user --no-shell --policy ./policy.yaml --format sarif
```

## License

MIT

# Conduit Custom Image

Custom Docker image for Conduit with dynamic configuration generation based on environment variables.

## Quick Start

Build the custom Conduit image:

```bash
cd src/conduit/image
docker build -t conduit .
docker tag conduit localhost/conduit
```

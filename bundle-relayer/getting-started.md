---
description: ⚠️ Bundle relayer is in beta
---

# Getting started

## Using Docker

Make sure docker daemon is running if not already.

{% content-ref url="installing-docker.md" %}
[installing-docker.md](installing-docker.md)
{% endcontent-ref %}

### Install docker image

The Bundler Relayer docker image is hosted on [Docker Hub](https://hub.docker.com/r/hopprotocol/v2-bundle-relayer).

Pull the Bundle Relayer docker image:

```bash
docker pull hopprotocol/v2-bundle-relayer
```

### Running Image

Create file with environment variables:

```bash
DB_PATH=/leveldb
PRIVATE_KEY=Your_Private_Key
```

Run docker image as daemon:

```bash
docker run \
  -d \
  --name bundle-relayer \
  -p 8000:8000 \
  --env-file docker.env \
  -v /tmp/leveldb:/leveldb \
  hopprotocol/v2-bundle-relayer worker --server
```

#### Flags

|Name|Description|Example|
|----|----|-----------|-------|
|`--server`|Enable API server.|eg. `--server`|
|`--indexer-poll-seconds`|The number of seconds to wait between indexer polls.|eg. `--indexer-poll-seconds 10`|
|`--exit-bundle-poll-seconds`|The number of seconds to wait between exit bundle polls.|eg. `--indexer-poll-seconds 10`|
|`--exit-bundle-retry-delay-seconds`|The number of seconds to wait between exit bundle retries.|eg. `--indexer-poll-seconds 600`|

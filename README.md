# SKO Workshop

This repo is the hands-on workspace for the SKO workshop. It contains the
declarative SAM configuration and step-by-step guides you will follow during
the session.

## Table of Contents

- [Getting Started](#getting-started)
- [Environment Setup](#environment-setup)
  - [Prerequisites](#prerequisites)
  - [If using Docker solace broker: Add the Solace Broker to the Docker Network](#if-using-docker-solace-broker-add-the-solace-broker-to-the-docker-network)
  - [Verify the Environment](#verify-the-environment)

## Getting Started

Follow the guides in the guides directory, starting with
[Getting Started](guides/Getting_Started.md).

## Environment Setup

### Prerequisites

Before starting, make sure you have the following:

1. The `sam-enterprise` Docker image loaded locally (`docker images | grep sam-enterprise`)
    > You can use SAM Desktop instead
1. Docker with a running Solace broker container attached to the `sam-network` bridge network
    > You can use Solace Cloud instead
1. SAM cli installed and placed in your bin executable
    ```
    ln -sf "$repo/bin/sam-enterprise" "$HOME/go/bin/sam"
    ```
    > You can replace `"$HOME/go/bin/sam"` with your $PATH bin 
1. A LiteLLM API token
1. Claude Code installed (`claude --version`)
1. The SAM CLI installed (`sam --version`)

### If using Docker solace broker: Add the Solace Broker to the Docker Network

If not already, get the solace broker running locally

**For Windows and Linux users:**

```
docker run -d -p 8080:8080 -p 55555:55555 -p 8008:8008 -p 1883:1883 -p 5672:5672 -p 9000:9000 -p 2222:2222 --shm-size=1g --env username_admin_globalaccesslevel=admin --env username_admin_password=admin --name=solace solace/solace-pubsub-standardCopy
```

**For macOS users:**

```
docker run -d -p 8080:8080 -p 55554:55555 -p 8008:8008 -p 1883:1883 -p 5672:5672 -p 9000:9000 -p 2222:2222 --shm-size=1g --env username_admin_globalaccesslevel=admin --env username_admin_password=admin --name=solace solace/solace-pubsub-standardCopy
```

The SAM container must reach the Solace broker by container name (`solace`). Both must be on the same Docker bridge network:

```bash
# Create the shared network if it does not exist
docker network create sam-network

# If your Solace broker container is already running, connect it
docker network connect sam-network solace

# Verify
docker network inspect sam-network --format '{{range .Containers}}{{.Name}} {{end}}'
```
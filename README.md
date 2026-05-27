# SKO Workshop

This repo is the hands-on workspace for the SKO workshop. It contains the
declarative SAM configuration and step-by-step guides you will follow during
the session.

## Table of Contents

- [Environment Setup](#environment-setup)
  - [Prerequisites](#prerequisites)
  - [If using Docker solace broker: Add the Solace Broker to the Docker Network](#if-using-docker-solace-broker-add-the-solace-broker-to-the-docker-network)
- [Getting Started](#getting-started)


## Environment Setup

### Prerequisites
All required executables will be shared the day of the workshop

Before starting, make sure you have the following:

1. SAM Desktop executable: SolaceAgentMesh.dmg or sam-desktop-enterprise.exe
1. OR The `sam-enterprise` Docker image loaded locally
    ```
    docker load -i sam-enterprise-latest.tar.gz
    ```
1. Docker with a running Solace broker container attached to the `sam-network` bridge network
    > Note: You can use Solace Cloud instead
1. SAM cli installed and placed in your bin executable
    
    ```bash
    # MacOS / Linux / WSL
    ln -sf "sam-enterprise" "$HOME/go/bin/sam"
    ```

    ```bash
    # Windows from cmd.ex
    echo @"%USERPROFILE%\sam-enterprise-windows-amd64.exe" %* > "%GOPATH%\bin\sam.cmd"
    ```

    > Note: You can replace `"$HOME/go/bin/sam"` with your $PATH bin 
1. A LiteLLM API token
1. Claude Code installed (`claude --version`)
1. The SAM CLI installed (`sam --version`)

### If using Docker solace broker: Add the Solace Broker to the Docker Network

If not already, get the solace broker running locally

The SAM container must reach the Solace broker by container name (`solace`). Both must be on the same Docker bridge network:

```bash
# Create the shared network if it does not exist
docker network create sam-network

# If your Solace broker container is already running, connect it
docker network connect sam-network solace
```

## Getting Started

Follow the guides in the guides directory, starting with

1. [Getting Started](guides/100_Getting_Started.md)
1. [Hiring](guides/200_Hiring.md)
2. [Onboarding](guides/300_Onboarding.md)
3. [Coaching](guides/400_Coaching.md)
4. [Supervision](guides/500_Supervision.md)
5. [Teamwork](guides/600_Teamwork.md)
6. [Improvement](guides/700_Improvement.md)

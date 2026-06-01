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
1. SAM Client
    1. SAM Desktop executable: SolaceAgentMesh.dmg or sam-desktop-enterprise.exe
        > Note: On MacOS, after installing the dmg execute the following
        ```bash
        xattr -cr /Applications/Solace\ Agent\ Mesh.app
        ```
        ![intro](./guides/img/intro.png)

        Follow the steps to configure SAM Desktop:
        - Get Started
        - Configure a model now
        - Enter LiteLLM key
        - Choose `Custom` as service type
        - Put `https://lite-llm.mymaas.net/` as endpoint
        - Choose `claude-sonnet-4-6` as the model
        - Test connection and make sure you get a success
        - Choose built-in

        ![intro](./guides/img/complete_desktop_config.png)

    1. OR The `sam-enterprise` Docker image loaded locally
        ```
        docker load -i sam-enterprise-latest.tar.gz
        docker compose up
        ```
        > Note: you can use podman instead
1. SAM cli installed in a dedicated dir
    > Note: make sure you do not have any `sam` command installed on your system
    > To confirm, open a terminal session and just type `sam` you should see no command found
    
    ```bash
    # Make sure the executable is called sam
    mv sam-enterprise sam
    # MacOS / Linux / WSL
    cd <path_to_cli_dir>
    chmod +ux sam
    xattr -cr sam
    export PATH=$PATH:$PWD
    # Open new terminal and confirm sam -v works
    ```
    > Note: to make sure the sam cli is executable in every terminal session, update your .profile (e.g. ~/.zshrc)

    ```
    echo "export PATH=$PATH:$PWD" >> ~/.zshrc 
    ```

    ```bash
    # Windows from cmd.ex
    echo @"%USERPROFILE%\sam-enterprise-windows-amd64.exe" %* > "%USERPROFILE%\bin\sam.cmd"
    ```
1. A LiteLLM API token
1. Claude Code installed (`claude --version`)
1. [Optional] Solace Broker
    1. Docker with a running Solace broker container attached to the `sam-network` bridge network
    
    1. OR you can use Solace Cloud instead

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

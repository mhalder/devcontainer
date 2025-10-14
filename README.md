# Development Container

A fully configured Ubuntu-based development container with Rust tooling, modern CLI utilities, and a customized shell environment.

## Features

- **Base Image**: Ubuntu latest
- **Shell**: Zsh with Starship prompt
- **Languages**: Rust (stable toolchain with rust-analyzer)
- **Essential Tools**:
  - Git, curl, wget
  - tmux, vim
  - build-essential
  - Network utilities (ping)
- **Rust Tools**:
  - cargo-binstall for faster tool installations
  - stau (status utility)
  - starship (cross-shell prompt)

## Getting Started with DevPod

[DevPod](https://devpod.sh/) provides a simple way to create reproducible development environments.

### Initial Setup

```bash
# Add the Docker provider
devpod-cli provider add docker
```

### Using VS Code

```bash
# Configure DevPod to use VS Code
devpod-cli ide use vscode

# Start the development environment
devpod-cli up .
```

### Using Neovim

```bash
# Configure DevPod to use no IDE (direct SSH access)
devpod-cli ide use none

# Start the development environment
devpod-cli up .

# SSH into the container
devpod-cli ssh
```

## Prerequisites

- [Docker](https://www.docker.com/get-started)
- [Docker Compose](https://docs.docker.com/compose/install/)
- [VS Code](https://code.visualstudio.com/) with [Dev Containers extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) (recommended)

## Quick Start

### Using VS Code Dev Containers

1. Open this project in VS Code
2. Press `F1` and select **Dev Containers: Reopen in Container**
3. Wait for the container to build and start
4. Your workspace will be mounted at `/home/ubuntu/workspace`

### Using Docker Compose Directly

```bash
# Build and start the container
docker-compose -f .devcontainer/docker-compose.yml up -d

# Attach to the container
docker-compose -f .devcontainer/docker-compose.yml exec devcontainer zsh
```

## Configuration

### User Setup

The container creates a non-root user (`ubuntu`) with:

- UID/GID matching your host user (configurable via `UID` and `GID` environment variables)
- Sudo privileges without password
- Zsh as the default shell

### Volume Mounts

- **Workspace**: Your project directory is mounted at `/home/ubuntu/workspace`
- **SSH Keys**: Host SSH keys are mounted read-only at `/home/ubuntu/.ssh`

### Environment Variables

- **GITHUB_TOKEN**: Set this to authenticate with GitHub for downloading releases and avoiding rate limits:
  ```bash
  export GITHUB_TOKEN=your_github_token_here
  docker compose -f .devcontainer/docker-compose.yml up
  ```
- **UID/GID**: Set these to match your host user for proper file permissions (defaults to current user):
  ```bash
  UID=$(id -u) GID=$(id -g) docker compose -f .devcontainer/docker-compose.yml up -d
  ```

### Customization

#### Adding VS Code Extensions

Edit `.devcontainer/devcontainer.json`:

```json
{
  "customizations": {
    "vscode": {
      "extensions": ["rust-lang.rust-analyzer", "tamasfe.even-better-toml"]
    }
  }
}
```

#### Installing Additional Packages

Modify `.devcontainer/Dockerfile` to add more APT packages:

```dockerfile
RUN apt-get update && apt-get install -y \
    your-package-here \
    && rm -rf /var/lib/apt/lists/*
```

#### Adding Dev Container Features

Use [Dev Container Features](https://containers.dev/features) in `devcontainer.json`:

```json
{
  "features": {
    "ghcr.io/devcontainers/features/node:1": {
      "version": "lts"
    }
  }
}
```

## Dotfiles

This container automatically clones a [dotfiles repository](https://github.com/mhalder/dotfiles.git) (refactor/zsh branch) during the build process. The dotfiles provide additional shell configurations and aliases.

## Rebuilding

After making changes to the Dockerfile or devcontainer.json:

**VS Code**:

- Press `F1` → **Dev Containers: Rebuild Container**

**Docker Compose**:

```bash
docker-compose -f .devcontainer/docker-compose.yml build
docker-compose -f .devcontainer/docker-compose.yml up -d
```

## Troubleshooting

### Permission Issues

If you encounter permission issues with mounted files, ensure the `UID` and `GID` environment variables match your host user:

```bash
UID=$(id -u) GID=$(id -g) docker-compose -f .devcontainer/docker-compose.yml up -d
```

### Shell Not Starting

If the container builds but Zsh doesn't start properly, check the build logs:

```bash
docker-compose -f .devcontainer/docker-compose.yml logs
```

### Cargo Commands Not Found

Ensure the PATH is set correctly. The Rust toolchain is installed at `/home/ubuntu/.cargo/bin` and should be automatically added to PATH.

## License

This project configuration is provided as-is for development purposes.

# Ubuntu Development Container

A fully-featured development container with modern CLI tools, multiple language runtimes, and pre-configured dotfiles.

## Features

- **Base**: Ubuntu latest with zsh shell
- **Languages**: Python 3.12, Node.js 22, Rust (stable), Go
- **Editors**: Neovim 0.11.4 with custom config
- **CLI Tools**: tmux, fzf, ripgrep, fd, ack, btop, k9s, yazi, lazy, gh, kubectl
- **Version Control**: git, jj (Jujutsu)
- **Package Managers**: cargo, npm, uv (Python), cargo-binstall
- **Debugging**: lldb-18 with Python support
- **Custom**: Personal dotfiles from [mhalder/dotfiles](https://github.com/mhalder/dotfiles) and [mhalder/nvim](https://github.com/mhalder/nvim)

## Usage

### VS Code

1. Install the "Dev Containers" extension
2. Open this folder in VS Code
3. Click "Reopen in Container" when prompted (or use Command Palette: "Dev Containers: Reopen in Container")

### DevPod

1. Install [DevPod](https://devpod.sh/)
2. Add this repository:
   ```bash
   devpod up /path/to/devcontainer
   ```
3. DevPod will automatically build and start the container using the devcontainer.json configuration

### Docker Compose

```bash
# Build and start
docker-compose -f .devcontainer/docker-compose.yml up -d

# Attach to container
docker-compose -f .devcontainer/docker-compose.yml exec devcontainer zsh

# Stop
docker-compose -f .devcontainer/docker-compose.yml down
```

## Configuration

### User Permissions

The container creates an `ubuntu` user with:
- UID/GID matching host (default: 1000)
- Passwordless sudo access
- Zsh as default shell

### Environment Variables

- `GITHUB_TOKEN`: GitHub authentication token (passed from host)
- `HOST_UID`/`HOST_GID`: Host user/group IDs for file permissions

### Volumes

- `..:/home/ubuntu/workspace`: Project files (cached)
- `~/.ssh:/home/ubuntu/.ssh`: SSH keys (read-only)

## Customization

To use your own dotfiles, modify line 61 in `.devcontainer/Dockerfile`:

```dockerfile
RUN git clone https://github.com/YOUR_USERNAME/dotfiles.git /home/ubuntu/dotfiles
```

To use your own Neovim config, modify line 133 in `.devcontainer/Dockerfile`:

```dockerfile
git clone https://github.com/YOUR_USERNAME/nvim.git ~/.config/nvim
```

FROM ubuntu:latest

# Install basic packages
RUN apt-get update && apt-get install -y \
    ack \
    build-essential \
    cmake \
    curl \
    fd-find \
    git \
    golang-go \
    imagemagick \
    iputils-ping \
    liblldb-18 \
    libssl-dev \
    lldb-18 \
    locales \
    luarocks \
    ncurses-bin \
    pkg-config \
    python3 \
    python3-dev \
    python3-lldb-18 \
    python3-pip \
    python3-pynvim \
    python3.12-venv \
    ripgrep \
    sudo \
    tmux \
    unzip \
    vim \
    wget \
    zsh \
    && rm -rf /var/lib/apt/lists/*

# Configure locale
RUN locale-gen en_US.UTF-8
ENV LANG=en_US.UTF-8 \
    LANGUAGE=en_US:en \
    LC_ALL=en_US.UTF-8

# Create ubuntu user with sudo privileges
ARG USERNAME=ubuntu
ARG USER_UID=1000
ARG USER_GID=1000

# Ensure ubuntu user exists and configure
RUN groupmod -g ${USER_GID} ubuntu || groupadd -g ${USER_GID} ubuntu && \
    usermod -u ${USER_UID} -g ${USER_GID} -s /bin/zsh ubuntu || \
    useradd -m -u ${USER_UID} -g ${USER_GID} -s /bin/zsh ubuntu && \
    echo "${USERNAME} ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# Set up workspace
RUN mkdir -p /home/ubuntu/workspace && \
    chown -R ubuntu:ubuntu /home/ubuntu

USER ubuntu
WORKDIR /home/ubuntu/workspace

# Clone dotfiles repository
RUN git clone https://github.com/mhalder/dotfiles.git /home/ubuntu/dotfiles

# Install Rust toolchain
RUN curl --proto "=https" --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y --default-toolchain stable

# Add cargo and local bin to PATH
ENV PATH="/home/ubuntu/.local/bin:/home/ubuntu/.cargo/bin:${PATH}"

# Install rust-analyzer
RUN . "$HOME/.cargo/env" && rustup component add rust-analyzer

# Install cargo-binstall
RUN curl -L --proto "=https" --tlsv1.2 -sSf https://raw.githubusercontent.com/cargo-bins/cargo-binstall/main/install-from-binstall-release.sh | bash

# Install tools using binstall for faster builds
RUN . "$HOME/.cargo/env" && cargo binstall -y starship stau oktofetch tmux-sessionizer

# Install nvm
ENV XDG_CONFIG_HOME="/home/ubuntu/.config"
RUN mkdir -p "/home/ubuntu/.config"
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash

# Install Node.js
ENV NVM_DIR="/home/ubuntu/.config/nvm"
RUN bash -c ". $NVM_DIR/nvm.sh && \
    nvm install 22 && \
    nvm alias default 22 && \
    nvm use 22 && \
    npm install -g @anthropic-ai/claude-code opencode-ai"

# Install uv (Python package manager)
RUN curl -LsSf https://astral.sh/uv/install.sh | sh

# Install neovim
RUN mkdir -p /home/ubuntu/.local/bin && \
    cd /tmp && \
    curl -LO https://github.com/neovim/neovim/releases/download/v0.11.4/nvim-linux-x86_64.tar.gz && \
    tar xzf nvim-linux-x86_64.tar.gz && \
    cp -r nvim-linux-x86_64/* /home/ubuntu/.local/ && \
    rm -rf nvim-linux-x86_64 nvim-linux-x86_64.tar.gz

# Setup Python virtualenv for neovim
RUN python3 -m venv /home/ubuntu/venv && \
    /home/ubuntu/venv/bin/pip install --upgrade pip && \
    /home/ubuntu/venv/bin/pip install pynvim

# Update oktofetch binaries
RUN oktofetch update

# Install kubectl
RUN curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl" && \
    chmod +x kubectl && \
    mv kubectl /home/ubuntu/.local/bin/

# Install dotfiles and configurations
RUN stau install btop --force && \
    stau install fzf --force && \
    stau install gh --force && \
    stau install git --force && \
    stau install jj --force && \
    stau install k9s --force && \
    stau install lazy --force && \
    stau install oktofetch --force && \
    stau install tmux --force && \
    stau install yazi --force && \
    stau install zsh --force

# Update oktofetch binaries
RUN oktofetch update

# Clone nvim config
RUN if [ ! -d ~/.config/nvim ]; then \
      git clone https://github.com/mhalder/nvim.git ~/.config/nvim; \
    else \
      echo "nvim config already exists, skipping clone"; \
    fi

# Set zsh as default shell
SHELL ["/bin/zsh", "-c"]

CMD ["/bin/zsh"]

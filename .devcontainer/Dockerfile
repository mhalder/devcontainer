FROM ubuntu:latest

# Install basic packages
RUN apt-get update && apt-get install -y \
    build-essential \
    curl \
    git \
    golang-go \
    iputils-ping \
    libssl-dev \
    locales \
    luarocks \
    ncurses-bin \
    pkg-config \
    python3 \
    python3-pip \
    python3.12-venv \
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
RUN git clone -b refactor/zsh https://github.com/mhalder/dotfiles.git /home/ubuntu/dotfiles

# Install Rust toolchain
RUN curl --proto "=https" --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y --default-toolchain stable

# Add cargo and local bin to PATH
ENV PATH="/home/ubuntu/.local/bin:/home/ubuntu/.cargo/bin:${PATH}"

# Install rust-analyzer
RUN . "$HOME/.cargo/env" && rustup component add rust-analyzer

# Install cargo-binstall
RUN curl -L --proto "=https" --tlsv1.2 -sSf https://raw.githubusercontent.com/cargo-bins/cargo-binstall/main/install-from-binstall-release.sh | bash

# Install tools using binstall for faster builds
RUN . "$HOME/.cargo/env" && cargo binstall -y starship stau oktofetch

# Configure zsh with starship
RUN echo 'eval "$(starship init zsh)"' >> /home/ubuntu/.zshrc

# Set zsh as default shell
SHELL ["/bin/zsh", "-c"]

CMD ["/bin/zsh"]

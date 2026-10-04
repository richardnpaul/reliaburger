FROM mcr.microsoft.com/devcontainers/rust:latest

ENV DEBIAN_FRONTEND=noninteractive

# Install system dependencies, kernel/eBPF build tools, and container runtime utilities
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    clang \
    llvm \
    libbpf-dev \
    btrfs-progs \
    buildah \
    busybox-static \
    iproute2 \
    nftables \
    runc \
    slirp4netns \
    uidmap \
    python3 \
    python3-pip \
    golang-go \
    curl \
    git \
    pkg-config \
    libssl-dev \
    lldb \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Add rust components
RUN rustup component add llvm-tools-preview clippy rustfmt

# Install cargo-nextest, cargo-binstall, cargo-llvm-cov, cargo-audit, and crane (multi-arch support)
RUN ARCH=$(uname -m) && \
    if [ "$ARCH" = "x86_64" ]; then NEXTEST_ARCH="linux"; else NEXTEST_ARCH="linux-arm"; fi && \
    curl -LsSf "https://get.nexte.st/latest/${NEXTEST_ARCH}" | tar zxf - -C /usr/local/cargo/bin && \
    curl -L --proto '=https' --tlsv1.2 -sSf https://raw.githubusercontent.com/cargo-bins/cargo-binstall/main/install-from-binstall-release.sh | bash && \
    cargo-binstall -y cargo-llvm-cov cargo-audit && \
    CRANE_ARCH=$([ "$ARCH" = "x86_64" ] && echo "x86_64" || echo "arm64") && \
    curl -sL "https://github.com/google/go-containerregistry/releases/latest/download/go-containerregistry_Linux_${CRANE_ARCH}.tar.gz" | tar -zxf - -C /usr/local/bin crane

# Create mount points and test cache directories with correct permissions for vscode user
RUN mkdir -p /sys/fs/bpf /home/vscode/.cache/reliaburger/test-images && \
    chown -R vscode:vscode /home/vscode/.cache

USER vscode

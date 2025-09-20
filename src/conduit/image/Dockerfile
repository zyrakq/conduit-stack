FROM rust:1.85.0-bullseye as builder

# Install ALL necessary dependencies for bindgen and RocksDB
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    clang \
    llvm-dev \
    libclang-dev \
    build-essential \
    pkg-config \
    libssl-dev \
    ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# Explicitly specify libclang path for bindgen
ENV LIBCLANG_PATH=/usr/lib/x86_64-linux-gnu
ENV BINDGEN_EXTRA_CLANG_ARGS="-I/usr/include"

WORKDIR /build

ARG REPO_URL=https://gitlab.com/zyrakq/conduit.git
ARG BRANCH=sso-oidc

# Clone repository
RUN git clone --depth 1 --branch ${BRANCH} ${REPO_URL} .

# Build with native target (avoid musl issues)
RUN cargo build --release

# Final image based on Debian slim
FROM debian:bullseye-slim

# Install only runtime dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# Create user
RUN useradd -r -s /bin/false -m -d /var/lib/matrix-conduit conduit

# Copy binary
COPY --from=builder /build/target/release/conduit /usr/local/bin/conduit

# Set permissions
RUN chown conduit:conduit /usr/local/bin/conduit
RUN chmod +x /usr/local/bin/conduit

USER conduit
WORKDIR /var/lib/matrix-conduit

ENV CONDUIT_DATABASE_PATH=/var/lib/matrix-conduit/
ENV CONDUIT_DATABASE_BACKEND=rocksdb
ENV CONDUIT_PORT=6167
ENV CONDUIT_ADDRESS=0.0.0.0
ENV CONDUIT_CONFIG=""

EXPOSE 6167
ENTRYPOINT ["conduit"]

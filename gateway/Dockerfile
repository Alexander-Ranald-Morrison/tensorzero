# ========== builder ==========

FROM rust:latest AS builder

WORKDIR /src

RUN apt-get update && apt-get install -y clang libc++-dev && rm -rf /var/lib/apt/lists/*

COPY . .
RUN git rev-parse HEAD

ARG CARGO_BUILD_FLAGS=""

RUN --mount=type=cache,id=tensorzero-gateway-release,sharing=shared,target=/usr/local/cargo/registry \
    --mount=type=cache,id=tensorzero-gateway-release,sharing=shared,target=/usr/local/cargo/git \
    cargo build --profile performance -p gateway $CARGO_BUILD_FLAGS && \
    cp -r /src/target/performance /release

# ========== base ==========

FROM debian:bookworm-slim AS base

RUN apt-get update && apt-get install -y ca-certificates openssl wget && rm -rf /var/lib/apt/lists/*

# ========== gateway ==========

FROM base AS gateway

RUN useradd -m -s /bin/bash gateway

USER gateway

COPY --from=builder /release/gateway /usr/local/bin/gateway

WORKDIR /app

EXPOSE 3000

ENTRYPOINT ["gateway"]

CMD ["--warn-default-cmd", "--config-file", "config/tensorzero.toml"]

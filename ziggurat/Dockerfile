ARG BUILD_FROM=ghcr.io/home-assistant/base:3.23

FROM rust:1-alpine AS builder

RUN apk add --no-cache git musl-dev

# The build context is the add-on directory, so the stack is built from a
# fresh fetch of the commit pinned below: bumping the pin is what invalidates
# this layer and rebuilds.
ARG ZIGGURAT_REF=6e0df44f032eb781556c15eeaf45000a3efe9630
WORKDIR /usr/src/ziggurat
RUN git init . \
    && git fetch --depth 1 https://github.com/zigpy/ziggurat "${ZIGGURAT_REF}" \
    && git checkout FETCH_HEAD

# The cargo registry and build cache persist across builds in BuildKit cache
# mounts, so a bumped pin only recompiles the crates that changed. The binary
# must be copied out: mount contents are not part of the image.
RUN --mount=type=cache,target=/usr/local/cargo/registry \
    --mount=type=cache,target=/usr/src/ziggurat/target \
    cargo build --release --bin ziggurat \
    && cp target/release/ziggurat /usr/bin/ziggurat

FROM ${BUILD_FROM}

COPY rootfs /
COPY --from=builder /usr/bin/ziggurat /usr/bin/ziggurat

HEALTHCHECK --interval=30s --timeout=5s --start-period=30s \
    CMD nc -z 127.0.0.1 9999 || exit 1

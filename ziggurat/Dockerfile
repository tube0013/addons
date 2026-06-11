ARG BUILD_FROM=ghcr.io/home-assistant/base:3.23

FROM rust:1-alpine AS builder

RUN apk add --no-cache git musl-dev

# The build context is the add-on directory, so the stack is built from a
# fresh fetch of the commit pinned below: bumping the pin is what invalidates
# this layer and rebuilds.
ARG ZIGGURAT_REF=16808ee52d075d8de3382edebc3fed5a66707b5c
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

COPY --from=builder /usr/bin/ziggurat /usr/bin/ziggurat
COPY rootfs /

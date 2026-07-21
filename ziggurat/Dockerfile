ARG BUILD_FROM=ghcr.io/home-assistant/base:3.23

FROM rust:1-alpine AS builder

RUN apk add --no-cache musl-dev

# Testing build: pulls ziggurat-server from zigpy/ziggurat#39 (tube0013's
# tcp-adaptor-support branch) instead of the published crates.io release. Pinned to a
# commit rather than --branch: Docker only re-runs this layer when an ARG it depends on
# actually changes, so pointing at a moving branch name would keep reusing a stale
# cached build even after new commits land. Bump ZIGGURAT_REV to the branch's latest
# commit to pick up updates. Revert to the `cargo install ziggurat-server --version`
# form before this add-on is used for anything other than testing that PR.
ARG ZIGGURAT_GIT=https://github.com/tube0013/ziggurat.git
ARG ZIGGURAT_REV=a73c330d6fd54afb276b66468973afb25d49e8e2

ENV CARGO_PROFILE_RELEASE_LTO=true \
    CARGO_PROFILE_RELEASE_CODEGEN_UNITS=1 \
    CARGO_PROFILE_RELEASE_PANIC=abort \
    CARGO_PROFILE_RELEASE_STRIP=true

RUN --mount=type=cache,target=/usr/local/cargo/registry \
    --mount=type=cache,target=/tmp/cargo-target \
    CARGO_TARGET_DIR=/tmp/cargo-target \
    cargo install ziggurat-server \
        --git "${ZIGGURAT_GIT}" \
        --rev "${ZIGGURAT_REV}" \
        --bin ziggurat \
        --locked \
        --root /usr/local

FROM ${BUILD_FROM}

COPY rootfs /
COPY --from=builder /usr/local/bin/ziggurat /usr/bin/ziggurat

HEALTHCHECK --interval=30s --timeout=5s --start-period=30s \
    CMD nc -z 127.0.0.1 9999 || exit 1

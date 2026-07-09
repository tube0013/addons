ARG BUILD_FROM=ghcr.io/home-assistant/base:3.23

FROM rust:1-alpine AS builder

RUN apk add --no-cache musl-dev

# Testing build: pulls ziggurat-server from a fork/branch (PR #39) instead of the
# published crates.io release. Uses tcp-adaptor-support-legacy-protocol, not
# tcp-adaptor-support directly: dev's tip (the ESP32 support merge, #29) changed
# handle_send_aps's wire protocol to fire-and-forget + async send_confirm/
# aps_ack_confirm notifications, which the currently-released zigpy-ziggurat doesn't
# understand yet (ZHA hangs on its startup permit(0) broadcast waiting on the old
# "transmitted" signal that no longer arrives). This branch has the same TCP-transport
# patch rebuilt on the pre-merge base so it stays compatible with zigpy-ziggurat until
# that's sorted out upstream. Revert to the `cargo install ziggurat-server --version`
# form before this add-on is used for anything other than testing that PR.
ARG ZIGGURAT_GIT=https://github.com/tube0013/ziggurat.git
ARG ZIGGURAT_BRANCH=tcp-adaptor-support-legacy-protocol

ENV CARGO_PROFILE_RELEASE_LTO=true \
    CARGO_PROFILE_RELEASE_CODEGEN_UNITS=1 \
    CARGO_PROFILE_RELEASE_PANIC=abort \
    CARGO_PROFILE_RELEASE_STRIP=true

RUN --mount=type=cache,target=/usr/local/cargo/registry \
    --mount=type=cache,target=/tmp/cargo-target \
    CARGO_TARGET_DIR=/tmp/cargo-target \
    cargo install ziggurat-server \
        --git "${ZIGGURAT_GIT}" \
        --branch "${ZIGGURAT_BRANCH}" \
        --bin ziggurat \
        --locked \
        --root /usr/local

FROM ${BUILD_FROM}

COPY rootfs /
COPY --from=builder /usr/local/bin/ziggurat /usr/bin/ziggurat

HEALTHCHECK --interval=30s --timeout=5s --start-period=30s \
    CMD nc -z 127.0.0.1 9999 || exit 1

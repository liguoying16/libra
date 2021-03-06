#while using circle we'll use circle's base image.
FROM alpine:3.12.0@sha256:a15790640a6690aa1730c38cf0a440e2aa44aaca9b0e8931a9f2b0d7cc90fd65 AS setup_ci_alpine

WORKDIR /libra
COPY rust-toolchain /libra/rust-toolchain
COPY cargo-toolchain /libra/cargo-toolchain
COPY scripts/dev_setup.sh /libra/scripts/dev_setup.sh

RUN apk add bash

#Batch mode and all operations tooling
RUN scripts/dev_setup.sh -o -b -p
ENV PATH "/root/.cargo/bin:/root/bin/:$PATH"

FROM setup_ci_alpine as tested_ci_alpine

#Compile a small rust tool?  But we already have in dev_setup (sccache/grcov)...?
#Test that all commands we need are installed and on the PATH
RUN [ -x "$(command -v hadolint)" ] \
    && [ -x "$(command -v vault)" ] \
    && [ -x "$(command -v terraform)" ] \
    && [ -x "$(command -v kubectl)" ] \
    && [ -x "$(command -v rustup)" ] \
    && [ -x "$(command -v cargo)" ] \
    && [ -x "$(command -v sccache)" ] \
    && [ -x "$(command -v grcov)" ] \
    && [ -x "$(command -v helm)" ] \
    && [ -x "$(command -v aws)" ] \
    && [ -x "$(rustup which cargo --toolchain `cat /libra/rust-toolchain`)" ] \
    && [ -x "$(rustup which cargo --toolchain `cat /libra/cargo-toolchain`)" ]

#should be a no-op, but since sccache failes to execute, sccache is rebuilt.
#RUN scripts/dev_setup.sh -b -p

FROM setup_ci_alpine as build_environment_alpine

FROM debian:stretch-slim as builder-base
RUN apt-get update && apt-get install -y curl

FROM builder-base as drone-cli-builder
ARG DRONE_CLI_VERSION=1.2.1
ARG DRONE_CLI_CHECKSUM=b74d0eeb878a8845787d6d1acaf8f2480d6a4acd2b7daaaaacbaafc443c8797e
RUN curl -L https://github.com/drone/drone-cli/releases/download/v${DRONE_CLI_VERSION}/drone_linux_amd64.tar.gz | tar zx
RUN echo "${DRONE_CLI_CHECKSUM} drone" | sha256sum -c -
RUN install -t /usr/local/bin drone

FROM builder-base as golangci-lint-builder
ARG GOLANGCI_LINT_VERSION=1.30.0
ARG GOLANGCI_LINT_CHECKSUM=42f000839df66ca6a4939dabc05ddee60b1a9af8671832c9d95b5984b09a7bb8
RUN curl -L https://github.com/golangci/golangci-lint/releases/download/v"${GOLANGCI_LINT_VERSION}"/golangci-lint-"${GOLANGCI_LINT_VERSION}"-linux-amd64.tar.gz |tar zx
RUN echo "${GOLANGCI_LINT_CHECKSUM} golangci-lint-${GOLANGCI_LINT_VERSION}-linux-amd64/golangci-lint" | sha256sum -c -
RUN install -t /usr/local/bin golangci-lint-"${GOLANGCI_LINT_VERSION}"-linux-amd64/golangci-lint

FROM golang:1.14-stretch as gox-builder
ARG GOX_VERSION=1.0.1
ARG GOX_CHECKSUM=87c9f4d0ac94106abbc1ebd99ca053789714e36e41f1e34ed81d42aedb1566a5
RUN curl -L https://github.com/mitchellh/gox/archive/v"${GOX_VERSION}".tar.gz | tar zx
RUN cd gox-"${GOX_VERSION}" && go build
RUN echo "${GOX_CHECKSUM} gox-${GOX_VERSION}/gox" | sha256sum -c -
RUN install -t /usr/local/bin gox-"${GOX_VERSION}"/gox

FROM golang:1.14-stretch
COPY --from=drone-cli-builder /usr/local/bin/drone /usr/local/bin
COPY --from=golangci-lint-builder /usr/local/bin/golangci-lint /usr/local/bin
COPY --from=gox-builder /usr/local/bin/gox /usr/local/bin

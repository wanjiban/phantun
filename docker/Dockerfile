#
# Dockerfile for phantun
#

#
# Build stage
#
FROM rust:latest AS builder

COPY . /phantun

RUN cd phantun \
  && cargo update \
  && cargo build --release \
  && ls -la target/release \
  && strip target/release/server target/release/client \
  && install target/release/server /usr/local/bin/phantun-server \
  && install target/release/client /usr/local/bin/phantun-client \
  && cd - \
  && rm -r phantun

#
# Runtime stage
#
FROM debian:latest
# FROM alpine:latest
# 安装必要的工具
RUN apt-get update && apt-get install -y procps iproute2 iptables \
    && rm -rf /var/lib/apt/lists/*
COPY --from=builder /usr/local/bin/phantun-server /usr/local/bin/
COPY --from=builder /usr/local/bin/phantun-client /usr/local/bin/
COPY docker/phantun.sh /usr/local/bin/

ENV USE_IPTABLES_NFT_BACKEND=0
ENV RUST_LOG=INFO

ENTRYPOINT ["phantun.sh"]
CMD ["phantun-server --local 1985 --remote 127.0.0.1:1984"]

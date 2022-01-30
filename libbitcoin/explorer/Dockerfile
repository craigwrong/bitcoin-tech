# syntax=docker/dockerfile:1
FROM alpine as downloader-verifier
WORKDIR /root
RUN wget https://github.com/libbitcoin/libbitcoin-explorer/releases/download/v3.2.0/bx-linux-x64-qrcode && \
    if [ "$(sha256sum bx-linux-x64-qrcode)" != "55f356f75c118df961e0442d0776f1d71e0b9e91936b1d9b96934f5eba167f0c  bx-linux-x64-qrcode" ]; then exit 1; fi

FROM ubuntu
COPY --from=downloader-verifier /root/bx-linux-x64-qrcode /usr/local/bin/bx
RUN chmod a+x /usr/local/bin/bx
ENTRYPOINT ["/usr/local/bin/bx"]

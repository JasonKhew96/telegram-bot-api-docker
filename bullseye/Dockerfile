# builder
FROM debian:bullseye as builder

WORKDIR /

RUN apt-get update && apt-get upgrade -y && apt-get -y install make git zlib1g-dev libssl-dev gperf cmake g++

RUN git clone --recursive https://github.com/tdlib/telegram-bot-api.git && \
    cd telegram-bot-api && \
    git checkout a3ba6536900e4fddb305790bd6481d59ca2284cf && \
    mkdir build && \
    cd build && \
    cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX:PATH=/usr/local .. && \
    cmake --build . --target install -j 2

# distroless
FROM gcr.io/distroless/cc-debian11

COPY --from=builder /usr/lib/x86_64-linux-gnu/libz.so /usr/lib/x86_64-linux-gnu/libz.so.1
COPY --from=builder /usr/lib/x86_64-linux-gnu/libcrypto.so /usr/lib/x86_64-linux-gnu/libcrypto.so.1
COPY --from=builder /usr/lib/x86_64-linux-gnu/libssl.so /usr/lib/x86_64-linux-gnu/libssl.so.1
COPY --from=builder /usr/local/bin/telegram-bot-api /usr/local/bin/telegram-bot-api

ENTRYPOINT [ "/usr/local/bin/telegram-bot-api" ]
CMD [ "--help" ]

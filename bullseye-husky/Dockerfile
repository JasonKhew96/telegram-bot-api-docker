# builder
FROM debian:bullseye as builder

WORKDIR /

RUN apt-get update && apt-get upgrade -y && apt-get -y install make git zlib1g-dev libssl-dev gperf cmake g++

RUN git clone --recursive https://github.com/c0re100/husky-bot-api.git && \
    cd husky-bot-api && \
    git checkout 6c440a4f93f111f3432e4fdf5fbb7f67af00622b && \
    mkdir build && \
    cd build && \
    cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX:PATH=/usr/local .. && \
    cmake --build . --target install -j 2 && \
    strip /usr/local/bin/telegram-bot-api

# distroless
FROM gcr.io/distroless/cc-debian11

COPY --from=builder /usr/lib/x86_64-linux-gnu/libz.so /usr/lib/x86_64-linux-gnu/libz.so.1
COPY --from=builder /usr/lib/x86_64-linux-gnu/libcrypto.so /usr/lib/x86_64-linux-gnu/libcrypto.so.1
COPY --from=builder /usr/lib/x86_64-linux-gnu/libssl.so /usr/lib/x86_64-linux-gnu/libssl.so.1
COPY --from=builder /usr/local/bin/telegram-bot-api /usr/local/bin/telegram-bot-api

ENTRYPOINT [ "/usr/local/bin/telegram-bot-api" ]
CMD [ "--help" ]

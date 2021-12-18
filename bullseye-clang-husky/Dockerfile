# builder
FROM debian:bullseye as builder

WORKDIR /

RUN apt-get install make git zlib1g-dev libssl-dev gperf cmake clang libc++-dev libc++abi-dev

RUN git clone --recursive https://github.com/c0re100/husky-bot-api.git && \
    cd telegram-bot-api && \
    git checkout 580d88ef44beb7bcf681ea313292d02f36cb6d5e && \
    mkdir build && \
    cd build && \
    CXXFLAGS="-stdlib=libc++" CC=/usr/bin/clang CXX=/usr/bin/clang++ cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX:PATH=/usr/local .. && \
    cmake --build . --target install -j 2

# distroless
FROM gcr.io/distroless/cc-debian11

COPY --from=builder /usr/lib/x86_64-linux-gnu/libz.so /usr/lib/x86_64-linux-gnu/libz.so
COPY --from=builder /usr/lib/x86_64-linux-gnu/libcrypto.so /usr/lib/x86_64-linux-gnu/libcrypto.so
COPY --from=builder /usr/lib/x86_64-linux-gnu/libssl.so /usr/lib/x86_64-linux-gnu/libssl.so
COPY --from=builder /usr/local/bin/telegram-bot-api /usr/local/bin/telegram-bot-api

ENTRYPOINT [ "/usr/local/bin/telegram-bot-api" ]
CMD [ "--help" ]
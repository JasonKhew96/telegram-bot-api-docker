# builder
FROM debian:bullseye as builder

WORKDIR /

RUN apt-get update && apt-get upgrade -y && apt-get -y install make git zlib1g-dev libssl-dev gperf cmake clang libc++-dev libc++abi-dev

RUN git clone --recursive https://github.com/c0re100/husky-bot-api.git && \
    cd husky-bot-api && \
    git checkout 6c440a4f93f111f3432e4fdf5fbb7f67af00622b && \
    mkdir build && \
    cd build && \
    CXXFLAGS="-stdlib=libc++" CC=/usr/bin/clang CXX=/usr/bin/clang++ cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX:PATH=/usr/local .. && \
    cmake --build . --target install -j 2 && \
    strip /usr/local/bin/telegram-bot-api

# distroless
FROM gcr.io/distroless/cc-debian11

# clang
COPY --from=builder /usr/lib/llvm-11/lib/libc++.so.1 /usr/lib/llvm-11/lib/libc++.so.1.0 /usr/lib/llvm-11/lib/
COPY --from=builder /usr/lib/x86_64-linux-gnu/libc++.so.1 /usr/lib/x86_64-linux-gnu/libc++.so.1.0 /usr/lib/x86_64-linux-gnu/
COPY --from=builder /usr/lib/llvm-11/lib/libc++abi.so.1 /usr/lib/llvm-11/lib/libc++abi.so.1.0 /usr/lib/llvm-11/lib/
COPY --from=builder /usr/lib/x86_64-linux-gnu/libc++abi.so.1 /usr/lib/x86_64-linux-gnu/libc++abi.so.1.0 /usr/lib/x86_64-linux-gnu/
COPY --from=builder /usr/lib/x86_64-linux-gnu/libatomic.so.1 /usr/lib/x86_64-linux-gnu/libatomic.so.1.2.0 /usr/lib/x86_64-linux-gnu/

COPY --from=builder /usr/lib/x86_64-linux-gnu/libz.so /usr/lib/x86_64-linux-gnu/libz.so.1
COPY --from=builder /usr/lib/x86_64-linux-gnu/libcrypto.so /usr/lib/x86_64-linux-gnu/libcrypto.so.1
COPY --from=builder /usr/lib/x86_64-linux-gnu/libssl.so /usr/lib/x86_64-linux-gnu/libssl.so.1
COPY --from=builder /usr/local/bin/telegram-bot-api /usr/local/bin/telegram-bot-api

ENTRYPOINT [ "/usr/local/bin/telegram-bot-api" ]
CMD [ "--help" ]

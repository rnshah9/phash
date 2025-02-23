FROM --platform=linux/amd64 ubuntu:22.04 as builder

RUN apt-get update
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y build-essential cmake libpng-dev libjpeg-dev libtiff-dev libavcodec-dev libavformat-dev libsamplerate0-dev libswscale-dev libsndfile-dev clang

COPY . /repo
WORKDIR /repo/build
RUN cmake .. -DPHASH_EXAMPLES=1 -DWITH_AUDIO_HASH=1 -DWITH_VIDEO_HASH=1 -DCMAKE_INSTALL_PREFIX=/install
RUN make -j8
RUN make install
RUN clang++ /repo/mayhem/fuzz_ph_bitcount.cpp -fsanitize=fuzzer,address -L /install/lib/ -lpHash -o /fuzz

FROM ubuntu:22.04 as package
RUN apt-get update
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y libpng-dev libsndfile-dev libsamplerate0-dev libavcodec-dev libavformat-dev libswscale-dev
COPY --from=builder /install /install
COPY --from=builder /fuzz /fuzz_ph_bitcount
ENV LD_LIBRARY_PATH=/install/lib

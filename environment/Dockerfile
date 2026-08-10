FROM ubuntu:24.04
ENV DEBIAN_FRONTEND=noninteractive

# Harbor/Terminus runtime tools (required even though not test deps).
# Placed early so the layer is cached across rebuilds.
RUN apt-get update && apt-get install -y --no-install-recommends tmux asciinema \
    && rm -rf /var/lib/apt/lists/* \
    && command -v tmux && command -v asciinema

RUN apt-get update && apt-get install -y --no-install-recommends build-essential cmake meson ninja-build pkg-config git ca-certificates wget autoconf automake libtool libboost-all-dev libssl-dev zlib1g-dev libgtest-dev libgmock-dev libfmt-dev nlohmann-json3-dev libspdlog-dev libprotobuf-dev protobuf-compiler libzstd-dev liblz4-dev libsnappy-dev libbz2-dev libreadline-dev libncurses-dev libicu-dev gcovr && rm -rf /var/lib/apt/lists/*
WORKDIR /app
COPY repo/ .
# Enable the test suite; disable docs (need Doxygen) and tools so configure produces the build system.
RUN cmake -B build -DCMAKE_BUILD_TYPE=Release -DURIPARSER_BUILD_TESTS=ON -DURIPARSER_BUILD_DOCS=OFF -DURIPARSER_BUILD_TOOLS=OFF 2>&1 | tee /tmp/cmake_configure.log
CMD ["sh", "-c", "cmake --build build && ctest --test-dir build --output-on-failure"]
RUN mkdir -p /installed-agent
RUN command -v patch >/dev/null 2>&1 || ( (apt-get update && apt-get install -y --no-install-recommends patch && rm -rf /var/lib/apt/lists/*) 2>/dev/null || apk add --no-cache patch 2>/dev/null || yum install -y patch 2>/dev/null || true )

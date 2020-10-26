FROM ubuntu:focal

# General dependencies
RUN apt-get -qq update
RUN apt-get install -qq -y llvm-10 clang clang-10 libclang-10-dev cmake wget curl zip
RUN apt-get install -qq -y git

# Install lsif-clang
ENV LSIF_CLANG_REV=366659086a6ba2968c837179c17ff978cf3efc97
RUN git clone https://github.com/sourcegraph/lsif-clang.git
WORKDIR /lsif-clang
RUN git checkout $LSIF_CLANG_REV
RUN cmake -B build
RUN make -C build install
WORKDIR /

# src-cli
ENV SRC_ACCESS_TOKEN=""
ENV SRC_ENDPOINT="https://sourcegraph.com"
RUN curl -L https://sourcegraph.com/.api/src-cli/src_linux_amd64 -o /usr/local/bin/src
RUN chmod +x /usr/local/bin/src

# Install Bear (not required, but keeping for now just cuz)
RUN apt-get install -y bear

# Install Bazelisk
RUN wget https://github.com/bazelbuild/bazelisk/releases/download/v1.7.4/bazelisk-linux-amd64 -O /usr/local/bin/bazelisk
RUN chmod +x /usr/local/bin/bazelisk
RUN ln -s /usr/local/bin/bazelisk /usr/local/bin/bazel

# Install bazel-compilation-database
RUN git clone --depth=10 https://github.com/grailbio/bazel-compilation-database.git /bazel-compilation-database


# Dependencies
RUN apt-get install -y g++

ENV PROJECT_REV=""
RUN git clone --depth=10 https://github.com/google/tcmalloc.git
WORKDIR /tcmalloc
RUN git checkout $PROJECT_REV
RUN /bazel-compilation-database/generate.sh
# bazelisk build //tcmalloc/...
RUN lsif-clang compile_commands.json

# Upload
ENV SRC_ACCESS_TOKEN=""
ENV SRC_ENDPOINT="https://sourcegraph.com"
ENTRYPOINT src lsif upload

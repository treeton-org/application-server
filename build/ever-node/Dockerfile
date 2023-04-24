###########
## Build ##
###########
FROM ubuntu:18.04 as build

# Arguments
ARG EVER_NODE_REPOSITORY=https://github.com/tonlabs/ever-node
ARG EVER_NODE_COMMIT_ID=0.52.2
ARG EVER_NODE_TOOLS_REPOSITORY=https://github.com/tonlabs/ever-node-tools
ARG EVER_NODE_TOOLS_COMMIT_ID=master
ARG FEATURES="external_db,metrics"

# Environments
ENV TIME_ZONE=Europe/Moscow
ENV EVER_NODE_PATH=/ever-node
ENV EVER_NODE_TOOLS_PATH=/ever-node-tools
ENV ZSTD_LIB_DIR=/usr/lib/x86_64-linux-gnu
ENV PATH="/root/.cargo/bin:${PATH}"

# Install GIT, curl and gnupg2
RUN apt-get update
RUN apt-get install -y git curl gnupg2

# Clone ever-node
RUN git clone --recursive ${EVER_NODE_REPOSITORY} ${EVER_NODE_PATH}
RUN git -C ${EVER_NODE_PATH} checkout ${EVER_NODE_COMMIT_ID}

# Clone ever-node-tools
RUN git clone --recursive ${EVER_NODE_TOOLS_REPOSITORY} ${EVER_NODE_TOOLS_PATH}
RUN git -C ${EVER_NODE_TOOLS_PATH} checkout ${EVER_NODE_TOOLS_COMMIT_ID}

# Set time zone
RUN ln -snf /usr/share/zoneinfo/${TIME_ZONE} /etc/localtime
RUN echo ${TIME_ZONE} > /etc/timezone

# Add kafka to sources.list
RUN curl https://packages.confluent.io/deb/7.3/archive.key | apt-key add
RUN echo "deb [arch=amd64] https://packages.confluent.io/deb/7.3 stable main" >> /etc/apt/sources.list
RUN echo "deb [arch=amd64] http://archive.ubuntu.com/ubuntu/ focal main restricted universe multiverse" >> /etc/apt/sources.list

# Install packages
RUN apt-get update
RUN apt-get install -y \
    gpg \
    tar \
    cmake \
    build-essential \
    pkg-config \
    libssl-dev \
    libtool \
    m4 \
    automake \
    clang \
    git \
    libzstd-dev \
    libgoogle-perftools-dev \
    librdkafka-dev

# Install Rust
RUN curl --proto '=https' --tlsv1.3 https://sh.rustup.rs -sSf | sh -s -- -y

# Build ever-node
WORKDIR ${EVER_NODE_PATH}
RUN cargo update && cargo build --release --features "${FEATURES}"

# Build ever-node-tools
WORKDIR ${EVER_NODE_TOOLS_PATH}
RUN cargo update && cargo build --release


##########
## Pack ##
##########
FROM ubuntu:18.04 as final

# Environments
ENV TIME_ZONE=Europe/Moscow
ENV ZSTD_LIB_DIR=/usr/lib/x86_64-linux-gnu

# Install curl and gnupg2
RUN apt-get update
RUN apt-get install -y curl gnupg2

# Set time zone
RUN ln -snf /usr/share/zoneinfo/${TIME_ZONE} /etc/localtime
RUN echo ${TIME_ZONE} > /etc/timezone

# Add kafka to sources.list
RUN curl https://packages.confluent.io/deb/7.3/archive.key | apt-key add
RUN echo "deb [arch=amd64] https://packages.confluent.io/deb/7.3 stable main" >> /etc/apt/sources.list
RUN echo "deb [arch=amd64] http://archive.ubuntu.com/ubuntu/ focal main restricted universe multiverse" >> /etc/apt/sources.list

# Install packages
RUN apt-get update
RUN apt-get install -y \
    librdkafka1 \
    build-essential \
    cmake \
    cron \
    git \
    gdb \
    gpg \
    jq \
    tar \
    tcpdump \
    netcat \
    python3 \
    python3-pip \
    wget \
    libzstd-dev \
    libgoogle-perftools-dev

# Install Rust
RUN curl --proto '=https' --tlsv1.3 https://sh.rustup.rs -sSf | sh -s -- -y

# Copy from build
COPY --from=build /ever-node/target/release/ton_node /ever-node/
COPY --from=build /ever-node-tools/target/release/console /ever-node/tools/
COPY --from=build /ever-node-tools/target/release/keygen /ever-node/tools/

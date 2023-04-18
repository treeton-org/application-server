# Arguments
# Example:
# docker build \
# --build-arg EVER_NODE_COMMIT_ID=befb1ba4d91733ee2c14fa3cf4ebe8047b279e92 \
# --build-arg EVER_NODE_TOOLS_COMMIT_ID=master \
# -t kok/kek:latest .
ARG EVER_NODE_COMMIT_ID=befb1ba4d91733ee2c14fa3cf4ebe8047b279e92
ARG EVER_NODE_TOOLS_COMMIT_ID=master

FROM ubuntu:22.04

# Environments
ENV EVER_NODE_PATH=/ever-node
ENV EVER_NODE_TOOLS_PATH=/ever-node-tools
ENV TIME_ZONE=Europe/Moscow
ENV PATH="/root/.cargo/bin:${PATH}"

# Clone ever-node
RUN apt-get update
RUN apt-get install -y git
RUN git clone --recursive "https://github.com/tonlabs/ever-node" ${EVER_NODE_PATH}
RUN git -C ${EVER_NODE_PATH} checkout ${EVER_NODE_COMMIT_ID}

# Clone ever-node
RUN apt-get update
RUN apt-get install -y git
RUN git clone --recursive "https://github.com/tonlabs/ever-node-tools" ${EVER_NODE_TOOLS_PATH}
RUN git -C ${EVER_NODE_TOOLS_PATH} checkout ${EVER_NODE_TOOLS_COMMIT_ID}

# Set time zone
RUN ln -snf /usr/share/zoneinfo/${TIME_ZONE} /etc/localtime
RUN echo ${TIME_ZONE} > /etc/timezone
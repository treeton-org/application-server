FROM node:18-buster as X

# Arguments
ARG TON_Q_SERVER_REPOSITORY="https://github.com/tonlabs/ton-q-server"
ARG TON_Q_SERVER_COMMIT=0.55.1

# Environments
ENV TON_Q_SERVER_PATH=/q-server

# Clone ton-q-server
RUN git clone --recursive ${TON_Q_SERVER_REPOSITORY} ${TON_Q_SERVER_PATH}
RUN git -C ${TON_Q_SERVER_PATH} checkout ${TON_Q_SERVER_COMMIT}

# Installation
WORKDIR ${TON_Q_SERVER_PATH}
RUN npm install && npm run tsc
ENTRYPOINT node index.js

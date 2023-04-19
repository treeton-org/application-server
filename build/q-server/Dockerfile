FROM node:18-buster as X

# Arguments
ARG TON_Q_SERVER_GITHUB_COMMIT_ID=0.55.1

# Environments
ENV TON_Q_SERVER_PATH=/q-server

# Clone ton-q-server
RUN git clone --recursive "https://github.com/tonlabs/ton-q-server" ${TON_Q_SERVER_PATH}
RUN git -C ${TON_Q_SERVER_PATH} checkout ${TON_Q_SERVER_COMMIT_ID}

# Installation
WORKDIR ${TON_Q_SERVER_PATH}
RUN npm install && npm run tsc
ENTRYPOINT node index.js

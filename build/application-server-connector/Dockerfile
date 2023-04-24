FROM confluentinc/cp-kafka-connect:7.3.3

# Install packages
USER root
RUN yum -y update
RUN yum -y install jq findutils

# Copy bash scripts
COPY check.sh create_topics.sh create_connectors.sh /work/
COPY connect-cli /usr/bin/connect-cli
COPY /connectors/ /work/connectors/

# Entrypoint
COPY entrypoint /entrypoint
ENTRYPOINT entrypoint

FROM confluentinc/cp-kafka-connect:7.3.3

# Install packages
USER root
RUN yum -y update
RUN yum -y install jq findutils curl

# Copy check script
COPY check /init/

# Create templates directory
RUN mkdir -p /init/templates
RUN mkdir -p /init/connectors

# Entrypoint
COPY entrypoint /entrypoint
ENTRYPOINT /entrypoint

FROM confluentinc/cp-kafka-connect:7.3.3

# Install packages
USER root
RUN yum -y update
RUN yum -y install jq findutils

# Copy check script
COPY check /init/

# Entrypoint
COPY entrypoint /entrypoint
ENTRYPOINT entrypoint

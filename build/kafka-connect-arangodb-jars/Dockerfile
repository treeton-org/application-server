###########
## Build ##
###########
FROM maven:3.9.1 as build

# Arguments
ARG KAFKA_CONNECT_ARANGODB_REPOSITORY=https://github.com/tonlabs/kafka-connect-arangodb
ARG KAFKA_CONNECT_ARANGODB_COMMIT=2eef79f827b514111f41a100fde27d0209bb8367
ARG JACKSON_CORE_REPOSITORY=https://github.com/FasterXML/jackson-core
ARG JACKSON_CORE_COMMIT=jackson-core-2.14.2
ARG SLF4J_REPOSITORY=https://github.com/qos-ch/slf4j
ARG SLF4J_COMMIT=v_2.0.7
ARG VELOCYPACK_REPOSITORY=https://github.com/treeton-org/java-velocypack
ARG VELOCYPACK_COMMIT=v3.0.1

# Environments
ENV KAFKA_CONNECT_ARANGODB_PATH=/kafka-connect-arangodb
ENV JACKSON_CORE_PATH=/jackson-core
ENV SLF4J_PATH=/slf4j
ENV VELOCYPACK_PATH=/velocypack

## Build kafka-connect-arangodb
RUN git clone --recursive ${KAFKA_CONNECT_ARANGODB_REPOSITORY} ${KAFKA_CONNECT_ARANGODB_PATH}
RUN git -C ${KAFKA_CONNECT_ARANGODB_PATH} checkout ${KAFKA_CONNECT_ARANGODB_COMMIT}
WORKDIR ${KAFKA_CONNECT_ARANGODB_PATH}
RUN mvn package

# jackson-core
RUN git clone --recursive ${JACKSON_CORE_REPOSITORY} ${JACKSON_CORE_PATH}
RUN git -C ${JACKSON_CORE_PATH} checkout ${JACKSON_CORE_COMMIT}
WORKDIR ${JACKSON_CORE_PATH}
RUN mvn clean install -DskipTests=true

# slf4j-api
RUN git clone --recursive ${SLF4J_REPOSITORY} ${SLF4J_PATH}
RUN git -C ${SLF4J_PATH} checkout ${SLF4J_COMMIT}
WORKDIR ${SLF4J_PATH}
RUN mvn clean install -pl slf4j-api -am

# velocypack
RUN git clone --recursive ${VELOCYPACK_REPOSITORY} ${VELOCYPACK_PATH}
RUN git -C ${VELOCYPACK_PATH} checkout ${VELOCYPACK_COMMIT}
WORKDIR ${VELOCYPACK_PATH}
RUN mvn clean install -DskipTests=true -Dgpg.skip=true -Dmaven.javadoc.skip=true -B


##########
## Copy ##
##########
FROM ubuntu:22.04 as final

# Copy from build
COPY --from=build /kafka-connect-arangodb/target/kafka-connect-arangodb-*.jar /source/
COPY --from=build /jackson-core/target/jackson-core-*.jar /source/
COPY --from=build /slf4j/slf4j-api/target/slf4j-api-*.jar /source/
COPY --from=build /velocypack/target/*.jar /source/

# Entrypoint
COPY entrypoint /
RUN chmod a+x /entrypoint
ENTRYPOINT /entrypoint

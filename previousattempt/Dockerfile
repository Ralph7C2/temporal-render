FROM temporalio/base-server:latest
FROM temporalio/tctl:latest
FROM temporalio/admin-tools:latest
# FROM temporalio/base-builder:1.5.0 # useless - doesnt seem to have temporal-server 

# forward env vars from docker env to entrypoint.sh
ARG DB_PORT
ARG DBNAME
ARG POSTGRES_USER
ARG POSTGRES_PWD
ARG POSTGRES_SEEDS
ARG DYNAMIC_CONFIG_FILE_PATH
ARG LOG_LEVEL
ENV DB_PORT=$DB_PORT
ENV DBNAME=$DBNAME
ENV POSTGRES_USER=$POSTGRES_USER
ENV POSTGRES_PWD=$POSTGRES_PWD
ENV POSTGRES_SEEDS=$POSTGRES_SEEDS
ENV DYNAMIC_CONFIG_FILE_PATH=$DYNAMIC_CONFIG_FILE_PATH
ENV LOG_LEVEL=$LOG_LEVEL

# hardcorded env vars
ENV DB=postgresql
ENV TEMPORAL_HOME=.

# # https://github.com/arniebilloo/test/blob/8e52f7b1647c7b0f55b67f432358936f863ae1a4/multiple.Dockerfile
# WORKDIR /etc/temporal
# ENV TEMPORAL_HOME /etc/temporal
# ENV SERVICES "history:matching:frontend:worker"
# EXPOSE 6933 6934 6935 6939 7233 7234 7235 7239

# RUN addgroup -g 1000 temporal
# RUN adduser -u 1000 -G temporal -D temporal
# RUN mkdir /etc/temporal/config
# RUN chown -R temporal:temporal /etc/temporal/config

# actually do work
COPY custom-auto-setup.sh .
COPY start-temporal.sh .
COPY entrypoint.sh .
COPY dl_to_stdout.sh .
#### DOESNT WORK
# # from base-builder - need it for temporal-server binary
# COPY --from=temporalio/base-server:latest /usr/local/bin/temporal-server /usr/local/bin

# Install Temporal server
# https://github.com/mediacloud/backend/blob/26c458ff3336d71b766d51a57edca6d50364a52e/apps/temporal-server/Dockerfile
RUN \
    # Keep version that's being used in sync with temporal-postgresql
    mkdir -p /var/tmp/temporal/ && \
    ./dl_to_stdout.sh "https://github.com/temporalio/temporal/releases/download/v1.14.1/temporal_1.14.1_linux_amd64.tar.gz" | \
        tar -zx -C /var/tmp/temporal/ && \
    mv /var/tmp/temporal/temporal-server /var/tmp/temporal/tctl /usr/bin/ && \
    cd / && \
    rm -rf /var/tmp/temporal/ && \
    true

RUN ./entrypoint.sh
CMD ["entrypoint.sh"]

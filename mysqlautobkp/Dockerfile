FROM alpine:latest
ARG FREQUENCY

COPY ./scripts/* /etc/periodic/$FREQUENCY

RUN apk update && \
    apk upgrade && \
    apk add --no-cache mysql-client && \
    chmod a+x /etc/periodic/$FREQUENCY/*

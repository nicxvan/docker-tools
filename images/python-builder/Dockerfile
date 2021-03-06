FROM docker:18.06.1
ARG BUILD_DATE
ARG VCS_REF
LABEL org.label-schema.build-date=$BUILD_DATE \
    org.label-schema.license=Apache-2.0 \
    org.label-schema.name=python-builder \
    org.label-schema.vcs-ref=$VCS_REF \
    org.label-schema.vcs-url=https://github.com/instantlinux/docker-tools

ENV BUILD_DIR=/build \
    BUILD_USER=build \
    TZ=UTC

ARG COMPOSE_VERSION=1.22.0
ARG PYTHON_VERSION=3.6.6-r0
ARG PYCRYPTOGRAPHY_VERSION=2.3.1-r0
ARG _DOCKER_DOWNLOADS=https://github.com/docker/compose/releases/download
ARG _COMPOSE_URL=${_DOCKER_DOWNLOADS}/${COMPOSE_VERSION}/docker-compose-Linux-x86_64
ARG DOCKER_GID=485
ARG BUILD_UID=1001
ARG COMPOSE_SHA=f679a24b93f291c3bffaff340467494f388c0c251649d640e661d509db9d57e9

COPY requirements.txt /root/

RUN addgroup -g $DOCKER_GID docker && \
    adduser -D -h $BUILD_DIR -u $BUILD_UID -G docker \
      -s /bin/bash $BUILD_USER && \
    echo '@edge http://dl-cdn.alpinelinux.org/alpine/edge/main' \
      >>/etc/apk/repositories && \
    apk add --update --no-cache \
      bash curl gcc git gzip jq make musl-dev openssh-client \
      python3==$PYTHON_VERSION python3-dev \
      py3-cryptography@edge==$PYCRYPTOGRAPHY_VERSION py3-virtualenv tar \
      tzdata uwsgi-python wget && \
    cp /usr/share/zoneinfo/$TZ /etc/localtime && \
    echo $TZ >/etc/timezone && \
    curl -sLo /usr/local/bin/docker-compose ${_COMPOSE_URL} && \
    echo "$COMPOSE_SHA  /usr/local/bin/docker-compose" | sha256sum -c && \
    chmod +x /usr/local/bin/docker-compose && \
    pip3 install --upgrade pip && \
    pip install -r /root/requirements.txt && \
    chown $BUILD_USER /etc/localtime /etc/timezone && \
    rm -f /var/cache/apk/*

WORKDIR $BUILD_DIR
VOLUME $BUILD_DIR
COPY entrypoint.sh /usr/local/bin/
RUN chmod 755 /usr/local/bin/entrypoint.sh

USER $BUILD_USER

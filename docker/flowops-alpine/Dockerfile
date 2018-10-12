FROM python:3.7-alpine as flowops-base

LABEL maintainer="Kevin Doran <kdoran@apache.org>"
LABEL site="https://github.com/kevdoran/flowops"

# Default to UTF-8 file.encoding
ENV LANG C.UTF-8

# Common APK dependencies
RUN apk add --no-cache bash curl libxslt-dev jq openssl

# Java 8 (for NiFi CLI). See openjdk:8-jre-alpine.
RUN { \
        echo '#!/bin/sh'; \
        echo 'set -e'; \
        echo; \
        echo 'dirname "$(dirname "$(readlink -f "$(which javac || which java)")")"'; \
    } > /usr/local/bin/docker-java-home \
    && chmod +x /usr/local/bin/docker-java-home
ENV JAVA_HOME /usr/lib/jvm/java-1.8-openjdk/jre
ENV PATH $PATH:/usr/lib/jvm/java-1.8-openjdk/jre/bin:/usr/lib/jvm/java-1.8-openjdk/bin
ENV JAVA_VERSION 8.171.11-r0
RUN set -x \
    && apk add --no-cache \
        openjdk8-jre="$JAVA_VERSION" \
    && [ "$JAVA_HOME" = "$(docker-java-home)" ]


# NiFi Toolkit and CLI
ARG NIFI_TOOLKIT_VERSION=1.7.1
ARG MIRROR=https://archive.apache.org/dist
ENV NIFI_TOOLKIT_HOME=/opt/nifi-toolkit/nifi-toolkit-${NIFI_TOOLKIT_VERSION}
ENV NIFI_TOOLKIT_BINARY_URL=${MIRROR}/nifi/${NIFI_TOOLKIT_VERSION}/nifi-toolkit-${NIFI_TOOLKIT_VERSION}-bin.tar.gz
ENV PATH $PATH:${NIFI_TOOLKIT_HOME}/bin
# Download, validate, and expand Apache NiFi Toolkit binary.
RUN apk add --no-cache bash curl jq openssl
RUN mkdir -p /opt/nifi-toolkit \
    && curl -fSL ${NIFI_TOOLKIT_BINARY_URL} -o nifi-toolkit-${NIFI_TOOLKIT_VERSION}-bin.tar.gz \
    && echo "$(curl ${NIFI_TOOLKIT_BINARY_URL}.sha256) *nifi-toolkit-${NIFI_TOOLKIT_VERSION}-bin.tar.gz" | sha256sum -c - \
    && tar -xvzf nifi-toolkit-${NIFI_TOOLKIT_VERSION}-bin.tar.gz -C /opt/nifi-toolkit \
    && rm nifi-toolkit-${NIFI_TOOLKIT_VERSION}-bin.tar.gz

# Default port for TLS Toolkit CA Server (disabled for now as usage does not involve CA server)
# EXPOSE 8443

# NiPyAPI
FROM python:3.7-alpine as nipyapi-builder
RUN mkdir /workdir
WORKDIR /workdir
RUN mkdir /install
ENV PYTHONPATH /install/lib/python3.7/site-packages
RUN apk update && apk upgrade 
RUN apk add gcc build-base linux-headers ca-certificates python3-dev libffi-dev libressl-dev libxslt-dev git
RUN wget https://raw.githubusercontent.com/Chaffelson/nipyapi/master/requirements.txt \
    && cat requirements.txt | grep -v docker | grep -v requests > minimal_requirements.txt
RUN pip install --install-option="--prefix=/install" -r minimal_requirements.txt
RUN pip install --upgrade --install-option="--prefix=/install" git+https://github.com/Chaffelson/nipyapi.git@master#egg=nipyapi
FROM flowops-base
COPY --from=nipyapi-builder /install /usr/local

ENTRYPOINT ["/bin/bash"]

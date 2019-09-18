FROM alpine:3.8

LABEL maintainer="OmiseGO Team <omg@omise.co>"
LABEL description="Deploy image for OmiseGO elixir-omg"

RUN set -xe \
 && apk add --update --no-cache --virtual .utils \
        bash \
        ca-certificates \
        curl \
        git \
        gnupg \
        libressl \
        libressl-dev \
        openssh-client

ARG CLOUD_SDK_VERSION=237.0.0
ARG CLOUD_SDK_DOWNLOAD_SHA256="10e08362e750581c6ff3866314ed439a1bb544755657b204bf719f68e03b4ba3"

RUN set -xe \
 && apk add --update --no-cache --virtual .gcloud-runtime \
        python \
        py-crcmod \
        libc6-compat \
 && CLOUD_SDK_DOWNLOAD_URL="https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-sdk-${CLOUD_SDK_VERSION}-linux-x86_64.tar.gz" \
 && curl -fsL -o cloud-sdk-src.tar.gz "${CLOUD_SDK_DOWNLOAD_URL}" \
 && echo "${CLOUD_SDK_DOWNLOAD_SHA256}  cloud-sdk-src.tar.gz" |sha256sum -c - \
 && tar xzf cloud-sdk-src.tar.gz \
 && tar -xzC /usr/local --strip-components=1 -f cloud-sdk-src.tar.gz \
 && rm cloud-sdk-src.tar.gz \
 && ln -s /lib /lib64 \
 && gcloud components install kubectl \
 && gcloud config set core/disable_usage_reporting true \
 && gcloud config set component_manager/disable_update_check true \
 && gcloud config set metrics/environment github_docker_image \
 && gcloud --version

ARG KAPITAN_VERSION="0.22.3"

RUN apk add --update --no-cache --virtual .kapitan-runtime \
        libffi \
        libstdc++ \
        python3 \
 && apk add --update --no-cache --virtual .kapitan-build \
        build-base \
        libffi-dev \
        python3-dev \
 && python3 -m ensurepip \
 && pip3 install --upgrade pip \
 && pip3 install kapitan==${KAPITAN_VERSION} \
 && apk del .kapitan-build \
 && kapitan --version

ARG HELM_VERSION="2.13.0"
ARG HELM_DOWNLOAD_SHA256="15eca6ad225a8279de80c7ced42305e24bc5ac60bb7d96f2d2fa4af86e02c794"

RUN set -xe \
 && HELM_DOWNLOAD_URL="https://storage.googleapis.com/kubernetes-helm/helm-v${HELM_VERSION}-linux-amd64.tar.gz" \
 && curl -fsL -o helm-src.tar.gz "${HELM_DOWNLOAD_URL}" \
 && echo "${HELM_DOWNLOAD_SHA256}  helm-src.tar.gz" |sha256sum -c - \
 && tar xzf helm-src.tar.gz \
 && mkdir -p /usr/src/helm \
 && tar -xzC /usr/src/helm --strip-components=1 -f helm-src.tar.gz \
 && install -m0755 /usr/src/helm/helm /usr/local/bin/helm \
 && install -m0755 /usr/src/helm/tiller /usr/local/bin/tiller \
 && rm -rf /usr/src/helm \
 && rm helm-src.tar.gz \
 && helm version --client

RUN cd /tmp && \
    git clone https://github.com/facebook/rocksdb.git && \
    cd rocksdb && \
    git checkout v6.2.2 && \
    make shared_lib && \
    mkdir -p /usr/local/rocksdb/lib && \
    mkdir /usr/local/rocksdb/include && \
    cp librocksdb.so* /usr/local/rocksdb/lib && \
    cp /usr/local/rocksdb/lib/librocksdb.so* /usr/lib/ && \
    cp -r include /usr/local/rocksdb/ && \
    cp -r include/* /usr/include/ && \
    rm -R /tmp/rocksdb/
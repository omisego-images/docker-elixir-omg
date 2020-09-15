FROM alpine:3.12

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
        python2 \
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

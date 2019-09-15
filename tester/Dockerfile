FROM alpine:3.8

LABEL maintainer="OmiseGO Team <omg@omise.co>"
LABEL description="Builder image for OmiseGO elixir-omg"

ARG USER=elixir-omg
ARG GROUP=elixir-omg
ARG UID=10000
ARG GID=10000

COPY --from=omisegoimages/elixir-omg-builder-erlang /usr/local/otp /usr/local/otp
COPY --from=omisegoimages/elixir-omg-builder-erlang /usr/local/rebar3 /usr/local/rebar3
COPY --from=omisegoimages/elixir-omg-builder-elixir /usr/local/elixir /usr/local/elixir

ENV GETH_VERSION="v1.8.21"
ENV GETHPATH="/go-ethereum"
ENV LD_LIBRARY_PATH=/usr/local/otp/lib:$LD_LIBRARY_PATH
ENV LD_LIBRARY_PATH=/usr/local/elixir/lib:$LD_LIBRARY_PATH
ENV PATH=/usr/local/otp/bin:$PATH
ENV PATH=/usr/local/rebar3/bin:$PATH
ENV PATH=/usr/local/elixir/bin:$PATH
ENV PATH=/usr/local/go/bin:$PATH

ENV HOME /home/${USER}
ENV LANG=en_US.UTF-8
ENV LC_ALL=en_US.UTF-8

RUN wget https://github.com/ethereum/solidity/releases/download/v0.4.25/solc-static-linux \
  && chmod +x solc-static-linux \
  && mv solc-static-linux solc  \
  && install -m0755 solc /usr/local/bin/solc

RUN set -xe \
 && apk add --update --no-cache --virtual .utils \
        rocksdb \
        linux-headers \
        bash \
        build-base \
        ca-certificates \
        cmake \
        curl \
        docker \
        expect \
        findutils \
        git \
        gnupg \
        libressl \
        openssh \
 && apk add --no-cache --virtual .libsecp256k1-build \
        autoconf \
        automake \
        gmp \
        gmp-dev \
        libtool \
 && rm -rf "$HOME/.cache" \
 && RUN_DEPS="$( \
        find /usr/local -type f -executable -not \( -name '*tkinter*' \) -exec \
            scanelf --needed --nobanner --format '%n#p' '{}' ';' \
            | tr ',' '\n' \
            | sort -u \
            | awk '! /^$/ && system("test -z $(find /usr/local -iname " $1 ")") == 0 { print "so:" $1 }' \
    )" \
 && apk add --update --no-cache --virtual .run-deps \
        $RUN_DEPS \
        ca-certificates \
        libressl-dev \
        lksctp-tools \
 && addgroup -g ${GID} ${GROUP} \
 && adduser -D -h ${HOME} -u ${UID} -G ${GROUP} -H ${USER} \
 && mkdir -p "${HOME}" \
 && chown -R "${UID}:${GID}" "${HOME}"

# Glibc for 3.8 alpine
# https://hub.docker.com/r/frolvlad/alpine-glibc/dockerfile
# Here we install GNU libc (aka glibc) and set C.UTF-8 locale as default.
RUN ALPINE_GLIBC_BASE_URL="https://github.com/sgerrand/alpine-pkg-glibc/releases/download" && \
    ALPINE_GLIBC_PACKAGE_VERSION="2.29-r0" && \
    ALPINE_GLIBC_BASE_PACKAGE_FILENAME="glibc-$ALPINE_GLIBC_PACKAGE_VERSION.apk" && \
    ALPINE_GLIBC_BIN_PACKAGE_FILENAME="glibc-bin-$ALPINE_GLIBC_PACKAGE_VERSION.apk" && \
    ALPINE_GLIBC_I18N_PACKAGE_FILENAME="glibc-i18n-$ALPINE_GLIBC_PACKAGE_VERSION.apk" && \
    apk add --no-cache --virtual=.build-dependencies wget ca-certificates && \
    echo \
        "-----BEGIN PUBLIC KEY-----\
        MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEApZ2u1KJKUu/fW4A25y9m\
        y70AGEa/J3Wi5ibNVGNn1gT1r0VfgeWd0pUybS4UmcHdiNzxJPgoWQhV2SSW1JYu\
        tOqKZF5QSN6X937PTUpNBjUvLtTQ1ve1fp39uf/lEXPpFpOPL88LKnDBgbh7wkCp\
        m2KzLVGChf83MS0ShL6G9EQIAUxLm99VpgRjwqTQ/KfzGtpke1wqws4au0Ab4qPY\
        KXvMLSPLUp7cfulWvhmZSegr5AdhNw5KNizPqCJT8ZrGvgHypXyiFvvAH5YRtSsc\
        Zvo9GI2e2MaZyo9/lvb+LbLEJZKEQckqRj4P26gmASrZEPStwc+yqy1ShHLA0j6m\
        1QIDAQAB\
        -----END PUBLIC KEY-----" | sed 's/   */\n/g' > "/etc/apk/keys/sgerrand.rsa.pub" && \
    wget \
        "$ALPINE_GLIBC_BASE_URL/$ALPINE_GLIBC_PACKAGE_VERSION/$ALPINE_GLIBC_BASE_PACKAGE_FILENAME" \
        "$ALPINE_GLIBC_BASE_URL/$ALPINE_GLIBC_PACKAGE_VERSION/$ALPINE_GLIBC_BIN_PACKAGE_FILENAME" \
        "$ALPINE_GLIBC_BASE_URL/$ALPINE_GLIBC_PACKAGE_VERSION/$ALPINE_GLIBC_I18N_PACKAGE_FILENAME" && \
    apk add --no-cache \
        "$ALPINE_GLIBC_BASE_PACKAGE_FILENAME" \
        "$ALPINE_GLIBC_BIN_PACKAGE_FILENAME" \
        "$ALPINE_GLIBC_I18N_PACKAGE_FILENAME" && \
    \
    rm "/etc/apk/keys/sgerrand.rsa.pub" && \
    /usr/glibc-compat/bin/localedef --force --inputfile POSIX --charmap UTF-8 "$LANG" || true && \
    echo "export LANG=$LANG" > /etc/profile.d/locale.sh && \
    \
    apk del glibc-i18n && \
    \
    apk del .build-dependencies && \
    rm \
        "$ALPINE_GLIBC_BASE_PACKAGE_FILENAME" \
        "$ALPINE_GLIBC_BIN_PACKAGE_FILENAME" \
        "$ALPINE_GLIBC_I18N_PACKAGE_FILENAME"

### https://hub.docker.com/r/nimmis/alpine-golang/dockerfile/
RUN apk update && apk upgrade && \
    # Make info file about this build
    mkdir -p /etc/BUILDS/ && \
    printf "Build of nimmis/alpine-golang:1.9.2, date: %s\n"  `date -u +"%Y-%m-%dT%H:%M:%SZ"` > /etc/BUILDS/alpine-golang && \
    # add extra package for installation
    apk add curl && \
    # download and upack golang
    curl https://storage.googleapis.com/golang/go1.9.2.linux-amd64.tar.gz | tar xzf - -C / && \
    mv /go /goroot && \
    # remove extra packages
    apk del curl && \
    # remove cached info
    rm -rf /var/cache/apk/*

ENV GOROOT=/goroot \
    GOPATH=/gopath \
    GOBIN=/gopath/bin \
    PATH=${PATH}:/goroot/bin:/gopath/bin

RUN set -xe && \
       mkdir -p ${GETHPATH} && \
       cd ${GETHPATH} && \
       export GOROOT="/goroot" && \
       export PATH=$PATH:$GOROOT/bin && \
       git init && \
       git remote add origin https://github.com/ethereum/go-ethereum && \
       git fetch --depth 1 origin "${GETH_VERSION}" && \
       git checkout FETCH_HEAD && \
       make geth && \
       install "${GETHPATH}/build/bin/geth" /usr/local/bin && \
       geth version

USER ${USER}

RUN set -xe \
 && mix do local.hex --force, local.rebar --force


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

ENV LD_LIBRARY_PATH=/usr/local/otp/lib:$LD_LIBRARY_PATH
ENV LD_LIBRARY_PATH=/usr/local/elixir/lib:$LD_LIBRARY_PATH
ENV PATH=/usr/local/otp/bin:$PATH
ENV PATH=/usr/local/rebar3/bin:$PATH
ENV PATH=/usr/local/elixir/bin:$PATH

ENV HOME /home/${USER}
ENV LANG=en_US.UTF-8
ENV LC_ALL=en_US.UTF-8

RUN wget https://github.com/ethereum/solidity/releases/download/v0.4.25/solc-static-linux \
  && chmod +x solc-static-linux \
  && mv solc-static-linux solc  \
  && install -m0755 solc /usr/local/bin/solc

RUN set -xe \
 && apk add --update --no-cache --virtual .utils \
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

USER ${USER}

RUN set -xe \
 && mix do local.hex --force, local.rebar --force


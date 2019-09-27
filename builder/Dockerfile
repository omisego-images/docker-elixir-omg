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
COPY --from=omisegoimages/elixir-omg-builder-rocksdb /usr/local/rocksdb /usr/local/rocksdb

ENV LD_LIBRARY_PATH=/usr/local/otp/lib:$LD_LIBRARY_PATH
ENV LD_LIBRARY_PATH=/usr/local/elixir/lib:$LD_LIBRARY_PATH
ENV LD_LIBRARY_PATH=/usr/local/rocksdb/lib:$LD_LIBRARY_PATH

ENV CMAKE_PREFIX_PATH=/usr/local/otp:$CMAKE_PREFIX_PATH
ENV CMAKE_PREFIX_PATH=/usr/local/elixir:$CMAKE_PREFIX_PATH
ENV CMAKE_PREFIX_PATH=/usr/local/rocksdb:$CMAKE_PREFIX_PATH

ENV PATH=/usr/local/otp/bin:$PATH
ENV PATH=/usr/local/rebar3/bin:$PATH
ENV PATH=/usr/local/elixir/bin:$PATH

ENV HOME /home/${USER}
ENV LANG=en_US.UTF-8
ENV LC_ALL=en_US.UTF-8

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

RUN set -xe \
  && cd /tmp \
  && curl -fsL -o solc https://github.com/ethereum/solidity/releases/download/v0.4.25/solc-static-linux \
  && chmod +x solc \
  && install -m0755 solc /usr/local/bin/solc \
  && scanelf --nobanner -E ET_EXEC -BF '%F' /usr/local/bin/solc | xargs -r strip --strip-all \
  && scanelf --nobanner -E ET_DYN -BF '%F' /usr/local/bin/solc | xargs -r strip --strip-unneeded

USER ${USER}

RUN set -xe \
 && mix do local.hex --force, local.rebar --force

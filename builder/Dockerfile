FROM alpine:3.12

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
ENV DOCKER=true
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

ENV RUSTUP_HOME=/usr/local/rustup \
    CARGO_HOME=/usr/local/cargo \
    PATH=/usr/local/cargo/bin:$PATH \
    RUST_VERSION=1.46.0

RUN set -eux; \
    url="https://static.rust-lang.org/rustup/archive/1.22.1/x86_64-unknown-linux-musl/rustup-init"; \
    wget "$url"; \
    echo "cee31c6f72b953c6293fd5d40142c7d61aa85db2a5ea81b3519fe1b492148dc9 *rustup-init" | sha256sum -c -; \
    chmod +x rustup-init; \
    ./rustup-init -y --no-modify-path --profile minimal --default-toolchain $RUST_VERSION --default-host x86_64-unknown-linux-musl; \
    rm rustup-init; \
    chmod -R a+w $RUSTUP_HOME $CARGO_HOME; \
    rustup --version; \
    cargo --version; \
    rustc --version;

USER ${USER}

RUN set -xe \
 && mix do local.hex --force, local.rebar --force

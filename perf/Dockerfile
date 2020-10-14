FROM elixir:1.10.4-alpine

ENV ETHEREUM_RPC_URL="http://localhost:8545"
ENV RETRY_SLEEP=1_000
ENV CHILD_CHAIN_URL="http://localhost:9656"
ENV WATCHER_SECURITY_URL="http://localhost:7434"
ENV WATCHER_INFO_URL="http://localhost:7534"
ENV LOAD_TEST_FAUCET_PRIVATE_KEY="0xd885a307e35738f773d8c9c63c7a3f3977819274638d04aaf934a1e1158513ce"

RUN apk add git curl bash maven jq \
        autoconf \
        automake \
        gmp \
        gmp-dev \
        libtool \
        gcc \
        cmake \
        gnupg \
        alpine-sdk

RUN git clone https://github.com/omgnetwork/elixir-omg

WORKDIR ./elixir-omg

RUN mkdir -p priv/openapitools \
        && curl https://raw.githubusercontent.com/OpenAPITools/openapi-generator/master/bin/utils/openapi-generator-cli.sh > priv/openapitools/openapi-generator-cli \
        && chmod u+x priv/openapitools/openapi-generator-cli

RUN priv/openapitools/openapi-generator-cli generate \
        -i https://raw.githubusercontent.com/omgnetwork/omg-childchain-v1/master/apps/omg_child_chain_rpc/priv/swagger/operator_api_specs.yaml \
        -g elixir \
        -o priv/perf/apps/child_chain_api/

RUN priv/openapitools/openapi-generator-cli generate \
        -i apps/omg_watcher_rpc/priv/swagger/security_critical_api_specs.yaml \
        -g elixir \
        -o priv/perf/apps/watcher_security_critical_api/

RUN priv/openapitools/openapi-generator-cli generate \
        -i apps/omg_watcher_rpc/priv/swagger/info_api_specs.yaml \
        -g elixir \
        -o priv/perf/apps/watcher_info_api/

RUN mix local.hex --force && mix local.rebar --force

WORKDIR ./priv/perf

RUN mix deps.get && mix compile

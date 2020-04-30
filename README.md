[![CircleCI](https://circleci.com/gh/omisego-images/docker-elixir-omg.svg?style=svg&circle-token=ce38b7a7577499882c41ec7b763f1aef88aec87f)](https://circleci.com/gh/omisego-images/docker-elixir-omg)

THE LATEST PLASMA CONTRACTS THAT ELIXIR-OMG SUPPORTS IS: https://github.com/omisego-images/docker-elixir-omg/blob/master/tester/CONTRACT_SHA

IF YOU NEED TO BUMP CONTRACTS (OR DOWNGRADE), PLEASE OPEN A PR AND UPDATE THIS FILE: https://github.com/omisego-images/docker-elixir-omg/blob/master/tester/CONTRACT_SHA 

IF YOU NEED TO SWAP THE CONTRACT REPO FROM PUBLIC TO PRIVATE FOR DEVELOPMENT, PLEASE UPDATE THIS FILE: https://github.com/omisego-images/docker-elixir-omg/blob/master/tester/CONTRACT_REPO_NAME

IF YOU NEED TO TURN ON CONTRACTS' EXPERIMENTAL FEATURES FOR DEVELOPMENT, PLEASE ADD THE FLAG CONFIGS TO THIS FILE: https://github.com/omisego-images/docker-elixir-omg/blob/master/tester/CONTRACT_EXPERIMENTAL_FEATURES. FOR INSTANCE, ADD `MULTI_EXIT_GAME_EXPERIMENT=true` TO TRIGGER THE BUILD FOR THE [EXPERIMENT FOR MULTIPLE EXIT GAMES](https://github.com/omisego/plasma-contracts/pull/616).

WAIT FOR BUILD TO PASS AFTER WHICH COMMENTS WITH FURTHER INSTRUCTIONS WILL APPEAR IN YOUR PR.

# OmiseGO Elixir OMG Images

This repository provides images for building [OmiseGO elixir-omg](https://github.com/omisego/elixir-omg) Docker image. **For the elixir-omg image itself, please see the [Dockerfile](https://github.com/omisego/elixir-omg/blob/master/Dockerfile.watcher https://github.com/omisego/elixir-omg/blob/master/Dockerfile.childchain) in elixir-omg repo itself.** This repository is meant for [CI/CD](https://jenkins.omisego.io/) use for the most part.

-   `builder` -- this is the builder image used to build and test the release in the CI/CD pipeline.
-   `tester` -- this is the tester image used to run tests in the CI/CD pipeline. Has geth.
-   `deploy` -- this is the deploy image used to publish the release to the staging environment. Does not have geth.

Please note that the image may change at any time. It is not recommend to rely on these images outside OmiseGO projects.

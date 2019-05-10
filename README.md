# OmiseGO Elixir OMG Images

This repository provides images for building [OmiseGO elixir-omg](https://github.com/omisego/elixir-omg) Docker image. **For the elixir-omg image itself, please see the [Dockerfile](https://github.com/omisego/elixir-omg/blob/master/Dockerfile.watcher https://github.com/omisego/elixir-omg/blob/master/Dockerfile.childchain) in elixir-omg repo itself.** This repository is meant for [CI/CD](https://jenkins.omisego.io/) use for the most part.

-   `builder` -- this is the builder image used to build and test the release in the CI/CD pipeline.
-   `deploy` -- this is the deploy image used to publish the release to the staging environment.

Please note that the image may change at any time. It is not recommend to rely on these images outside OmiseGO projects.

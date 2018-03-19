# docker-navcoind
This holds the Docker files necessary to run navcoind inside a Docker container.

## Building the image
To build the image, simply run the command below in the directory containing the Docker files:

> docker-compose build

This command will fetch navcoin-core from GitHub, setup and/or build all navcoin-core dependencies and build the project from the sources. The result is an image that can be used to start a Docker container from.

## Starting the service
To run the service that spawns a Docker container running the navcoind daemon, simply run the command below in the directory containing the Docker files:

> docker-compose up

To run the service entering an interactive shell, run the command below (developer note):

> docker-compose exec -ti navcoind-testnet /bin/bash

### Docker-compose.yml
This file describes the parameters used for running the Docker service. From this you can see that:
- navcoin-core branch used for the build
- parameters passed to navcoind (by default, this service spawns a navcoin testnet daemon)

### Overriding default build branch
The default build uses the current release branch. If you're testing out a branch or reviewing code on a branch in PR, you can change the NAV_BRANCH in `docker-compose.yml`

    services:
      testnet:
        build:
            context: .
            args:
                NAV_BRANCH: branch-name

- change/save the NAV_BRANCH to the branch you're reviewing/testing
- run `docker-compose up --build` to build out that branch and start the new image

**Direct shell override**

You can also just override the NAV_BRANCH directly in your shell command as follows:

    NAV_BRANCH=branch-name docker-compose up --build

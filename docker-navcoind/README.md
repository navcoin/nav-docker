# docker-navcoind
This holds the Docker files necessary to run navcoind inside a Docker container.

## Building the image
To build the image, simply run the command below in the directory containing the Docker files:

> docker-compose build

This command will fetch navcoin-core from GitHub, setup and/or build all navcoin-core dependencies and build the project from the sources. The result is an image that can be used to start a Docker container from.

## Starting the service
To run the service that spawns a Docker container running the navcoind daemon, simply run the command below in the directory containing the Docker files:

> docker-compose up

If the service is up and running you'll see the following:

> Recreating dockernavcoind_testnet_1 ... done
> Attaching to dockernavcoind_testnet_1

Then in another shell tab or window you can run commands an interactive shell mode as follows:

> docker exec -ti dockernavcoind_testnet_1 /bin/bash

If that command is successful you should see something like:

> root@a5by28ef4237:/#

From here, you can now start entering rpc commands:

> navcoin-cli -rpcuser=youruser -rpcpassword=yourpass getinfo

### Docker-compose.yml
This file describes the parameters used for running the Docker service. From this you can see that:
- navcoin-core branch used for the build
- parameters passed to navcoind (by default, this service spawns a navcoin testnet daemon)

**Overriding default branch**

The default build uses the current release branch. If you're testing out a branch or reviewing code on a branch in PR, you can change the NAV_BRANCH in `docker-compose.yml`

    services:
      testnet:
        build:
            context: .
            args:
                NAV_BRANCH: branch-name

save `docker-compose.yml` and run the following to rebuild and start service
> docker-compose up --build

### commands/overrides

override branch, build project, run service
> NAV_BRANCH=branch-name docker-compose up --build

enable tests, build project
> CONFIGURE_FLAGS=--enable-tests docker-compose up --build

override branch, enable tests, build project, run service
> NAV_BRANCH=branch-name; CONFIGURE_FLAGS=--enable-tests; docker-compose up --build


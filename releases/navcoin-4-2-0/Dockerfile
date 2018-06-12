FROM ubuntu:16.04

LABEL maintainer.0="Paul Sanderson"

#NavCoin settings
ARG NAVCOIN_VERSION=4.2.0
ARG NAV_CHK=166d64c4e61a2ff0900ce89bdf7e8b58b2ca97577af737c4b0b69f917d51208c
ARG NAV_PKG=navcoin-${NAVCOIN_VERSION}-x86_64-linux-gnu.tar.gz

#install the libs needed
RUN apt-get update -y
RUN apt-get install wget -y
RUN apt-get install libcurl4-openssl-dev -y

WORKDIR /tmp

#download NavCoin and check it
RUN mkdir -p ${NAVCOIN_VERSION}
RUN wget "https://github.com/NAVCoin/navcoin-core/releases/download/${NAVCOIN_VERSION}/${NAV_PKG}"
RUN echo "${NAV_CHK}  ${NAV_PKG}" | sha256sum -c
RUN tar -xzf ${NAV_PKG}

# move navcoind
WORKDIR /tmp/navcoin-${NAVCOIN_VERSION}/bin
RUN cp navcoind /bin/navcoind

WORKDIR /

#cleanup
RUN rm -rf /tmp/*

ENTRYPOINT ["/bin/bash", "-c"]

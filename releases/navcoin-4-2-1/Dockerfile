FROM ubuntu:16.04

LABEL maintainer.0="Paul Sanderson"

ARG NAVCOIN_VERSION=4.2.1
ARG NAV_CHK=1500674cc0c9f31a5369c21dd59b3cccdbf25c5db8a04f728967029552725d3e
ARG NAV_PKG=navcoin-${NAVCOIN_VERSION}-x86_64-linux-gnu.tar.gz

RUN apt-get update -y
RUN apt-get install wget -y
RUN apt-get install libcurl4-openssl-dev -y


WORKDIR /tmp

RUN mkdir -p ${NAVCOIN_VERSION}
RUN wget "https://github.com/NAVCoin/navcoin-core/releases/download/${NAVCOIN_VERSION}/${NAV_PKG}"
RUN echo "${NAV_CHK}  ${NAV_PKG}" | sha256sum -c
RUN tar -xzf ${NAV_PKG}

WORKDIR /tmp/navcoin-${NAVCOIN_VERSION}/bin
RUN cp navcoind /bin/navcoind

WORKDIR /

ENTRYPOINT ["/bin/bash", "-c"]

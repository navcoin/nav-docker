FROM ubuntu:xenial

LABEL maintainer.0="Paul Sanderson" \
	maintainer.1="Nick Vanbaelen"

ARG USER_ID
ARG GROUP_ID

ARG NAV_BRANCH=v4.1.0
ARG NAV_REPO=https://github.com/NAVCoin/navcoin-core.git

ARG GOSU_URL=https://github.com/tianon/gosu/releases/download/
ARG GOSU_VER=1.9

ARG BDB_URL=http://download.oracle.com/berkeley-db
ARG BDB_VER=db-4.8.30.NC
ARG BDB_PKG=${BDB_VER}.tar.gz
ARG BDB_CHK=12edc0df75bf9abd7f82f821795bcee50f42cb2e5f76a6a281b85732798364ef
ARG BDB_DIR=/usr/local/berkeley-db-4.8

#--------------------------------------------------------------------------------
# Add user and group first to make sure their IDs get assigned consistently,
# regardless of whatever dependencies get added
#--------------------------------------------------------------------------------
RUN groupadd -g ${GROUP_ID} navcoin
RUN useradd -u ${USER_ID} -g navcoin -s /bin/bash -m -d /navcoin navcoin

#--------------------------------------------------------------------------------
# Installing packages
#--------------------------------------------------------------------------------
RUN apt-get update
RUN apt-get install -yq --no-install-recommends build-essential
RUN apt-get install -yq --no-install-recommends libcurl3-dev
RUN apt-get install -yq --no-install-recommends libtool
RUN apt-get install -yq --no-install-recommends autotools-dev
RUN apt-get install -yq --no-install-recommends automake
RUN apt-get install -yq --no-install-recommends pkg-config
RUN apt-get install -yq --no-install-recommends libssl-dev
RUN apt-get install -yq --no-install-recommends libevent-dev
RUN apt-get install -yq --no-install-recommends bsdmainutils
RUN apt-get install -yq --no-install-recommends libzmq3-dev
RUN apt-get install -yq --no-install-recommends libqrencode-dev
RUN apt-get install -yq --no-install-recommends qrencode
RUN apt-get install -yq --no-install-recommends wget
RUN apt-get install -yq --no-install-recommends curl
RUN apt-get install -yq --no-install-recommends libboost-system-dev
RUN apt-get install -yq --no-install-recommends libboost-filesystem-dev
RUN apt-get install -yq --no-install-recommends libboost-chrono-dev
RUN apt-get install -yq --no-install-recommends libboost-program-options-dev
RUN apt-get install -yq --no-install-recommends libboost-test-dev
RUN apt-get install -yq --no-install-recommends libboost-thread-dev
RUN apt-get install -yq --no-install-recommends libminiupnpc-dev
RUN apt-get install -yq --no-install-recommends ca-certificates	
RUN apt-get install -yq --no-install-recommends libunbound-dev
RUN apt-get install -yq --no-install-recommends git
RUN apt-get clean
RUN rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

#--------------------------------------------------------------------------------
# Install gosu
#--------------------------------------------------------------------------------
RUN gpg --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4
RUN curl -o /usr/local/bin/gosu -fSL ${GOSU_URL}/${GOSU_VER}/gosu-$(dpkg --print-architecture)
RUN curl -o /usr/local/bin/gosu.asc -fSL ${GOSU_URL}/${GOSU_VER}/gosu-$(dpkg --print-architecture).asc
RUN gpg --verify /usr/local/bin/gosu.asc
RUN rm /usr/local/bin/gosu.asc
RUN chmod +x /usr/local/bin/gosu

#--------------------------------------------------------------------------------
# Build + install Berkeley DB
#--------------------------------------------------------------------------------
WORKDIR /tmp
RUN mkdir -p ${BDB_DIR}
RUN wget "${BDB_URL}/${BDB_PKG}" -q
RUN echo "${BDB_CHK}  ${BDB_PKG}" | sha256sum -c
RUN tar -xzf ${BDB_PKG}

WORKDIR /tmp/${BDB_VER}/build_unix/
RUN ../dist/configure --enable-cxx \
                      --disable-shared \
                      --with-pic \
                      --prefix=$BDB_DIR
RUN make install

#--------------------------------------------------------------------------------
# Build + install navcoind
#--------------------------------------------------------------------------------
WORKDIR /tmp
RUN git clone -b ${NAV_BRANCH} ${NAV_REPO}

WORKDIR /tmp/navcoin-core
RUN ./autogen.sh
RUN ./configure LDFLAGS="-L${BDB_DIR}/lib/" \
                CPPFLAGS="-I${BDB_DIR}/include/" \
                --enable-hardening \
                --without-gui \
                --enable-upnp-default
RUN make
RUN make install

WORKDIR /
RUN rm -fr /tmp/*

#--------------------------------------------------------------------------------
# Copy files
#--------------------------------------------------------------------------------
#ADD ./conf/apache2.conf /etc/apache2/
#ADD ./conf/navpi.conf /etc/apache2/sites-available/
#ADD ./bin /usr/local/bin
#ADD docker-entrypoint.sh /usr/local/bin/docker-entrypoint

#--------------------------------------------------------------------------------
## Create ssl certificate
#--------------------------------------------------------------------------------
#RUN mkdir /etc/apache2/ssl && cd /etc/apache2/ssl
#RUN openssl genrsa -des3 -passout pass:x -out tmp-navpi-ssl.key 2048
#RUN openssl rsa -passin pass:x -in tmp-navpi-ssl.key -out navpi-ssl.key
#RUN openssl req -new -key navpi-ssl.key -out navpi-ssl.csr -subj "/C=NZ/ST=Auckland/L=Auckland/O=Nav Coin/OU=Nav Pi/CN=my.navpi.org"
#RUN openssl x509 -req -days 365 -in navpi-ssl.csr -signkey navpi-ssl.key -out navpi-ssl.crt
#RUN rm tmp-navpi-ssl.key navpi-ssl.csr

#--------------------------------------------------------------------------------
## Enable apache modules and site
#--------------------------------------------------------------------------------
#RUN a2enmod rewrite && a2enmod php5 && a2enmod ssl
#RUN a2ensite navpi.conf && a2dissite 000-default.conf

ENTRYPOINT ["/bin/bash", "-c"]
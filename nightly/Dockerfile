FROM kukkimonsuta/armhf-mono:4.1

MAINTAINER Lukas Novotny <lukas@dramiel.com>

# Based on https://github.com/aspnet/aspnet-docker

ENV DNX_FEED https://www.myget.org/F/aspnetvnext/api/v2
ENV DNX_USER_HOME /opt/dnx

RUN apt-get -qq update && apt-get -qqy install unzip curl

# Install nodejs
RUN curl -sL https://deb.nodesource.com/setup_0.12 | sudo bash - \
	&& apt-get install -y nodejs

# Install libuv for Kestrel from source code (binary is not in wheezy and one in jessie is still too old)
RUN apt-get -qy install \
	autoconf \
	automake \
	build-essential \
	libtool
RUN LIBUV_VERSION=1.4.2 \
	&& curl -sSL https://github.com/libuv/libuv/archive/v${LIBUV_VERSION}.tar.gz | tar zxfv - -C /usr/local/src \
	&& cd /usr/local/src/libuv-$LIBUV_VERSION \
	&& sh autogen.sh && ./configure && make && make install \
	&& rm -rf /usr/local/src/libuv-$LIBUV_VERSION \
	&& cd ~ \
	&& ldconfig

ONBUILD RUN curl -sSL https://raw.githubusercontent.com/aspnet/Home/dev/dnvminstall.sh | DNX_BRANCH=dev sh
ONBUILD RUN bash -c "source $DNX_USER_HOME/dnvm/dnvm.sh \
	&& dnvm install latest -a default \
	&& dnvm alias default | xargs -i ln -s $DNX_USER_HOME/runtimes/{} $DNX_USER_HOME/runtimes/default"

COPY NuGet.Config /tmp/
RUN mkdir -p $HOME/.config/NuGet/ && mv /tmp/NuGet.Config $HOME/.config/NuGet/

ENV PATH $PATH:$DNX_USER_HOME/runtimes/default/bin
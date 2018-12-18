FROM ubuntu:18.04

ENV CONFIG_DIR /etc
ENV LOG_DIR /var/log/accel-ppp
ENV WORKDIR /root/accel-ppp
RUN set -x \
	&& buildDeps=' \
		git \
		cmake \
          pkg-config' \
	&& deps=' \
		libnl-3-dev \
		libssl-dev \
          libpcre3-dev \
		ppp \
          libhiredis-dev \
          libjson-c-dev' \
	&&  apt update -y && apt upgrade -y && apt install -y $buildDeps $deps

RUN mkdir -p $CONFIG_DIR $LOG_DIR $WORKDIR
RUN	set - x \
		&& git clone --single-branch https://github.com/dpdk-vbng-cp/accel-ppp.git $WORKDIR \
          && mkdir $WORKDIR/build \
          && cd $WORKDIR/build \
		&& cmake -DBUILD_DRIVER=TRUE -DRADIUS=TRUE  .. \
		&& make \
		&& make install

WORKDIR $WORKDIR

# forward request and error logs to docker log collector
RUN ln -sf /dev/stdout $LOG_DIR/accel-ppp.log \
     && ln -sf /dev/stdout $LOG_DIR/debug.log \
     && ln -sf /dev/stdout $LOG_DIR/auth-fail.log \
     && ln -sf /dev/stderr $LOG_DIR/error.log \
     && ln -sf /dev/stderr $LOG_DIR/emerg.log 

COPY etc $CONFIG_DIR/
EXPOSE 2001/tcp
CMD ["accel-pppd", "-c", "/etc/accel-ppp.conf"]

FROM alpine:3.13 as builder
RUN apk add --no-cache \
    autoconf \
    automake \
    build-base \
    clang \
    clang-static \
    gettext-dev \
    gettext-static \
    git \
    libmaxminddb-dev \
    libmaxminddb-static \
    libressl-dev \
    linux-headers \
    ncurses-dev \
    ncurses-static \
    tzdata

# GoAccess
COPY . /goaccess
WORKDIR /goaccess
RUN autoreconf -fiv
RUN CC="clang" CFLAGS="-O3 -static" LIBS="$(pkg-config --libs openssl)" ./configure --prefix=/install --enable-utf8 --with-openssl --enable-geoip=mmdb
RUN make -j8 && make install
RUN sed -i 's/#time-format %H:%M:%S/time-format %H:%M:%S/' /goaccess/config/goaccess.conf
RUN sed -i 's_#date-format %d/%b/%Y_date-format %d/%b/%Y_' /goaccess/config/goaccess.conf
RUN sed -i 's/#log-format %h %\^\[%d:%t %\^\] "%r" %s %b "%R" "%u"/log-format %h %^[%d:%t %^] "%r" %s %b "%R" "%u"/' /goaccess/config/goaccess.conf

FROM alpine:3.13 as package
COPY --from=builder /install /install
COPY --from=builder /goaccess/config /goaccess/config
COPY --from=builder /goaccess/bin2c /

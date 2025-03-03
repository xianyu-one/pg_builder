FROM ubuntu:22.04 AS builder

RUN export DEBIAN_FRONTEND=noninteractive && \
    export TZ=Asia/Taipei && \
    apt update && apt upgrade -y && \
    apt install -y \
    build-essential \
    libreadline-dev \
    zlib1g-dev \
    python3-distutils \
    libsystemd-dev \
    tcl-dev \
    libperl-dev \
    meson \
    gcc \
    libpq-dev \
    libssl-dev \
    libxml2-dev \
    pkg-config \
    liblz4-dev \
    libzstd-dev \
    libbz2-dev \
    libz-dev \
    libyaml-dev \
    libssh2-1-dev \
    python3-dev \
    libselinux1-dev \
    libpam0g-dev \
    libkrb5-dev \
    git \
    postgresql-server-dev-all \
    wget

 
RUN mkdir /build && \
   mkdir /release && \
   wget -q -O - \
      https://github.com/pgbackrest/pgbackrest/archive/release/2.54.2.tar.gz | \
      tar zx -C /build && \
   meson setup /build/pgbackrest /build/pgbackrest-release-2.54.2 && \
   ninja -C /build/pgbackrest && \
   cd build && \
   git clone --branch REL_15_STABLE --single-branch https://github.com/ossc-db/pg_rman.git && \
   cd /build/pg_rman && \
   make && \
   make install && \
   wget -q -O - \
      https://ftp.postgresql.org/pub/source/v15.12/postgresql-15.12.tar.gz | \
      tar zx -C /build && \
   cd /build/postgresql-15.12 && \
   export CPU_CORES=$(nproc) && \
   ./configure \
      --prefix=/release/postgresql \
      --with-ssl=openssl \
      --with-perl \
      --with-python \
      --with-tcl \
      --with-systemd \
      --with-lz4 && \
   make -j $CPU_CORES world && \
   make install-world && \
   cp /build/pgbackrest/src/pgbackrest /release/postgresql/bin/ && \
   cp /build/pg_rman/pg_rman /release/postgresql/bin/ && \
   cd /release/postgresql/ && \
   tar -czvf /release/postgresql.tar.gz ./*

FROM alpine:3

COPY --from=builder /release/postgresql.tar.gz /release/postgresql.tar.gz
   
WORKDIR /export-path
   
CMD ["tar", "-xzvf", "/release/postgresql.tar.gz", "-C", "/export-path"]
   
ARG PG_VERSION=16.9
ARG PGBACKREST_VERSION=2.54.2
ARG REPMGR_VERSION=v5.4.1
ARG PGRMAN_BRANCH=REL_16_STABLE
ARG ARCHIVE_NAME=postgresql-16.9-ubuntu22.04.tar.gz

# 阶段1: 构建器
FROM ubuntu:22.04 AS builder

# 传递构建参数到此阶段
ARG PG_VERSION
ARG PGBACKREST_VERSION
ARG REPMGR_VERSION
ARG PGRMAN_BRANCH
ARG ARCHIVE_NAME

# 安装所有构建依赖
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
    wget \
    libcurl4-openssl-dev \
    libjson-c-dev \
    flex

# 创建工作目录
RUN mkdir /build && mkdir /release

# 编译 PostgreSQL
RUN cd /build && \
   wget -q -O - https://ftp.postgresql.org/pub/source/v${PG_VERSION}/postgresql-${PG_VERSION}.tar.gz | \
      tar zx -C /build && \
   cd /build/postgresql-${PG_VERSION} && \
   export CPU_CORES=$(nproc) && \
   ./configure \
      --prefix=/build/postgresql-release \
      --with-ssl=openssl \
      --with-perl \
      --with-python \
      --with-tcl \
      --with-systemd \
      --with-lz4 && \
   make -j $CPU_CORES world && \
   make install-world

ENV PKG_CONFIG_PATH="/build/postgresql-release/lib/pkgconfig"
ENV PATH="/build/postgresql-release/bin:${PATH}"

# 编译 pgBackRest
RUN cd /build && \
   wget -q -O - https://github.com/pgbackrest/pgbackrest/archive/release/${PGBACKREST_VERSION}.tar.gz | \
      tar zx -C /build && \
   cd /build/pgbackrest-release-${PGBACKREST_VERSION} && \
   meson setup build && \
   ninja -C build && \
   cp build/src/pgbackrest /build/postgresql-release/bin/

# 编译 pg_rman
RUN cd /build && \
   git clone --branch ${PGRMAN_BRANCH} --depth 1 https://github.com/ossc-db/pg_rman.git && \
   cd /build/pg_rman && \
   make && \
   make install

# 编译 repmgr
RUN cd /build && \
   git clone --branch ${REPMGR_VERSION} --depth 1 https://github.com/EnterpriseDB/repmgr.git && \
   cd /build/repmgr && \
   ./configure && \
   make install

# 将所有编译好的文件打包成一个 .tar.gz
RUN cd /build/postgresql-release && \
   tar -czvf /release/${ARCHIVE_NAME} ./*

# 阶段 2: 导出器
FROM scratch

ARG ARCHIVE_NAME

COPY --from=builder /release/${ARCHIVE_NAME} /${ARCHIVE_NAME}
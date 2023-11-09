FROM debian:12
MAINTAINER Ashwin Ganesh <ganesasp@amazon.com>

# First round of dependences
RUN apt-get update && apt-get install -y \
        apt \
        apt-file \
        apt-utils \
        autoconf \
	automake \
        autotools-dev \
	bash-completion \
        bc \
        bind9-host \
        binfmt-support \
        binfmt-support \
        bison \
        bsdmainutils \
        build-essential \
        ccache \
        cdbs \
        cpio \
        cryptsetup-bin \
        debhelper \
	device-tree-compiler \
        devscripts \
        devscripts \
        dialog \
        dosfstools \
        doxygen \
        dpkg-cross \
        emacs \
        file \
        flex \
        gawk \
        gcc \
        gdb \
        genisoimage \
	git \
        gperf \
        ifupdown \
        iproute2 \
        iputils-ping \
	isolinux \
        kmod \
        less \
        libc6-dev \
	libcurl4-nss-dev \
	libdouble-conversion-dev \
        libedit-dev \
	libevent-dev \
        libexpat1-dev \
        libgoogle-glog-dev \
        libi2c-dev \
	libkrb5-dev \
	libnuma-dev \
	libsasl2-dev \
	libsnappy-dev \
	libpam-dev \
        libpcap-dev \
        libsnmp-dev \
	libssl-dev \
        libtool \
        libtool-bin \
        locales \
        lsof \
        make \
        mingetty \
        mtd-utils \
        mtools \
        multistrap \
        nano \
        ncurses-dev \
        netbase \
        net-tools \
        nfs-common \
        openssh-server \
        pkg-config \
        pkg-config \
        procps \
        psmisc \
        python-is-python3 \
        python3 \
        python3-debian \
        python3-dnspython \
        python3-yaml \
        qemu-system \
        qemu-system-arm \
        qemu-system-ppc \
        qemu-user-static \
        rsyslog \
        rsync \
        ruby \
	ruby-dev \
	rubygems \
	screen \
        squashfs-tools \
        sshpass \
        stgit \
        sudo \
	syslinux-utils \
        telnet \
        texinfo \
        traceroute \
	u-boot-tools \
        vim-tiny \
        wget \
        xorriso \
        zile \
        zip

RUN apt-get install -y \
chrpath devscripts dh-autoreconf flex \
libcap-dev libc-ares-dev libjson-c-dev libpam0g-dev libpcre3-dev \
libreadline-dev libsystemd-dev pkg-config \
texlive-plain-generic texinfo texlive-latex-base

RUN     gem install rake
RUN     gem install --version 1.3.3 fpm

#
# The i2c-dev.h user/kernel header conflict is a nightmare.
#
# The ONLP implementation expects a new file called <linux/i2c-device.h> to be in place which contains the correct user-space driver definitions.
# This should be manually populated here after the toolchains have been installed.
#
RUN cp /usr/include/linux/i2c-dev.h /usr/include/linux/i2c-devices.h


# Cross environments.

RUN wget http://old-releases.ubuntu.com/ubuntu/pool/universe/e/emdebian-crush/xapt_2.2.20_all.deb && \
    dpkg -i xapt_2.2.20_all.deb && \
    rm xapt_2.2.20_all.deb

RUN dpkg --add-architecture arm64 && \
    dpkg --add-architecture armel && \
    dpkg --add-architecture armhf

RUN apt-get install -y \
    crossbuild-essential-arm64 \
    crossbuild-essential-armel \
    crossbuild-essential-armhf

RUN xapt -a arm64   libi2c-dev libsnmp-dev libssl-dev libedit-dev ncurses-dev && \
    xapt -a armel   libi2c-dev libsnmp-dev libssl-dev libedit-dev ncurses-dev && \
    xapt -a armhf   libi2c-dev libsnmp-dev libssl-dev libedit-dev ncurses-dev

# Copied from 10.1.1 builder
 COPY multistrap-insecure-fix.patch /tmp
 RUN  patch -p1 < /tmp/multistrap-insecure-fix.patch /usr/sbin/multistrap
 RUN rm /tmp/multistrap-insecure-fix.patch

# Copied from 10.1.5 builder
RUN apt -y --fix-broken install && apt-get upgrade -y && apt-get -y install apt-cacher-ng \
 libmnl-dev libmnl-dev  libelf-dev libcap-dev libxtables-dev  libdb-dev

RUN xapt -a arm64 libmnl-dev libelf-dev libcap-dev libxtables-dev libdb-dev

RUN rm -rf /var/lib/apt/lists/* && rm -rf /var/cache/apt/*

#
# Copy the docker shell init script to /bin
#
COPY docker_shell /bin/docker_shell
COPY container-id /bin/container-id

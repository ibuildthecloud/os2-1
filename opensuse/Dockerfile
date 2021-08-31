# Elemental runtime image, should be a proper elemental release like
# rancher/elemental:v0.1.0
ARG E_IMAGE=rancher/elemental:dev
FROM $E_IMAGE as elemental

# Make OS image
FROM opensuse/leap:15.3 as os
RUN zypper in -y \
    avahi \
    bash-completion \
    conntrack-tools \
    coreutils \
    curl \
    device-mapper \
    dosfstools \
    dracut \
    e2fsprogs \
    findutils \
    gawk \
    gptfdisk \
    grub2-i386-pc \
    grub2-x86_64-efi \
    haveged \
    iproute2 \
    iptables \
    iputils \
    issue-generator \
    jq \
    kernel-default \
    kernel-firmware-bnx2 \
    kernel-firmware-i915 \
    kernel-firmware-intel \
    kernel-firmware-iwlwifi \
    kernel-firmware-mellanox \
    kernel-firmware-network \
    kernel-firmware-platform \
    kernel-firmware-realtek \
    less \
    lsscsi \
    lvm2 \
    mdadm \
    multipath-tools \
    nano \
    netcat-openbsd \
    nfs-utils \
    open-iscsi \
    open-vm-tools \
    parted \
    pigz \
    policycoreutils \
    psmisc \
    procps \
    python-azure-agent \
    qemu-guest-agent \
    rng-tools \
    rsync \
    squashfs \
    strace \
    systemd \
    systemd-sysvinit \
    tcpdump \
    tar \
    timezone \
    vim \
    which

# Copy in some local OS customizations
COPY files /

# Starting from here are the lines needed for elemental to work

# IMPORTANT: Setup elemental-release used for versioning/upgrade. The
# values here should reflect the tag of the image building built
ARG IMAGE_REPO=norepo
ARG IMAGE_TAG=latest
RUN echo "IMAGE_REPO=${IMAGE_REPO}"          > /usr/lib/elemental-release && \
    echo "IMAGE_TAG=${IMAGE_TAG}"           >> /usr/lib/elemental-release && \
    echo "IMAGE=${IMAGE_REPO}:${IMAGE_TAG}" >> /usr/lib/elemental-release

# Copy in Elemental runtime
COPY --from=elemental / /

# Rebuild initrd to setup dracut with the boot configurations
RUN mkinitrd

# ISO BUILD
# Add support for generating an ISO from the Docker build. If you
# don't need an iso you can remove all lines from here until the
# end of the file
FROM ${E_IMAGE}-tools as iso-build
COPY --from=os / /iso/overlay
RUN makeiso
FROM scratch as iso
COPY --from=iso-build /output.iso /
FROM os

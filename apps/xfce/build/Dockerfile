# syntax=docker/dockerfile:1.4
ARG BASE_APP_IMAGE=ghcr.io/penturduckenline/gow-base-app:fedora
FROM ${BASE_APP_IMAGE}

# -----------------------------
# Package groups and packages
# -----------------------------

# Core utilities
ARG CORE_PACKAGES=" \
    lsb-release \
    wget \
    gnupg \
    dbus-x11 \
    firefox \
    flatpak \
    sudo \
"

# XFCE desktop environment packages (Fedora only)
ARG DE_PACKAGES=" \
    @xfce-desktop-environment \
    xfce4-settings \
    papirus-icon-theme \
    arc-theme \
    at-spi2-core \
"

# Additional apps / utilities
ARG ADDITIONAL_PACKAGES=" \
    xfce4-terminal \
    xfce4-taskmanager \
    xfce4-whiskermenu-plugin \
    xfce4-docklike-plugin \
    xarchiver \
    mousepad \
    zip unzip p7zip \
    gnome-software \
    gnome-software-plugin-flatpak \
"

# -----------------------------
# Install packages safely
# -----------------------------
RUN set -eux; \
    # Install core packages
    dnf -y install $CORE_PACKAGES; \
    \
    # Install XFCE desktop environment and related packages
    dnf -y install --allowerasing $DE_PACKAGES; \
    \
    # Install additional utilities (skip if unavailable)
    dnf -y install --skip-unavailable $ADDITIONAL_PACKAGES; \
    \
    # Remove optional/unnecessary packages safely
    dnf -y remove firefox-langpacks || true; \
    \
    # Clean up
    dnf clean all; \
    rm -rf /var/cache/dnf /tmp/* /var/tmp/*

# -----------------------------
# Replace launch scripts
# -----------------------------
COPY --chmod=777 scripts/launch-comp.sh scripts/startup.sh /opt/gow/
COPY --chmod=777 scripts/startdbus.sh /opt/gow/startdbus

# -----------------------------
# Include default XFCE config
# -----------------------------
COPY --chmod=777 --chown=retro:retro scripts/xfce4 /opt/gow/xfce4

# -----------------------------
# Fix locales
# -----------------------------
COPY scripts/locale /etc/locale.conf

# -----------------------------
# Allow anyone to start dbus without password
# -----------------------------
RUN echo "ALL ALL=NOPASSWD: /opt/gow/startdbus" >> /etc/sudoers

# -----------------------------
# Fix bwrap perms for flatpaks
# -----------------------------
RUN chmod u+s /usr/bin/bwrap

# -----------------------------
# Environment variables
# -----------------------------
ENV XDG_RUNTIME_DIR=/tmp/.X11-unix

ARG IMAGE_SOURCE
LABEL org.opencontainers.image.source=$IMAGE_SOURCE

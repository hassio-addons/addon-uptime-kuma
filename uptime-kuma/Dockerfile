ARG BUILD_FROM=ghcr.io/hassio-addons/base:14.1.3
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Set workdir
WORKDIR /opt

# Copy Python requirements file
COPY requirements.txt /opt/

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Setup base
ARG UPTIME_KUMA_VERSION="1.23.2"
# hadolint ignore=DL3003,DL3042
RUN \
    apk add --no-cache --virtual .build-dependencies \
        build-base=0.5-r3 \
        py3-pip=23.1.2-r0 \
    \
    && apk add --no-cache \
        nodejs=18.17.1-r0 \
        npm=9.6.6-r0 \
        python3=3.11.5-r0 \
        iputils=20221126-r2 \
        setpriv=2.38.1-r8 \
    \
    && mkdir -p /opt/uptime-kuma \
    && curl -L -s "https://github.com/louislam/uptime-kuma/archive/refs/tags/${UPTIME_KUMA_VERSION}.tar.gz" \
        | tar zxvf - --strip-components 1 -C /opt/uptime-kuma \
    \
    && cd /opt/uptime-kuma \
    \
    && pip install -r /opt/requirements.txt \
    \
    && npm ci \
        --no-audit \
        --no-fund \
        --no-update-notifier \
        --omit=dev \
    && npm run download-dist \
    \
    && npm cache clear --force \
    \
    && apk del --no-cache --purge .build-dependencies \
    && rm -fr \
        /tmp/* \
        /root/.cache \ 
        /root/.npm \
        /root/.npmrc


# Copy root filesystem
COPY rootfs /

# Build arguments
ARG BUILD_ARCH
ARG BUILD_DATE
ARG BUILD_DESCRIPTION
ARG BUILD_NAME
ARG BUILD_REF
ARG BUILD_REPOSITORY
ARG BUILD_VERSION


# Labels
LABEL \
    io.hass.name="${BUILD_NAME}" \
    io.hass.description="${BUILD_DESCRIPTION}" \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION} \
    maintainer="Franck Nijhof <frenck@addons.community>" \
    org.opencontainers.image.title="${BUILD_NAME}" \
    org.opencontainers.image.description="${BUILD_DESCRIPTION}" \
    org.opencontainers.image.vendor="Home Assistant Community Add-ons" \
    org.opencontainers.image.authors="Franck Nijhof <frenck@addons.community>" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.url="https://addons.community" \
    org.opencontainers.image.source="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/README.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}

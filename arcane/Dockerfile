ARG BUILD_FROM=ghcr.io/hassio-addons/base:21.0.12
ARG ARCANE_VERSION=v3.1.1
FROM getarcaneapp/manager:${ARCANE_VERSION} AS arcane

FROM ${BUILD_FROM}

COPY --from=arcane /app/arcane /app/arcane
COPY rootfs /

RUN chmod +x /etc/services.d/arcane/run /etc/services.d/arcane/finish \
    && mkdir -p /app/data /builds

ENV ARCANE_PORT=3552 \
    ARCANE_ENVIRONMENT=production \
    ARCANE_IN_CONTAINER=true

WORKDIR /app
EXPOSE 3552

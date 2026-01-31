FROM ghcr.io/kailas-cloud/valkey-bundle:9.0-coordinator

RUN mkdir -p /valkey/etc
COPY valkey.conf /valkey/etc/valkey.conf

ENTRYPOINT ["valkey-server", "/valkey/etc/valkey.conf"]

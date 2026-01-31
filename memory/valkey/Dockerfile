FROM valkey/valkey-bundle:9.0

RUN mkdir -p /valkey/etc
COPY valkey.conf /valkey/etc/valkey.conf

ENTRYPOINT ["valkey-server", "/valkey/etc/valkey.conf"]

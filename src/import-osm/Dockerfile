FROM golang:1.4

RUN DEBIAN_FRONTEND=noninteractive apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
      libprotobuf-dev \
      libleveldb-dev \
      libgeos-dev \
      postgresql-client \
      osmctools \
      --no-install-recommends \
 && ln -s /usr/lib/libgeos_c.so /usr/lib/libgeos.so \
 && rm -rf /var/lib/apt/lists/*

WORKDIR $GOPATH/src/github.com/omniscale/imposm3
RUN go get github.com/tools/godep \
 && git clone https://github.com/osm2vectortiles/imposm3 \
        $GOPATH/src/github.com/omniscale/imposm3 \
 && git reset --hard 060f4111fcaf459e8b09a0351836dfe8eb4dc943 \
 && go get \
 && go install

# Purge no longer needed packages to keep image small.
# Protobuf and LevelDB dependencies cannot be removed
# because they are dynamically linked.
RUN apt-get purge -y --auto-remove \
    g++ gcc libc6-dev make git \
    && rm -rf /var/lib/apt/lists/*

VOLUME /data/import /data/cache
ENV IMPORT_DATA_DIR=/data/import \
    IMPOSM_CACHE_DIR=/data/cache \
    MAPPING_YAML=/usr/src/app/mapping.yml

WORKDIR /usr/src/app
COPY . /usr/src/app/

CMD ["./import-pbf.sh"]

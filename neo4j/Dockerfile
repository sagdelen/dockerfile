FROM openjdk:8-jre-alpine

maintainer agdelen, agdelens@gmail.com

## from offical Dockerfile

RUN addgroup -S neo4j && adduser -S -H -h /var/lib/neo4j -G neo4j neo4j

ENV NEO4J_SHA256=9c4438466e2a3b4ef7b4a8f4b9564b963b5059aa36fea547521eef88cc384653 \
    NEO4J_TARBALL=neo4j-enterprise-3.3.4-unix.tar.gz \
    NEO4J_EDITION=enterprise
ARG NEO4J_URI=http://dist.neo4j.org/neo4j-enterprise-3.3.4-unix.tar.gz

RUN apk add --no-cache --quiet \
    bash \
    curl \
    tini \
    su-exec \
    && curl --fail --silent --show-error --location --remote-name ${NEO4J_URI} \
    && echo "${NEO4J_SHA256}  ${NEO4J_TARBALL}" | sha256sum -csw - \
    && tar --extract --file ${NEO4J_TARBALL} --directory /var/lib \
    && mv /var/lib/neo4j-* /var/lib/neo4j \
    && rm ${NEO4J_TARBALL} \
    && mv /var/lib/neo4j/data /data \
    && chown -R neo4j:neo4j /data \
    && chmod -R 777 /data \
    && chown -R neo4j:neo4j /var/lib/neo4j \
    && chmod -R 777 /var/lib/neo4j \
    && ln -s /data /var/lib/neo4j/data \
    && apk del curl

ENV PATH /var/lib/neo4j/bin:$PATH

## add graphaware

WORKDIR /var/lib/neo4j/plugins
RUN apk update && \
    apk add ca-certificates wget nano && \
    update-ca-certificates && \
    wget https://products.graphaware.com/download/framework-server-enterprise/graphaware-server-enterprise-all-3.3.3.52.jar && \
    wget https://products.graphaware.com/download/uuid/graphaware-uuid-3.3.3.52.16.jar && \
	wget https://github.com/neo4j-contrib/neo4j-apoc-procedures/releases/download/3.3.0.2/apoc-3.3.0.2-all.jar

## from offical Dockerfile

WORKDIR /var/lib/neo4j

VOLUME /data

COPY docker-entrypoint.sh /docker-entrypoint.sh

## enable graphaware framework

COPY neo4j.addition.conf conf/neo4j.addition.conf

RUN cat conf/neo4j.addition.conf >> conf/neo4j.conf

## export port and start

EXPOSE 7474 7473 7687

ENTRYPOINT ["/sbin/tini", "-g", "--", "/docker-entrypoint.sh"]
CMD ["neo4j"]
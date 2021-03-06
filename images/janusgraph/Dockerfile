FROM fabric8/java-centos-openjdk8-jdk

ARG JANUSGRAPH_VERSION=0.2.0-hadoop2

ENV JG_VER=${JANUSGRAPH_VERSION}

LABEL io.k8s.description="JanusGraph Server, backed by Cassandra and ElasticSearch" \
    io.k8s.display-name="JanusGraph ${JG_VER}" \
    io.openshift.expose-services="8080:http, 8182:http, 8184:http" \
    io.openshift.tags="cassandra,elasticsearch,graph,gremlin,janusgraph" \
    architecture=x86_64 \
    name="aicoe/janusgraph" \
    version="1.1.0" \
    release="1" \
    vendor="Red Hat Office of the CTO - AI CoE" \
    license="GPLv3" 

WORKDIR /opt/janusgraph-${JG_VER}

RUN yum install -y unzip && \
    yum clean all -y && \
    curl -L -o /opt/janusgraph.zip https://github.com/JanusGraph/janusgraph/releases/download/v0.2.0/janusgraph-0.2.0-hadoop2.zip && \
    unzip /opt/janusgraph.zip -d /opt/ && \
    rm /opt/janusgraph.zip && \
    mkdir -p /opt/janusgraph-0.2.0-hadoop2/db/berkeley && \
    chmod 777 -R /opt/janusgraph-0.2.0-hadoop2/db/ && \
    chown 1011:0 -R /opt/janusgraph-0.2.0-hadoop2/ && \
    sed -i 's/^storage.backend=cassandrathrift/storage.backend=cql/' /opt/janusgraph-0.2.0-hadoop2/conf/gremlin-server/janusgraph-cassandra-es-server.properties && \
    sed -i 's/^scriptEvaluationTimeout: 30000/scriptEvaluationTimeout: 70000/' /opt/janusgraph-0.2.0-hadoop2/conf/gremlin-server/gremlin-server.yaml && \
    echo 'schema.default=none' >> /opt/janusgraph-0.2.0-hadoop2/conf/gremlin-server/janusgraph-cassandra-es-server.properties && \
    chmod a+rw /etc/passwd  # needed for entrypoint

USER 1011

ADD entrypoint.sh /opt/janusgraph-0.2.0-hadoop2/
ADD hack/readiness.sh /tmp
ADD startup.sh /opt/janusgraph-0.2.0-hadoop2/
ADD hack/files /opt/janusgraph-0.2.0-hadoop2/files/
ADD aicoe-berkeleyje-lucene.properties /opt/janusgraph-0.2.0-hadoop2/conf
#ADD configurations/gremlin-server/janusgraph-berkeleyje-server.properties /opt/janusgraph-0.2.0-hadoop2/conf/gremlin-server/server.properties
#ADD configurations/gremlin-server/gremlin-server.yaml /opt/janusgraph-0.2.0-hadoop2/conf/gremlin-server/gremlin-server.yaml

EXPOSE 8080
EXPOSE 8182
EXPOSE 8184

VOLUME [ "/opt/janusgraph-${JG_VER}/data", "/opt/janusgraph-${JG_VER}/log" ]

ENTRYPOINT ["/opt/janusgraph-0.2.0-hadoop2/entrypoint.sh"]

CMD ["/bin/bash", "-e", "/opt/janusgraph-0.2.0-hadoop2/startup.sh"]

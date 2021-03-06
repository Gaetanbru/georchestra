FROM jetty:9.2-jre7

ENV XMS=1536M XMX=8G

RUN echo "deb http://httpredir.debian.org/debian jessie main contrib" > /etc/apt/sources.list \
    && echo "deb http://security.debian.org/ jessie/updates main contrib" >> /etc/apt/sources.list \
    && echo "ttf-mscorefonts-installer msttcorefonts/accepted-mscorefonts-eula select true" | debconf-set-selections \
    && apt-get update \
    && apt-get install -y ttf-mscorefonts-installer \
    && apt-get clean \
    && apt-get autoremove -y \
    && rm -rf /var/lib/apt/lists/*

RUN java -jar "$JETTY_HOME/start.jar" --add-to-startd=jmx,jmx-remote,stats,jndi

ADD . /

RUN mkdir /mnt/geoserver_datadir /mnt/geoserver_geodata /mnt/geoserver_tiles && \
    chown jetty:jetty /etc/georchestra /mnt/geoserver_datadir /mnt/geoserver_geodata /mnt/geoserver_tiles

VOLUME [ "/mnt/geoserver_datadir", "/mnt/geoserver_geodata", "/mnt/geoserver_tiles", "/tmp", "/run/jetty" ]

RUN sed -i 's/threads.max=200/threads.max=50/g' /var/lib/jetty/start.d/server.ini

CMD ["sh", "-c", "exec java -Djava.io.tmpdir=/tmp/jetty \
-Dgeorchestra.datadir=/etc/georchestra \
-Dgeofence.dir=/etc/georchestra/geoserver/geofence \
-DGEOSERVER_DATA_DIR=/mnt/geoserver_datadir \
-DGEOWEBCACHE_CACHE_DIR=/mnt/geoserver_tiles \
-DENABLE_JSONP=true \
-Dhttps.protocols=TLSv1,TLSv1.1,TLSv1.2 \
-Dorg.geotools.image.reduceWarpAffine=false \
-Xms$XMS -Xmx$XMX \
-XX:SoftRefLRUPolicyMSPerMB=36000 \
-XX:+UseParallelGC \
-XX:-UsePerfData \
${JAVA_OPTIONS} \
-jar /usr/local/jetty/start.jar" ]

FROM ubuntu:18.04
ENV RSKJ_VERSION='PAPYRUS-2.0.1'

RUN apt-get update -y && \
    apt-get install -y git curl gnupg software-properties-common openjdk-8-jdk=8u252-b09-1~18.04 && \
    rm -rf /var/lib/apt/lists/* && \
    apt-get autoremove -y && \
    apt-get clean

RUN gpg --keyserver https://secchannel.rsk.co/release.asc --recv-keys 1A92D8942171AFA951A857365DECF4415E3B8FA4
RUN gpg --finger 1A92D8942171AFA951A857365DECF4415E3B8FA4
RUN groupadd --gid 888 rsk && useradd rsk -d /var/lib/rsk -s /sbin/nologin --uid=888 --gid=888
WORKDIR /home/rsk
RUN git clone --branch ${RSKJ_VERSION} https://github.com/rsksmart/rskj.git --single-branch --depth 1
WORKDIR /home/rsk/rskj
#RUN gpg --verify SHA256SUMS.asc
#RUN sha256sum --check SHA256SUMS.asc
RUN ./configure.sh
RUN ./gradlew --no-daemon clean build -x test
RUN chown -R rsk *
#RUN sha256sum rskj-core/build/libs/*

# RSKJ config
WORKDIR /rsk-node
RUN chown rsk ./
RUN mkdir db
COPY node.conf  ./
COPY logback.xml  ./
RUN ln -s /home/rsk/rskj/rskj-core/build/libs/rskj-core-*-all.jar ./rsk.jar
RUN chown -R rsk /rsk-node/*
RUN mkdir -p /var/log/rsk
RUN chown rsk /var/log/rsk

EXPOSE 4444
EXPOSE 50505

CMD java -Xss4M -Drsk.conf.file=/rsk-node/node.conf -Dlogback.configurationFile=/rsk-node/logback.xml -cp /rsk-node/rsk.jar co.rsk.Start 2>&1

FROM primedio/package-java-openjdk-1.8:master

WORKDIR /root

COPY src/ src/
COPY build.sbt build.sbt
COPY lib/ lib/
#COPY store/ store/

RUN \
  sbt clean package \
# HACK:  We need to trigger the download of sbt-launch.jar which is a diff version than default sbt install
  && java -Djava.security.egd=file:/dev/./urandom -jar lib/sbt-launch.jar exit

RUN \
  mkdir -p /root/logs

ENV LOGS_HOME=/root/logs

ENV \
  STORE_HOME=/root/store

RUN \
  ln -s /root/volumes/source.ml/prediction.ml/pmml/store

COPY run run

EXPOSE 9040 

CMD ["supervise", "."]

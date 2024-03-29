FROM primedio/package-tensorflow-serving-1.0:master

WORKDIR /root

COPY lib/ lib/

RUN \
  cd ~/lib/jni \
  && ln -s ~/lib/jni/libtensorflow_jni-cpu.so libtensorflow_jni.so

RUN \
  mkdir -p /root/logs

ENV \
  LOGS_HOME=/root/logs

RUN \
  echo "deb https://dl.bintray.com/sbt/debian /" | tee -a /etc/apt/sources.list.d/sbt.list \
  && apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 2EE0EA64E40A89B84B2DF73499E82A75642AC823 \
  && apt-get update \
  && apt-get install -y sbt

RUN \
  sbt clean clean-files

RUN \
  wget http://apache.mirrors.tds.net/maven/maven-3/3.3.9/binaries/apache-maven-3.3.9-bin.tar.gz \
  && tar -xvzf apache-maven-3.3.9-bin.tar.gz \
  && rm apache-maven-3.3.9-bin.tar.gz

ENV \
  MAVEN_HOME=/root/apache-maven-3.3.9/

ENV \
  PATH=$MAVEN_HOME/bin:$PATH

COPY src/ src/
COPY build.sbt build.sbt
COPY client/ client/

RUN \
  cd client \
  && /root/apache-maven-3.3.9/bin/mvn clean install \
  && cp target/tensorflow-prediction-client-1.0-SNAPSHOT.jar ../lib

RUN \
  sbt clean package 

COPY run run

RUN \
  mkdir -p /root/tensorboard 

ENV \
  STORE_HOME=/root/store

RUN \
  ln -s /root/volumes/source.ml/prediction.ml/tensorflow/store

ENV \
  TF_CPP_MIN_LOG_LEVEL=0

# Must carry this over in jupyter PYTHONVARS or whatever
# import os
# os.environ['TF_CPP_MIN_LOG_LEVEL']='0'
# import tensorflow as tf

EXPOSE 5678 6006 9000 9001 9002 9040 

CMD ["supervise", "."]

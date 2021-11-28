FROM --platform=x86_64 centos:7

RUN mkdir -p /opt/lagou/software
RUN mkdir -p /opt/lagou/servers

RUN yum update -y
RUN yum install java-1.8.0-openjdk -y
RUN yum install which -y
RUN yum install vim -y
RUN yum install openssh-server openssh-clients -y

ENV JAVA_HOME /usr/lib/jvm/jre-1.8.0-openjdk
ENV JRE_HOME /usr/lib/jvm/jre
ENV CLASSPATH ${JAVA_HOME}/lib:${JRE_HOME}/lib
ENV PATH ${PATH}:${JAVA_HOME}/bin
ENV HADOOP_HOME /opt/lagou/servers/hadoop
ENV PATH ${PATH}:${HADOOP_HOME}/bin:${HADOOP_HOME}/sbin

RUN yum install wget -y

RUN wget https://dlcdn.apache.org/hadoop/common/hadoop-2.10.1/hadoop-2.10.1.tar.gz && \
    tar -zxvf hadoop-2.10.1.tar.gz
RUN mv hadoop-2.10.1 /opt/lagou/servers/hadoop


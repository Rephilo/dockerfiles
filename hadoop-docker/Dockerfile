FROM --platform=x86_64 centos:7

ARG NAMENODE=172.17.0.2

#创建文件夹
RUN mkdir -p /opt/lagou/software
RUN mkdir -p /opt/lagou/servers

#安装一些用到的包
RUN yum update -y
RUN yum install java-1.8.0-openjdk -y
RUN yum install which -y
RUN yum install vim -y
RUN yum install openssh-server openssh-clients -y

#配置环境变量
ENV JAVA_HOME /usr/lib/jvm/jre-1.8.0-openjdk
ENV JRE_HOME /usr/lib/jvm/jre
ENV CLASSPATH ${JAVA_HOME}/lib:${JRE_HOME}/lib
ENV PATH ${PATH}:${JAVA_HOME}/bin
ENV HADOOP_HOME /opt/lagou/servers/hadoop
ENV PATH ${PATH}:${HADOOP_HOME}/bin:${HADOOP_HOME}/sbin

#下载Hadoop
RUN yum install wget -y

RUN wget https://dlcdn.apache.org/hadoop/common/hadoop-2.10.1/hadoop-2.10.1.tar.gz && \
    tar -zxvf hadoop-2.10.1.tar.gz
RUN mv hadoop-2.10.1 /opt/lagou/servers/hadoop

#配置hadoop-env.sh文件的JDK
RUN sed -i '/JAVA_HOME=/d' ${HADOOP_HOME}/etc/hadoop/hadoop-env.sh
RUN sed -i -e "1i export JAVA_HOME=${JAVA_HOME}" ${HADOOP_HOME}/etc/hadoop/hadoop-env.sh

#指定NameNode节点以及数据存储目录(修改core-site.xml)
RUN sed -i "/<configuration>/a<property><name>fs.defaultFS</name><value>hdfs://${NAMENODE}:9000</value></property><property><name>hadoop.tmp.dir</name><value>/opt/lagou/servers/hadoop/data/tmp</value></property>"  ${HADOOP_HOME}/etc/hadoop/core-site.xml
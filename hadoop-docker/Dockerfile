MAINTAINER Rephilo
FROM ubuntu:lunar

ENV NAMENODE=namenode
ENV DATANODE=datanode
ENV RESOURCEMANAGER=resourcemanager

#创建文件夹
RUN mkdir -p /opt/hadoop

#安装一些用到的包
RUN apt update -y
RUN apt install ssh openjdk-8-jre -y

#配置环境变量
ENV HADOOP_VERSION=3.3.1
ENV JAVA_HOME /usr/lib/jvm/java-1.8.0-openjdk-arm64/
ENV JRE_HOME $JAVA_HOME/jre
ENV CLASSPATH ${JAVA_HOME}/lib:${JRE_HOME}/lib
ENV PATH ${PATH}:${JAVA_HOME}/bin
ENV HADOOP_HOME /opt/hadoop
ENV PATH ${PATH}:${HADOOP_HOME}/bin:${HADOOP_HOME}/sbin
ENV PDSH_RCMD_TYPE ssh

WORKDIR $HADOOP_HOME

#下载Hadoop
RUN apt install wget -y

RUN wget https://archive.apache.org/dist/hadoop/common/hadoop-${HADOOP_VERSION}/hadoop-${HADOOP_VERSION}-aarch64.tar.gz && \
    tar -zxf hadoop-${HADOOP_VERSION}-aarch64.tar.gz -C $HADOOP_HOME && \
    rm hadoop-${HADOOP_VERSION}-aarch64.tar.gz

#启动ssh
RUN ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa && \
    cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys && \
    chmod 0600 ~/.ssh/authorized_keys && \
    echo "StrictHostKeyChecking no" >> /etc/ssh/ssh_config

#HDFS集群配置
#配置hadoop-env.sh文件的JDK
RUN sed -i '/JAVA_HOME=/d' ${HADOOP_HOME}/etc/hadoop/hadoop-env.sh
RUN sed -i -e "1i export JAVA_HOME=${JAVA_HOME}" ${HADOOP_HOME}/etc/hadoop/hadoop-env.sh

#指定NameNode节点以及数据存储目录(修改core-site.xml)
RUN sed -i "/<configuration>/a<property><name>fs.defaultFS</name><value>hdfs://${NAMENODE}:9000</value></property><property><name>hadoop.tmp.dir</name><value>/opt/lagou/servers/hadoop/data/tmp</value></property>"  ${HADOOP_HOME}/etc/hadoop/core-site.xml

#指定datanode从节点
RUN sed -i '' ${HADOOP_HOME}/etc/hadoop/slaves

#MapReduce集群配置
#配置mapred-env.sh文件的JDK
RUN sed -i '/JAVA_HOME=/d' ${HADOOP_HOME}/etc/hadoop/mapred-env.sh
RUN sed -i -e "1i export JAVA_HOME=${JAVA_HOME}" ${HADOOP_HOME}/etc/hadoop/mapred-env.sh

#指定MapReduce计算框架运行Yarn资源调度框架(修改mapred-site.xml)
RUN mv ${HADOOP_HOME}/etc/hadoop/mapred-site.xml.template ${HADOOP_HOME}/etc/hadoop/mapred-site.xml
RUN sed -i '/<configuration>/a<property><name>mapreduce.framework.name</name><value>yarn</value></property>'  ${HADOOP_HOME}/etc/hadoop/mapred-site.xml

#Yarn集群配置
#配置yarn-env.sh文件的JDK
RUN sed -i '/JAVA_HOME=/d' ${HADOOP_HOME}/etc/hadoop/yarn-env.sh
RUN sed -i -e "1i export JAVA_HOME=${JAVA_HOME}" ${HADOOP_HOME}/etc/hadoop/yarn-env.sh

#指定ResourceMnager的master节点信息(修改yarn-site.xml)
RUN sed -i "/<configuration>/a<property><name>yarn.resourcemanager.hostname</name><value>${RESOURCEMANAGER}</value></property>"  ${HADOOP_HOME}/etc/hadoop/yarn-site.xml
RUN sed -i "/<configuration>/i<property><name>yarn.nodemanager.aux-services</name><value>mapreduce_shuffle</value></property>" ${HADOOP_HOME}/etc/hadoop/yarn-site.xml

#修改用户组
RUN chown -R root:root /opt/hadoop

EXPOSE 9000 8088 9870
ENTRYPOINT ["bin/docker-startup.sh"]
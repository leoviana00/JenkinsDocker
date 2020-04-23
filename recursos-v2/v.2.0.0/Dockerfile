FROM jenkins/jenkins:2.233

LABEL Author="Leonardo Viana Pereira" 

ARG master_image_version="v.2.0.0"
ENV master_image_version $master_image_version

USER jenkins
# Plugin Setup
COPY plugins.txt /usr/share/jenkins/ref/plugins.txt
RUN /usr/local/bin/install-plugins.sh < /usr/share/jenkins/ref/plugins.txt
#COPY plugins.sh /usr/local/bin/plugins.sh
#COPY install-plugins.sh /usr/local/bin/install-plugins.sh
# Auto Configuration Scripts
COPY groovy/* /usr/share/jenkins/ref/init.groovy.d/
COPY config/*.properties ${JENKINS_HOME}/config/
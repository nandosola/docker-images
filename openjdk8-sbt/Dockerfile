FROM alpine:edge
MAINTAINER Nando Sola <nando@graphenedb.com>

####################################################################################
# Usage:
#   docker run -v /path/to/scala/repo:/app -ti graphenedb/openjdk8-sbt
#
####################################################################################
# Inspired by
# https://github.com/cloudbees/java-build-tools-dockerfile/blob/master/Dockerfile
# https://github.com/William-Yeh/docker-sbt/blob/master/Dockerfile
# https://github.com/docker-library/openjdk/blob/master/8-jdk/alpine/Dockerfile
####################################################################################

ENV JAVA_VERSION 8u111
ENV JAVA_ALPINE_VERSION 8.111.14-r1

ENV LANG C.UTF-8
ENV ARCH amd64

# add a simple script that can auto-detect the appropriate JAVA_HOME value
# based on whether the JDK or only the JRE is installed
RUN { \
    echo '#!/bin/sh'; \
    echo 'set -e'; \
    echo; \
    echo 'dirname "$(dirname "$(readlink -f "$(which javac || which java)")")"'; \
  } > /usr/local/bin/docker-java-home \
  && chmod +x /usr/local/bin/docker-java-home

ENV JAVA_HOME /usr/lib/jvm/java-1.8-openjdk
ENV PATH $PATH:/usr/lib/jvm/java-1.8-openjdk/jre/bin:/usr/lib/jvm/java-1.8-openjdk/bin

RUN set -x \
  && apk add --no-cache \
  openjdk8="$JAVA_ALPINE_VERSION" \
  && [ "$JAVA_HOME" = "$(docker-java-home)" ]

# SBT
ENV SBT_VERSION 0.13.6
ENV SBT_JAR     https://repo.typesafe.com/typesafe/ivy-releases/org.scala-sbt/sbt-launch/$SBT_VERSION/sbt-launch.jar

# 'gosu' wil be used instead of 'sudo', as recommended by:
# http://docs.docker.com/articles/dockerfile_best-practices/
ENV GOSU_VERSION    1.10
ENV GOSU_SHA256_SUM 5b3b03713a888cee84ecbf4582b21ac9fd46c3d935ff2d7ea25dd5055d302d3c

#==================================================
# Miscellaneous packages
# iproute and telnet are not part of the base dist
# tree is convenient for troubleshooting builds
#==================================================
RUN apk add --no-cache  \
      ca-certificates \
      tar bzip2 zip unzip \
      wget curl \
      bash \
      secure-delete \
      git

# ========================================================================
# Download a precompiled binary for gosu (https://github.com/tianon/gosu)
# ========================================================================
RUN wget -O /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$ARCH" && \
    echo "$GOSU_SHA256_SUM  /usr/local/bin/gosu" | sha256sum -c - && \
    chmod 4755 /usr/local/bin/gosu

# Fake sudo, as a wrapper for gosu
ADD bin/fakesudo /usr/local/bin/sudo
RUN chmod 755 /usr/local/bin/sudo

#==============================================
# Add 'jenkins' user to run tests
# This adds compatibility with our CI process
#==============================================
RUN addgroup -g 1000 jenkins && \
    adduser -s /bin/bash -h /home/jenkins -D -u 1000 -G jenkins jenkins

#==========================
# Get SBT and dependencies
#==========================
ADD  $SBT_JAR /usr/local/bin/sbt-launch.jar
COPY bin/sbt.sh /usr/local/bin/sbt
RUN  chmod a+x /usr/local/bin/sbt &&\
     chmod a+r /usr/local/bin/sbt-launch.jar

RUN echo "==> fetch all sbt jars from Maven repo..."       && \
    echo "==> [CAUTION] this may take several minutes!!!"  && \
    su -c '/usr/local/bin/sbt version' - jenkins

#=============
# Final setup
#=============
USER jenkins

VOLUME [ "/app" ]
WORKDIR /app

ENTRYPOINT ["/usr/local/bin/sbt"]

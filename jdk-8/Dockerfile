FROM openjdk:8-jdk-alpine

ARG MAVEN_VERSION=3.5.0
ARG SHA=beb91419245395bd69a4a6edad5ca3ec1a8b64e41457672dc687c173a495f034
ARG BASE_URL=https://apache.osuosl.org/maven/maven-3/${MAVEN_VERSION}/binaries
ENV PATH=/opt/maven/bin/:$PATH
ENV BUILDER_VERSION 1.0
ENV MAVEN_HOME /usr/share/maven

LABEL io.k8s.description="Platform for building Java applications with maven" \
      io.k8s.display-name="Java builder 1.0" \
      io.openshift.expose-services="8080:http" \
      io.openshift.tags="builder,maven-3,java" \
      io.openshift.s2i.scripts-url=image:///usr/local/sti

	  
RUN mkdir -p /usr/share/maven /usr/share/maven/ref \
		&& curl -fsSL -o /tmp/apache-maven.tar.gz ${BASE_URL}/apache-maven-$MAVEN_VERSION-bin.tar.gz \
		&& echo "${SHA}  /tmp/apache-maven.tar.gz" | sha256sum -c - \
		&& tar -xzf /tmp/apache-maven.tar.gz -C /usr/share/maven --strip-components=1 \
		&& rm -f /tmp/apache-maven.tar.gz \ 
		&& ln -s /usr/share/maven/bin/mvn /usr/bin/mvn  \
		&& chmod +x /usr/share/maven/conf/settings.xml  \
		&& mv /usr/share/maven/conf/settings.xml /usr/share/maven/conf/settings1.xml
		

# Copy maven settings
COPY  settings.xml /usr/share/maven/conf/ 

# Copy the sti scripts
COPY ./.sti/bin/ /usr/local/sti

RUN mkdir -p /opt/openshift && \
    mkdir -p /opt/app-root/source && chmod -R a+rwX /opt/app-root/source && \
    mkdir -p /opt/s2i/destination && chmod -R a+rwX /opt/s2i/destination && \
    mkdir -p /opt/app-root/src && chmod -R a+rwX /opt/app-root/src && \
    chown -R 1001:1001 /opt/openshift && \
    chmod -R a+rwX /usr/local/sti/assemble && \
    chmod -R a+rwX /usr/local/sti/run && \
    chmod -R a+rwX /usr/local/sti/save-artifacts && \
    chmod -R a+rwX /usr/local/sti/usage && \
    chmod +x /usr/local/sti/assemble /usr/local/sti/run /usr/local/sti/save-artifacts /usr/local/sti/usage

# This default user is created in the openshift/base-centos7 image
USER 1001

# Set the default port for applications built using this image
EXPOSE 8080

# Set the default CMD for the image
CMD ["usage"]

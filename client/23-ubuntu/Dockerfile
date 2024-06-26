# Dockerfile Bareos client/file daemon
FROM ubuntu:focal

#LABEL maintainer="barcus@tou.nu"

ARG BUILD_DATE
ARG NAME
ARG VCS_REF
ARG VERSION

LABEL org.label-schema.schema-version="1.0" \
      org.label-schema.build-date=$BUILD_DATE \
      org.label-schema.name=$NAME \
      org.label-schema.vcs-ref=$VCS_REF \
      org.label-schema.vcs-url="https://github.com/csongorhg/bareos" \
      org.label-schema.version=$VERSION

ENV BAREOS_DAEMON_USER bareos
ENV BAREOS_DAEMON_GROUP bareos
ENV DEBIAN_FRONTEND noninteractive
ENV BAREOS_KEY http://download.bareos.org/current/xUbuntu_20.04/Release.key
ENV BAREOS_REPO http://download.bareos.org/current/xUbuntu_20.04/

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

RUN apt-get update -qq \
 && apt-get -qq -y install --no-install-recommends curl tzdata gnupg gosu python3-pip \
 && curl -Ls $BAREOS_KEY -o /tmp/bareos.key \
 && apt-key --keyring /etc/apt/trusted.gpg.d/breos-keyring.gpg \
    add /tmp/bareos.key \
 && echo "deb $BAREOS_REPO /" > /etc/apt/sources.list.d/bareos.list \
 && apt-get update -qq \
 && apt-get install -qq -y --no-install-recommends \
    bareos-client mysql-client postgresql-client bareos-tools bareos-vmware-plugin \
 && apt-get clean \
 && pip install pyvmomi \
 && rm -rf /var/lib/apt/lists/* \
 && apt-get install python3-pip \
 && pip3 install urllib3 requests


COPY docker-entrypoint.sh /docker-entrypoint.sh
RUN chmod a+x /docker-entrypoint.sh

RUN tar czf /bareos-fd.tgz /etc/bareos/bareos-fd.d

EXPOSE 9102

ENTRYPOINT ["/docker-entrypoint.sh"]
CMD ["/usr/sbin/bareos-fd", "-f"]

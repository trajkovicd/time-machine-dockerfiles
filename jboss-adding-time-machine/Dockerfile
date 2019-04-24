FROM jboss/wildfly:latest

LABEL vendor="SolutionSoft Systems, Inc"
LABEL maintainer="kzhao@solution-soft.com"

ENV LICHOST		"172.0.0.1"
ENV LICPORT		"57777"
ENV LICPASS		"docker"

ENV TM_VERSION		"12.9R3"

ENV TMAGENT_DATADIR	"/tmdata/data"
ENV TMAGENT_LOGDIR	"/tmdata/log"

# User root user to install software
USER root

COPY py/get-pip.py /tmp

# - install supervisor and TM

RUN mkdir -p /tmdata \
&&  python /tmp/get-pip.py \
&&  pip install --no-cache-dir supervisor \
&&  (cd /tmp; \
     curl -O ftp://ftp.solution-soft.com/pub/tm/linux/redhat/64bit/tm_linux_2.6.up_x86_64-$TM_VERSION.tgz; \
     tar -xzf tm_linux_2.6.up_x86_64-$TM_VERSION.tgz; \
     ./ssstm_install.sh tm_linux_2.6.up_x86_64-$TM_VERSION.rpm) \
&&  adduser -d /var/tmp -M -r time-traveler \
&&  rm -rf /tmp/*

# - install tmlicd

COPY bin/tmlicd /usr/local/bin

# - install supervisord config files

COPY config /

EXPOSE 7800
VOLUME /tmdata

ENTRYPOINT ["/entrypoint.sh"]
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisord.conf"]
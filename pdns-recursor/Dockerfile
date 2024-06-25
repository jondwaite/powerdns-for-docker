FROM python:3-slim-bookworm
LABEL maintainer="Jon Waite <jondwaite@hotmail.com>"

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y --no-install-recommends curl gnupg apt-transport-https \
  && install -d /etc/apt/keyrings; curl https://repo.powerdns.com/FD380FBB-pub.asc | tee /etc/apt/keyrings/rec-50-pub.asc

COPY src/ /

RUN apt-get update && apt-get install -y --no-install-recommends \
  pdns-recursor \
  && apt-get purge -y --auto-remove curl gnupg \
  && rm -rf /var/lib/apt/lists/*

RUN pip3 install envtpl \
  && rm -rf ~/.cache/*

ENV VERSION=5.0 \
  PDNS_setuid=pdns \
  PDNS_setgid=pdns \
  PDNS_daemon=no \
  PDNS_local_port=53 \
  PDNS_local_address=0.0.0.0 \
  PDNS_allow_from=0.0.0.0/0 \
  PDNS_webserver=yes \
  PDNS_webserver_address=0.0.0.0 \
  PDNS_api_readonly=yes

EXPOSE 53 53/udp 8082

COPY recursor.conf.jinja2 /etc/powerdns/
COPY docker-entrypoint.sh /

HEALTHCHECK --interval=10s --retries=3 CMD /usr/bin/rec_control ping

ENTRYPOINT [ "bash", "/docker-entrypoint.sh" ]
CMD [ "/usr/sbin/pdns_recursor" ]

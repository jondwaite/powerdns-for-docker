FROM python:3-slim-bookworm
LABEL maintainer="Jon Waite <jondwaite@hotmail.com>"

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y --no-install-recommends curl gnupg apt-transport-https \
  && install -d /etc/apt/keyrings; curl https://repo.powerdns.com/FD380FBB-pub.asc | tee /etc/apt/keyrings/auth-49-pub.asc

COPY src/ /

RUN apt-get update && apt-get install -y --no-install-recommends \
  pdns-server \
  pdns-backend-mysql \
  pdns-backend-pgsql \
  pdns-tools \
  postgresql-client \
  default-mysql-client \
  && apt-get purge -y --auto-remove curl gnupg \
  && rm -rf /var/lib/apt/lists/*

RUN pip3 install envtpl \
  && rm -rf ~/.cache/*

ENV VERSION=4.9 \
  PDNS_setuid=pdns \
  PDNS_setgid=pdns \
  PDNS_guardian=yes \
  PDNS_webserver=yes \
  PDNS_webserver_address=0.0.0.0 \
  PDNS_webserver_port=8081

EXPOSE 53 53/udp 8081

COPY pdns.conf.jinja2 /etc/powerdns/
COPY docker-entrypoint.sh /

ENTRYPOINT [ "bash", "/docker-entrypoint.sh" ]
CMD [ "/usr/sbin/pdns_server" ]

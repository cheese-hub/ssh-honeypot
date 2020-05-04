FROM ubuntu:18.04

RUN apt-get update && apt-get -y install openssh-client supervisor

ADD supervisord.conf /etc/supervisor/conf.d/supervisord.conf

CMD ["/usr/bin/supervisord"]

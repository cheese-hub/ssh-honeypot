FROM ubuntu:18.04

# software dependencies

RUN apt-get update && apt-get -y install zlib1g-dev libssl1.0-dev wget tar unzip gcc make rsyslog net-tools git supervisor openssh-server

USER root

# temporary staging directories 

RUN mkdir /tmp/honeypot-tmp
RUN mkdir /tmp/longtail-tmp

ADD . /tmp/honeypot-tmp

# copy over config files

RUN yes | cp -f /tmp/honeypot-tmp/sshd_config /etc/ssh/
RUN yes | cp -f /tmp/honeypot-tmp/sshd_config /usr/local/etc/
RUN yes | cp -f /tmp/honeypot-tmp/rsyslog.conf /etc/

# rsyslog seems to fail without this directory present

RUN mkdir /var/lib/rsyslog

# sshd privileged run dir

RUN mkdir /run/sshd

# dummy rc.local file so ssh service restarts can be added here
RUN touch /etc/rc.local

# install LongTail ssh configs for ports 22 and 2222

RUN cd /tmp/longtail-tmp && git clone https://github.com/wedaa/LongTail-Log-Analysis.git
RUN  cd /tmp/longtail-tmp/LongTail-Log-Analysis && chmod +x install_openssh.sh && ./install_openssh.sh 

RUN chmod 777 /usr/local/sbin/sshd-22 /usr/local/etc/sshd_config-22 /usr/local/sbin/sshd-2222 /usr/local/etc/sshd_config-2222 

RUN yes | cp -f /dev/null /var/log/messages

# use supervisor to start ssh and rsyslog

ADD supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# create a user that we will try to ssh as

RUN useradd -d /home/term -m -s /bin/bash term
RUN echo 'term:term' | chpasswd 

# add user to users group

RUN usermod -a -G users term

CMD ["/usr/bin/supervisord"]

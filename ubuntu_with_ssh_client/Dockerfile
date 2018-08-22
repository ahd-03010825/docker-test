FROM ubuntu:16.04
RUN apt-get update
RUN apt-get upgrade -y
RUN apt-get install openssh-server -y
RUN apt-get install openssh-client -y
CMD mkdir -p /var/run/sshd && chmod 0755 /var/run/sshd && /usr/sbin/sshd -D

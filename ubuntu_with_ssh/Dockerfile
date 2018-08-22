FROM ubuntu:16.04
RUN apt-get update
RUN apt-get upgrade -y
RUN apt-get install openssh-server vim -y
RUN useradd kinoue
RUN echo 'kinoue:mizuho0' | chpasswd
EXPOSE 22
CMD mkdir -p /var/run/sshd && chmod 0755 /var/run/sshd && /usr/sbin/sshd -D

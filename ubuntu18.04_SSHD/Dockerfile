FROM ubuntu:18.04

MAINTAINER Ubuntu 18.04 Base System ver.1 20181106 "neilink@zoho.com"  

RUN apt-get update &&  DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends vim\ 
 apt-utils\
 gosu\ 
 openssh-server\
 iputils-ping\ 
 build-essential\
 wget\
 curl\
 tree\
 net-tools\
 links\
 make\
 iproute2  

RUN  mkdir /var/run/sshd && \
 echo 'root:asaburu' | chpasswd && \
 sed -ri 's/^#?PermitRootLogin\s+.*/PermitRootLogin yes/' /etc/ssh/sshd_config && \
 sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd && \
 mkdir /root/.ssh

RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

EXPOSE 22

CMD ["/usr/sbin/sshd", "-D"]



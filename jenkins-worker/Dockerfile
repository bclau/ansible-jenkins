FROM ubuntu:latest
MAINTAINER Claudiu Belu

# Install Essentials
RUN apt-get update -y

# Install Packages
RUN apt-get install -y openssh-server wget git python python-pip python3 python3-pip openjdk-8-jdk
RUN pip install zuul tox

# newer setuptools have issues with external dependencies (github)
RUN pip install -U pip setuptools==36.1.0

# gen dummy keys, centos doesn't autogen them like ubuntu does
RUN /usr/bin/ssh-keygen -A

# Set SSH Configuration to allow remote logins without /proc write access
RUN sed -ri 's/^session\s+required\s+pam_loginuid.so$/session optional pam_loginuid.so/' /etc/pam.d/sshd

# Create Jenkins User
RUN useradd jenkins -m -s /bin/bash

# Add public key for Jenkins login
RUN mkdir /home/jenkins/.ssh
COPY /files/authorized_keys /home/jenkins/.ssh/authorized_keys
RUN chown -R jenkins /home/jenkins
RUN chgrp -R jenkins /home/jenkins
RUN chmod 600 /home/jenkins/.ssh/authorized_keys
RUN chmod 700 /home/jenkins/.ssh

# Create the sshd folder and set the appropriate priviledges.
RUN mkdir -p /var/run/sshd
RUN chmod 0755 /var/run/sshd

# Add the jenkins user to sudoers
RUN echo "jenkins  ALL=(ALL)  ALL" >> etc/sudoers

# Set Name Servers
COPY /files/resolv.conf /etc/resolv.conf
COPY /files/hosts /etc/hosts

# Expose SSH port and run SSHD
EXPOSE 22
CMD ["/usr/sbin/sshd","-D"]

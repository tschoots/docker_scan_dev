FROM centos:latest


RUN yum  update -y && yum install -y wget \
    && yum groupinstall -y  "X Window System" "Desktop" "Desktop Platform" \
    && yum install -y gdm openssh-server \
    && cd /opt \
    && wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u60-b27/jdk-8u60-linux-x64.tar.gz" \
    && tar -xvf jdk-8u60-linux-x64.tar.gz \
    && cd /opt/jdk1.8.0_60/  \
    && alternatives --install /usr/bin/java java /opt/jdk1.8.0_60/bin/java 2 \
    && alternatives --set java /opt/jdk1.8.0_60/bin/java  \
    && alternatives --install /usr/bin/jar jar /opt/jdk1.8.0_60/bin/jar 2 \
    && alternatives --install /usr/bin/javac javac /opt/jdk1.8.0_60/bin/javac 2 \
    && alternatives --set jar /opt/jdk1.8.0_60/bin/jar \
    && alternatives --set javac /opt/jdk1.8.0_60/bin/javac 


RUN mkdir /var/run/sshd \
    && echo 'root:screencast' | chpasswd \
    && sed -i 's/PermitRootLogin without-password/PermitRootLogin yes/' /etc/ssh/sshd_config \
    && sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd


#ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile

# this port we want to run ssh on
EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]

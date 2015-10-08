# Black Duck scan container dev container. 
#
# VERSION               0.0.2
#
# author : Ton Schoots
#
# status 8-10-2015
#        Image installs go from source has to be changed bij pulling from gitHub
#        GoClipse plugin isn't configured automatically manual steps have to perfromed.




FROM ubuntu:14.04
MAINTAINER Ton Schoots <tschoots@blackducksoftware.com>

RUN apt-get update \
    && apt-get install -y wget dbus libxtst6 git curl \
    && dbus-uuidgen > /var/lib/dbus/machine-id \
    && git config --global user.email "ton@maiastra.com" \
    && git config --global user.name "Ton Schoots"


# install oracle java
RUN cd /opt \
    && wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u60-b27/jdk-8u60-linux-x64.tar.gz" \
    && tar -xvf jdk-8u60-linux-x64.tar.gz \
    && cd /opt/jdk1.8.0_60/  \
    && update-alternatives --install /usr/bin/java java /opt/jdk1.8.0_60/bin/java 2 \
    && update-alternatives --set java /opt/jdk1.8.0_60/bin/java  \
    && update-alternatives --install /usr/bin/jar jar /opt/jdk1.8.0_60/bin/jar 2 \
    && update-alternatives --install /usr/bin/javac javac /opt/jdk1.8.0_60/bin/javac 2 \
    && update-alternatives --set jar /opt/jdk1.8.0_60/bin/jar \
    && update-alternatives --set javac /opt/jdk1.8.0_60/bin/javac \
    && rm -rf /opt/jdk-8u60-linux-x64.tar.gz


# install eclipse
RUN cd /tmp \
    && wget http://download.eclipse.org/technology/epp/downloads/release/mars/R/eclipse-java-mars-R-linux-gtk-x86_64.tar.gz \
    && tar -xvf eclipse-java-mars-R-linux-gtk-x86_64.tar.gz \
    && mv eclipse /root  \
    && cd /root/eclipse 
#    && ./eclipse \
#       -clean -purgeHistory \
#       -noSplash \
#       -repository \
#       http://goclipse.github.io/releases/ \
#       -installIUs \
#       v201508251600.GoClipse \
#       -vmargs -Declipse.p2.mirrors=true -Djava.net.preferIPv4Stack=true

# install go environment
RUN apt-get update && apt-get install -y --no-install-recommends \
                g++ \
                gcc \
                libc6-dev \
                make \
     && rm -rf /var/lib/apt/lists/* 

ENV GOLANG_VERSION 1.5.1
ENV GOLANG_DOWNLOAD_URL https://golang.org/dl/go$GOLANG_VERSION.linux-amd64.tar.gz
ENV GOLANG_DOWNLOAD_SHA1 46eecd290d8803887dec718c691cc243f2175fe0

RUN curl -fsSL "$GOLANG_DOWNLOAD_URL" -o golang.tar.gz \
     && echo "$GOLANG_DOWNLOAD_SHA1 golang.tar.gz" | sha1sum -c - \
     && tar -C /usr/local -xzf golang.tar.gz \
     && rm golang.tar.gz

ENV GOPATH /root/go
ENV PATH $GOPATH/bin:/usr/local/go/bin:$PATH

RUN echo "export GOPATH=$GOPATH" >> /root/.profile \
    && echo "export PATH=$PATH" >> /root/.profile

RUN mkdir -p $GOPATH/src/github.com/tschoots \
     && cd $GOPATH/src/github.com/tschoots \
     && git clone https://github.com/tschoots/scanner.git

RUN cd /root/go \
    && go get -u github.com/nsf/gocode \
    && go get -v ./... \
    && go get -u golang.org/x/tools/cmd/oracle 

RUN apt-get update && apt-get install -y openssh-server firefox
RUN mkdir /var/run/sshd
RUN echo 'root:blackduck' | chpasswd
RUN sed -i 's/PermitRootLogin without-password/PermitRootLogin yes/' /etc/ssh/sshd_config

# SSH login fix. Otherwise user is kicked off after login
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile

#before going on let's make sure we are fully updated.
RUN apt-get update

EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]

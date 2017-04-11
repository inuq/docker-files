FROM centos:centos6
MAINTAINER inuq

# Update installed Package.
RUN yum -y update && yum clean all

# Dev tools install.
RUN yum -y install git vim unzip ant && \
    yum clean all
RUN yum -y install automake bison bzip2 gcc gcc-c++ gdbm-devel git libffi-devel \
    libtool libxml2-devel libxslt-devel \
    make openssh openssh-clients openssh-server openssl openssl-devel passwd \
    patch readline readline-devel rsync sqlite-devel sudo tar tcl tcl-devel tk \
    tk-devel vim wget which zlib zlib-devel && \
    yum clean all

# Ant(ver1.8.x) install.
RUN yum -y remove ant && yum clean all
RUN wget http://archive.apache.org/dist/ant/binaries/apache-ant-1.8.4-bin.zip && \
unzip apache-ant-1.8.4-bin.zip && \
rm apache-ant-1.8.4-bin.zip && \
mv apache-ant-1.8.4 /opt/. && \
ln -s /opt/apache-ant-1.8.4 /opt/ant && \
ln -s /opt/ant/bin/ant /usr/bin/ant

# Set locale.
RUN yum reinstall -y glibc-common && yum clean all
ENV LANG ja_JP.UTF-8
ENV LANGUAGE ja_JP:ja
ENV LC_ALL ja_JP.UTF-8

############################### sshd ################################

# Create "Jenkins" User.
RUN useradd jenkins
RUN passwd -f -u jenkins

# Set up SSH.
RUN ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key -C '' -N ''
RUN ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key -C '' -N ''
RUN mkdir -p /home/jenkins/.ssh; chown jenkins /home/jenkins/.ssh; chmod 700 /home/jenkins/.ssh
ADD authorized_keys /home/jenkins/.ssh/authorized_keys

RUN chown jenkins /home/jenkins/.ssh/authorized_keys
RUN chmod 600 /home/jenkins/.ssh/authorized_keys

# Setup sudoers.
RUN echo "jenkins ALL=(ALL) ALL" >> /etc/sudoers.d/jenkins

# Set up SSHD config.
RUN sed -ri 's/#PermitRootLogin yes/PermitRootLogin no/g' /etc/ssh/sshd_config
RUN sed -ri 's/UsePAM yes/#UsePAM yes/g' /etc/ssh/sshd_config
RUN sed -ri 's/#UsePAM no/UsePAM no/g' /etc/ssh/sshd_config

# Init SSHD.
RUN /etc/init.d/sshd start
RUN /etc/init.d/sshd stop

# Start SSHD.
EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]

########################################## timezone ##############################################
# Setup timezone.
RUN cp -p /usr/share/zoneinfo/Japan /etc/localtime

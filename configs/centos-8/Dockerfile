FROM centos:8
RUN yum update -y
RUN yum install -y dnf-plugins-core
RUN yum config-manager --set-enabled PowerTools

# Install Shibboleth's recommended Service Provider repo as per
# https://shibboleth.net/downloads/service-provider/RPMS/
ADD configs/centos-8/shibboleth.repo /etc/yum.repos.d/shibboleth.repo

# Accept Shibboleth's GPG key
RUN rpm --import https://shibboleth.net/downloads/service-provider/RPMS/repomd.xml.key

# Install EPEL for fcgi-devel
RUN yum install -y epel-release

# Install required packages for building
RUN yum install -y \
  make \
  rpm-build \
  rpmdevtools \
  sudo \
  yum-utils \
  rsync \
  fcgi

# Make the build area available
RUN mkdir -p /app/build

# 1. Build
# 2. Test by installing/running
#    Remove any installed packages from previous docker-compose runs
# 3. Copy the RPMs back to the host volume
CMD /app/shibboleth-rebuild.sh && \
  yum remove -y shibboleth* && \
  yum install -y ~/rpmbuild/RPMS/x86_64/*.rpm && \
  /usr/lib64/shibboleth/shibauthorizer && \
  /usr/lib64/shibboleth/shibresponder && \
  mkdir -p /app/build/centos-8/ && \
  rsync --no-relative -vahu ~/rpmbuild/RPMS ~/rpmbuild/SRPMS /app/build/centos-8/

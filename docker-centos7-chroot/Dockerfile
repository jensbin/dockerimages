FROM centos:7
MAINTAINER Jens Binkert <jensbin+git@pm.me>

# To keep the image small, don't install docs with packages
RUN [ -e /etc/yum.conf ] && grep -q '^tsflags=' /etc/yum.conf && sed -i 's/^tsflags.*/tsflags=nodocs/' /etc/yum.conf || echo 'tsflags=nodocs' >> /etc/yum.conf
RUN     yum -y update && \
        yum -y install sudo && \
        yum -y clean all --enablerepo='*' && \
        rm -rf /var/cache/yum /var/lib/yum

RUN echo 'jens ALL=(ALL) NOPASSWD: ALL' > /etc/sudoers.d/jens

ENV SERVICE_USER=jens \
    SERVICE_UID=1000 \
    SERVICE_GROUP=jens \
    SERVICE_GID=1000 \
    SERVICE_HOMEDIR=/home/jens

RUN groupadd -g ${SERVICE_GID} ${SERVICE_GROUP} && \
    useradd -g ${SERVICE_UID} -d ${SERVICE_HOMEDIR}  --shell /bin/bash -u ${SERVICE_GID} ${SERVICE_USER}

USER ${SERVICE_USER}
WORKDIR ${SERVICE_HOMEDIR}


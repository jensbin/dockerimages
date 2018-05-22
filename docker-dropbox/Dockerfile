FROM debian:stretch
LABEL maintainer "Jens Binkert <jensbin+git@pm.me>"

ENV DEBIAN_FRONTEND=noninteractive \
    GPGKEY=1C61A2656FB57B7E4DE0F4C1FC918B335044912E

RUN sed -i.bak 's/stretch main/stretch main contrib/g' /etc/apt/sources.list && \
  apt-get update && \
  apt-get -y dist-upgrade && \
  apt-get install -y \
  ca-certificates \
  curl \
  dirmngr \
  gnupg \
  python-gpgme \
  file \
  libasound2 \
  libdbus-glib-1-2 \
  libgtk2.0-0 \
  libxrender1 \
  libx11-xcb1 \
  libxt6 \
  libxxf86vm1 \
  python-libxslt1 \
  xz-utils \
  --no-install-recommends \
  && rm -rf /var/lib/apt/lists/*

ENV LANG C.UTF-8

# Following 'How do I add or remove Dropbox from my Linux repository?' - https://www.dropbox.com/en/help/246
RUN cd /tmp && set -x \
  && echo 'deb http://linux.dropbox.com/debian jessie main' > /etc/apt/sources.list.d/dropbox.list \
  && apt-key adv --keyserver ha.pool.sks-keyservers.net --recv-keys "${GPGKEY}" \
  || apt-key adv --keyserver pool.sks-keyservers.net --recv-keys "${GPGKEY}" \
  || apt-key adv --keyserver keyserver.pgp.com --recv-keys "${GPGKEY}" \
  || apt-key adv --keyserver pgp.mit.edu --recv-keys "${GPGKEY}" \
  && apt-get -qqy update \
  # Note 'ca-certificates' dependency is required for 'dropbox start -i' to succeed
  && apt-get -qqy install dropbox \
  # Perform image clean up.
  && apt-get -qqy autoclean \
  && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* \
  # Create service account and set permissions.
  && groupadd dropbox \
  && useradd -m -d /dbox -c "Dropbox Daemon Account" -s /usr/sbin/nologin -g dropbox dropbox

# Dropbox is weird: it insists on downloading its binaries itself via 'dropbox
# start -i'. So we switch to 'dropbox' user temporarily and let it do its thing.
USER dropbox
RUN mkdir -p /dbox/.dropbox /dbox/.dropbox-dist /dbox/Dropbox /dbox/base \
  && echo y | dropbox start -i

# Switch back to root, since the run script needs root privs to chmod to the user's preferrred UID
USER root

# Dropbox has the nasty tendency to update itself without asking. In the processs it fills the
# file system over time with rather large files written to /dbox and /tmp. The auto-update routine
# also tries to restart the dockerd process (PID 1) which causes the container to be terminated.
RUN mkdir -p /opt/dropbox \
  # Prevent dropbox to overwrite its binary
  && mv /dbox/.dropbox-dist/dropbox-lnx* /opt/dropbox/ \
  && mv /dbox/.dropbox-dist/dropboxd /opt/dropbox/ \
  && mv /dbox/.dropbox-dist/VERSION /opt/dropbox/ \
  && rm -rf /dbox/.dropbox-dist \
  && install -dm0 /dbox/.dropbox-dist \
  # Prevent dropbox to write update files
  && chmod u-w /dbox \
  && chmod o-w /tmp \
  && chmod g-w /tmp \
  # Prepare for command line wrapper
  && mv /usr/bin/dropbox /usr/bin/dropbox-cli

# Install init script and dropbox command line wrapper
COPY run /dbox/
RUN chmod 555 /dbox/run
COPY dropbox /usr/bin/dropbox
RUN chmod 555 /usr/bin/dropbox

WORKDIR /dbox/Dropbox
EXPOSE 17500
VOLUME ["/dbox/.dropbox", "/dbox/Dropbox"]
ENTRYPOINT ["/dbox/run"]

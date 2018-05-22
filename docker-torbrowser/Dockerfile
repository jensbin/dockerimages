# tor browser
#
#
FROM debian:stretch
LABEL maintainer "Jens Binkert <jensbin+git@pm.me>"

ENV DEBIAN_FRONTEND=noninteractive TORVERSION=7.5.4 \
    HOME=/home/anon TORGPGKEY=EF6E286DDA85EA2A4BA7DE684E2C6E8793298290

RUN sed -i.bak 's/stretch main/stretch main contrib/g' /etc/apt/sources.list && \
  apt-get update && \
  apt-get -y dist-upgrade && \
  apt-get install -y \
  ca-certificates \
  curl \
  dirmngr \
  gnupg \
  file \
  libasound2 \
  libdbus-glib-1-2 \
  libgtk2.0-0 \
  libxrender1 \
  libx11-xcb1 \
  libxt6 \
  xz-utils \
  --no-install-recommends \
  && rm -rf /var/lib/apt/lists/*

RUN useradd --create-home --home-dir $HOME anon \
  && chown -R anon:anon $HOME

ENV LANG C.UTF-8


# download tor and check signature
RUN cd /tmp && set -x \
  && curl -sSOL "https://www.torproject.org/dist/torbrowser/${TORVERSION}/tor-browser-linux64-${TORVERSION}_en-US.tar.xz" \
  && curl -sSOL "https://www.torproject.org/dist/torbrowser/${TORVERSION}/tor-browser-linux64-${TORVERSION}_en-US.tar.xz.asc" \
  && mkdir ~/.gnupg && chmod 600 ~/.gnupg \
  && gpg --no-tty --keyserver ha.pool.sks-keyservers.net --recv-keys "${TORGPGKEY}" \
  || gpg --no-tty --keyserver pool.sks-keyservers.net --recv-keys "${TORGPGKEY}" \
  || gpg --no-tty --keyserver keyserver.pgp.com --recv-keys "${TORGPGKEY}" \
  || gpg --no-tty --keyserver pgp.mit.edu --recv-keys "${TORGPGKEY}" \
  && gpg --verify tor-browser-linux64-${TORVERSION}_en-US.tar.xz.asc \
  && mkdir -p ~/opt \
  && tar -vxJ --strip-components 1 -C ~/opt -f tor-browser-linux64-${TORVERSION}_en-US.tar.xz \
  && chown -R anon:anon $HOME \
  && rm -rf tor-browser* \
  && rm -rf ~/.gnupg


# good fonts
COPY local.conf /etc/fonts/local.conf

WORKDIR $HOME
USER anon

ENTRYPOINT ["/bin/bash"]
CMD [ "opt/Browser/start-tor-browser", "--log", "/dev/null" ]

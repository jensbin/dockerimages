# Dropbox in Docker

The work is based on some other projects found on github. Most noticable:

* https://github.com/janeczku/docker-dropbox

## Use dropbox

Dropbox is running with notification icon and as cli. But no integration with filemanager (e.g.: pcmanfm, thunar).


`dropbox` will start the container is not running. If running it can be used to interact with docker in the container (e.g.: `dropbox status`).

```
dropbox() {
  CONTAINERID=$(docker ps -f name=dropbox -q | awk 'NR==1{print $1}')
  if [[ ! -z "${CONTAINERID}" ]]; then
    docker exec -it ${CONTAINERID} dropbox "$@"
  else
    echo "Dropbox container not running. Staring..."
    XSOCK=/tmp/.X11-unix
    XAUTH=/tmp/.docker.xauth
    [[ -f $XAUTH ]] || xauth nlist :0 | sed -e 's/^..../ffff/' | xauth -f $XAUTH nmerge -
    docker run -d \
      --dns 1.1.1.1 \
      -v /etc/localtime:/etc/localtime:ro \
      -e DBOX_HOME=${HOME} \
      -e DBOX_UID=$(id -u) \
      -e DBOX_GID=$(id -g) \
      -e DBOX_SKIP_UPDATE=True \
      -v ${HOME}/Dropbox:/dbox/Dropbox \
      -v ${HOME}/.dropbox:/dbox/.dropbox \
      -v $XSOCK:$XSOCK:ro \
      -v $XAUTH:$XAUTH:ro \
      -e XAUTHORITY=$XAUTH \
      -e DISPLAY=$DISPLAY \
      -e QT_DEVICE_PIXEL_RATIO \
      -v /dev/shm:/dev/shm \
      -p 17500:17500 \
      -v /etc/machine-id:/etc/machine-id:ro \
      --rm \
      --name dropbox \
      jensbin/dropbox
  fi
}
```

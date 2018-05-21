# Centos 7 root shell in docker

## Use centos7

The main use case for me is to have a disposable centos7 shell.


```
centos7(){
  XSOCK=/tmp/.X11-unix
  XAUTH=/tmp/.docker.xauth
  [[ -f $XAUTH ]] || xauth nlist :0 | sed -e 's/^..../ffff/' | xauth -f $XAUTH nmerge -
  docker run -ti \
    -v /etc/zoneinfo/Etc/UTC:/etc/localtime:ro \
    -v ${HOME}:/home/jens \
    -v $XSOCK:$XSOCK:ro \
    -v $XAUTH:$XAUTH:ro \
    -e XAUTHORITY=$XAUTH \
    -e DISPLAY=$DISPLAY \
    -e QT_DEVICE_PIXEL_RATIO \
    -v /dev/snd:/dev/snd \
    -v /dev/shm:/dev/shm \
    -v /etc/machine-id:/etc/machine-id:ro \
    --rm \
    --name centos7 \
    jensbin/centos:7 \
    ${1:-/bin/bash}
}
```

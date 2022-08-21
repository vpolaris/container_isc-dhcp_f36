# Container for ISC-DHCP from a Fedora 36 Edition

## Goal

Build a chrooted image of ISC DHCP. Even if it's fully functional, this container will be used to achieve a larger project, I aim to create an image with DHCP server linked a web GUI interface named Glass, to provide Log output rsyslog will be implemented too

Prerequisites

You can use Podman or docker to build the image, but an action workflow is already designed to fully complete the job for amd64 and arm64, you can find it in my docker repository 

https://hub.docker.com/repository/docker/vpolaris/isc-dhcpd


## What the script does ?
 - Pull a fedora 36 container as helper
 - Use a dedicated directory to build the DHCP image
 - Use Fedora container to build the DHCP server inside the directory
 - Copy config and service files inside the directory
 - use the directoy to build the final image


## Installation

You can clone the repository or download files 

``` sh
git clone https://github.com/vpolaris/contenair_isc-dhcp_f36.git
cd contenair_isc-dhcp_f36
sudo podman build --squash-all --cap-add MKNOD --build-arg="$(uname -m)" -t f36:dhcpd -f ./Dockerfile . 
```

## Usage

There is a basic config file inside the image which can link to private netwok 192.168. and 172.16. and also the default podman network. You can overide it by mounting a volume


To schedule the default service use the following command
``` sh
podman run -tid --privileged --net host \
  --cap-add SYSLOG \
  --cap-add NET_RAW \
  --cap-add SYS_CHROOT \
  --name dhcpd \
  -e IPOPT=-4 \
  --volume /etc/dhcp/dhcpd.conf:/isc-dhcpd/etc/dhcpd.conf:rw \
  -t f36:dhcpd

```
### IPOTP role

Determine which IP protocol the DHCP will supprt
can take one of these three values:

- -4 for IPV4 only (default)
- -6 for IPV6 only
- all for both values

## Entrypoint options

you can call /bin/entrypoint.sh with following arguments

stop : shutdow the DHCP service without leaving the container
start :  enable DHCP service
restart : stop and start DHCP service

### Example
``` sh
podman exec -ti dhcpd /bin/entrypoint.sh stop
```





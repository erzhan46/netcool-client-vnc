netcool-client-vnc
=========================

Docker image to provide VNC interface to access Netcool/Omnibus 8.1 client in Ubuntu 16.04 LXDE desktop environment.
Forked from (https://github.com/fcwu/docker-ubuntu-vnc-desktop)

Quick Start
-------------------------

1. Clone this repository (make sure to use _'--recursive'_ to initialize all submodules)
```
git clone --recursive https://github.com/erzhan46/netcool-client-vnc
```
2. Download Netcool/Omnibus package (64-bit Linux) from Passport Advantage (e.g. _"IBM Tivoli Netcool OMNIbus 8.1.0.21 Core - Linux 64bit Multilingual (CC3V7ML)"_ ) and copy it to omnibus8.1 directory.
```
cp TVL_NTCL_OMN_V8.1.0.21_CORE_LNX_M.zip <Dev>/netcool-client-vnc/omnibus8.1
```
3. Build the docker image
```
cd <Dev>/netcool-client-vnc
make build
```
4. Prepare Object Server connectivity by creating local directory e.g. $HOME/share and placing two files in it:
- omni.dat - Netcool/Omnibus connections file
- hosts - Containing IP to hostname matches for remote server(s) where Object Server(s) is running. (This is required to ensure that nco_event will run)
5. Run the docker container mapping local port `6080` for Web access, port '5900' for local VNC access and mounting directory containing omni.dat and hosts files to /root/etc in the container:
```
docker run -p 6080:80 -p 5900:5900 -v /dev/shm:/dev/shm -v $HOME/share:/root/etc erzhan/ubuntu-netcool-vnc

```
6. Access VNC via web browser at http://127.0.0.1:6080/ or using VNC client at localhost:5900

<img src="https://github.com/erzhan46/netcool-client-vnc/blob/master/screenshots/omnibus.png" width=700/>

7. Environment variable `VNC_PASSWORD` can be set to enable password for VNC

```
docker run -p 6080:80 -p5900:5900 -e VNC_PASSWORD=mypassword -v /dev/shm:/dev/shm -v $HOME/share:/root/etc erzhan/ubuntu-netcool-vnc
```
A prompt will ask password either in the browser or vnc viewer.

8. Base access authentication of HTTP via `HTTP_PASSWORD`

```
docker run -p 6080:80 -e HTTP_PASSWORD=mypassword -v /dev/shm:/dev/shm -v $HOME/share:/root/etc erzhan/ubuntu-netcool-vnc

```

SSL
--------------------

To connect with SSL, generate self signed SSL certificate first if you don't have it

```
mkdir -p ssl
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout ssl/nginx.key -out ssl/nginx.crt
```

Specify SSL port by `SSL_PORT`, certificate path to `/etc/nginx/ssl`, and forward it to 6081

```
docker run -p 6081:443 -e SSL_PORT=443 -v ${PWD}/ssl:/etc/nginx/ssl -v /dev/shm:/dev/shm -v $HOME/share:/root/etc erzhan/ubuntu-netcool-vnc
```

Screen Resolution
------------------

The Resolution of virtual desktop adapts browser window size when first connecting the server. You may choose a fixed resolution by passing `RESOLUTION` environment variable, for example

```
docker run -p 6081:443 --e RESOLUTION=1920x1080 -v /dev/shm:/dev/shm -v $HOME/share:/root/etc erzhan/ubuntu-netcool-vnc
```


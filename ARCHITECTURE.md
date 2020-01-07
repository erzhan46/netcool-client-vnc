# Architecture of the container #

Components
============

The container contains the following components :
- An Ubuntu base system
- The tini + supervisord startup and daemon control system
- Nginx Web server
- A backend ("novnc2") Python Web app providing an API (written with
  Flask) on port 6079
- A frontend VueJS Web app displayed to the user, which will wrap noVNC
- noVNC + WebSockify providing the Web VNC client in an HTML5 canvas
- Xvfb running the X11 server in memory
- x11vnc exporting the X11 display through VNC
- and all regular X applications, like the LXDE desktop and apps
- Netcool/Omnibus v8.1 (Admin client, Event Viewer and MIB Manager)

Wiring them all
------------------

Internally, Xvfb will be started in DISPLAY :1, then x11vnc will
provide access to it on the default VNC port (5900).

noVNC will be started listening to HTTP requests on port 6081.
It is possible to connect directly to port 6081 of the container, to
only use the regular noVNC Web interface (provided it is exported by
the container).

Above noVNC stands the VueJS frontend Web app provided by nginx, which
will proxy the noVNC canvas, and will add some useful features over
noVNC.

User-oriented features
==========================

The Web frontend adds the following features :
- upon display of the Web page, the app will detect the size of the
  Web browser's window, and will invoke the backend API so as to make
  sure the noVNC rendering ajusts to that size
- provide a flash video rendering transporting the sound (???)

Netcool/Omnibus specifics
=========================
Netcool/OMnibus will be deployed in /opt/IBM/tivoli/netcool.
Desktop shortcuts will be crated for Administrator, Event Viewer and MIB Manager.
Object Server connectivity can be loaded using local volume mounting by creating local directory containing omni.dat and hosts files. Directory should be mounted to /root/etc directory in the container. Container startup script will place omni.dat to /opt/IBM/tivoli/netcool/etc directory and run nco_igen. Contents of a hosts file will be added to /etc/hosts.
Example of omni.dat:
```
#
# omni.dat file as prototype for interfaces file
#
# Ident: $Id: omni.dat 1.5 1999/07/13 09:34:20 chris Development $
#
[NCOMS]
{
	Primary: host1 4100 ssl 4150
}
```
Corresponding hosts file:
```
192.168.1.11 host1
```

Multipath TCP on Mac OSX
========================

This project allows you to use *Multipath TCP* (MPTCP) on an unmodified Mac OSX
computer. This project is intended as to allow you to test MPTCP and so
do not expect good performances.

How does it work ?
------------------

The solution is quite straightforward: take a virtual machine running the
[Linux kernel version of Multipath TCP](https://github.com/cpaasch/mptcp) and
redirect traffic through it. The project however goes even beyond that. Indeed,
this project main goals are to:

* allow using the the multiple interfaces available on the host;
* allow mobility.

The first requirement is met by mapping the physical adapters to virtual ones.
The connectivity from inside the virtual device is ensured by bridging the
physical and virtual interfaces together. A NAT is also used as in some
environment a MAC filtering might be in place which can cause the virtual
machine to not retrieve an IP address using regular DHCP. Static IP addresses
are therefore used and are NATed to the corresponding public address. The
second requirement is ensured by monitoring the route changes on the host and
by echoing those changes inside the virtual machine.

Installation
------------

The only requirement to run Multipath TCP on your computer is to install
[VirtualBox](https://www.virtualbox.org/). VirtualBox is a virtualization
software that allows to run guest operating systems. In this project it is used
to run the MPTCP's virtual machine.

The project only depends on a single script: `mptcpctl`. To install it, simply
run the following:

	$ sudo curl https://raw.github.com/multipath-tcp/mptcp-virtual/master/mptcpctl -o /usr/local/bin/mptcpctl
	$ sudo chmod +x /usr/local/bin/mptcpctl

Usage
-----

The first step is to import the VirtualBox appliance (this can be skipped if
already imported):

	$ curl -L https://github.com/downloads/multipath-tcp/mptcp-virtual/mptcp-virtual.ova -o /tmp/mptcp-virtual.ova
	$ mptcpctl import /tmp/mptcp-virtual.ova

Once this step is performed then the proxy can be started:

	$ mptcpctl start

And stopped using:

	$ mptcpctl stop

To allow mobility (not useful if the interfaces are not changing IP addresses):

	$ mptcpctl monitor

Once the machine has started, then you can try Multipath TCP setting
`127.0.0.1:13128` as a HTTP(S) proxy in your preferred application. You can
test that it works using:

	$ http_proxy="127.0.0.1:13128" curl mptcp.info.ucl.ac.be

A different message should be displayed whether you are MPTCP-capable.

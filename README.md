Management KVM machines.

1) Install following packages:

    1.1) For Debian, Ubuntu:

        1.1.1) Host: apt-get install uml-utilities bridge-utils arp-scan kvm ssh

        1.1.2) Guest: apt-get install dnsmasq ssh

2) Add user into group kvm:
    usermod kvm -G <user_name>

3) Create image file:
    kvm-img create -f qcow2 -o preallocation=metadata,cluster_size=2M,encryption=off ./ubuntu-server-10.04-64.img 500G

4) Convert image file:
    kvm-img convert -O qcow2 -o preallocation=metadata,cluster_size=2M,encryption=off ./ubuntu-server-10.04-64.img ./ubuntu-server-10.04-64.img.new

5) For bridge interface.

    5.1) For Debian, Ubuntu:

        5.1.1) Host file /etc/network/interfaces example:

            5.1.1.1) Static IP address:
# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto eth0
iface eth0 inet manual

auto br0
iface br0 inet static
    network 10.0.0.0
    gateway 10.0.0.1
    address 10.0.0.4
    broadcast 10.0.0.255
    netmask 255.255.255.0
    bridge_ports eth0
    bridge_maxwait 0
    bridge_stp 0
    bridge_fd 0

            5.1.1.2) Dyamic IP address:
# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto eth0
iface eth0 inet dhcp

        5.1.2) Guest file /etc/network/interfaces example:
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto eth0
iface eth0 inet dhcp

#!/bin/bash

echo 1 > /proc/sys/net/ipv4/conf/default/forwarding


ip netns add h2
ip link add veth1 type veth peer name veth_h2
ip link set veth1 netns h2
ip net e h2 ip l s veth1 up

ip net e h2 ip l a link veth1 name veth2_10 type vlan id 10
ip net e h2 ip a a 10.0.0.2/24 dev veth2_10
ip net e h2 ip l s veth2_10 up


ip net e h2 ip l a link veth1 name veth2_20 type vlan id 20
ip net e h2 ip a a 20.0.0.2/24 dev veth2_20
ip net e h2 ip l s veth2_20 up

ip netns add h3
ip link add veth1 type veth peer name veth_h3
ip link set veth1 netns h3
ip net e h3 ip l s veth1 up

ip net e h3 ip l a link veth1 name veth3_10 type vlan id 10
ip net e h3 ip a a 10.0.0.3/24 dev veth3_10
ip net e h3 ip l s veth3_10 up

ip netns add h4
ip link add veth1 type veth peer name veth_h4
ip link set veth1 netns h4
ip net e h4 ip l s veth1 up

ip net e h4 ip l a link veth1 name veth4_20 type vlan id 20
ip net e h4 ip a a 20.0.0.4/24 dev veth4_20
ip net e h4 ip l s veth4_20 up

brctl addbr br0
ip l s br0 up

brctl addif br0 veth_h2
ip l s veth_h2 up
brctl addif br0 veth_h3
ip l s veth_h3 up
brctl addif br0 veth_h4
ip l s veth_h4 up


ip l a link br0 name br0_10 type vlan id 10
ip a a 10.0.0.1/24 dev br0_10
ip l s br0_10 up

ip l a link br0 name br0_20 type vlan id 20
ip a a 20.0.0.1/24 dev br0_20
ip l s br0_20 up

ip net e h2 ip l s lo up
ip net e h3 ip l s lo up
ip net e h4 ip l s lo up

ip net e h4 ip r a 10.0.0.2 dev veth4_20
ip net e h4 ip r a 10.0.0.1 dev veth4_20
ip net e h3 ip r a 20.0.0.2 dev veth3_10
ip net e h3 ip r a 20.0.0.1 dev veth3_10

ip net e h2 ip r a default via 20.0.0.1 
ip net e h3 ip r a default via 20.0.0.1
ip net e h4 ip r a default via 10.0.0.1


#!/bin/bash
#sudo -i
echo 1 > /proc/sys/net/ipv4/conf/default/forwarding

#СЃРѕР·РґР°Р»Рё h2
ip netns add h2
#СЃРѕР·РґР°Р»Рё С€РЅСѓСЂ СЃ РєРѕРЅС†Р°РјРё 1 Рё h2
ip link add veth1 type veth peer name veth_h2
#Р·Р°СЃСѓРЅСѓР»Рё 1 РєРѕРЅРµС† С€РЅСѓСЂРєР° РІ h2
ip link set veth1 netns h2
#РїРѕРґРЅСЏР»Рё 
ip net e h2 ip l s veth1 up

#СЃРѕР·РґР°Р»Рё vlan 10 РґР»СЏ h2
ip net e h2 ip l a link veth1 name veth2_10 type vlan id 10
#РїРµСЂРµС€Р»Рё РЅР° h2 Рё РїРѕРІРµСЃРёР»Рё РЅР° РЅРµРіРѕ ip
ip net e h2 ip a a 10.0.0.2/24 dev veth2_10
#РїРѕРґРЅСЏР»Рё vlan РєРѕРїРёСЋ
ip net e h2 ip l s veth2_10 up


#СЃРѕР·РґР°Р»Рё vlan 20 РґР»СЏ h2
ip net e h2 ip l a link veth1 name veth2_20 type vlan id 20
#РїРµСЂРµС€Р»Рё РЅР° h2 Рё РїРѕРІРµСЃРёР»Рё РЅР° РЅРµРіРѕ ip
ip net e h2 ip a a 20.0.0.2/24 dev veth2_20
#РїРѕРґРЅСЏР»Рё vlan РєРѕРїРёСЋ
ip net e h2 ip l s veth2_20 up

#СЃРѕР·РґР°Р»Рё h3
ip netns add h3
#СЃРѕР·РґР°Р»Рё С€РЅСѓСЂ СЃ РєРѕРЅС†Р°РјРё 1 Рё h3
ip link add veth1 type veth peer name veth_h3
#Р·Р°СЃСѓРЅСѓР»Рё 1 РєРѕРЅРµС† С€РЅСѓСЂРєР° РІ h3
ip link set veth1 netns h3
#РїРѕРґРЅСЏР»Рё 
ip net e h3 ip l s veth1 up

#СЃРѕР·РґР°Р»Рё vlan 10 РґР»СЏ h3
ip net e h3 ip l a link veth1 name veth3_10 type vlan id 10
#РїРµСЂРµС€Р»Рё РЅР° h3 Рё РїРѕРІРµСЃРёР»Рё РЅР° РЅРµРіРѕ ip
ip net e h3 ip a a 10.0.0.3/24 dev veth3_10
#РїРѕРґСЏР»Рё vlan 
ip net e h3 ip l s veth3_10 up

#СЃРѕР·РґР°Р»Рё h4
ip netns add h4
#СЃРѕР·РґР°Р»Рё С€РЅСѓСЂ СЃ РєРѕРЅС†Р°РјРё 1 Рё h4
ip link add veth1 type veth peer name veth_h4
#Р·Р°СЃСѓРЅСѓР»Рё 1 РєРѕРЅРµС† С€РЅСѓСЂРєР° РІ h3
ip link set veth1 netns h4
#РїРѕРґРЅСЏР»Рё veth_h4
ip net e h4 ip l s veth1 up

#СЃРѕР·РґР°Р»Рё vlan 20 РґР»СЏ h4
ip net e h4 ip l a link veth1 name veth4_20 type vlan id 20
#РїРµСЂРµС€Р»Рё РЅР° h2 Рё РїРѕРІРµСЃРёР»Рё РЅР° РЅРµРіРѕ ip
ip net e h4 ip a a 20.0.0.4/24 dev veth4_20
#РїРѕРґСЏР»Рё vlan 
ip net e h4 ip l s veth4_20 up

#СЃРѕР·РґР°Р»Рё Р±СЂРёРґР¶ br0
brctl addbr br0
#РїРѕРґРЅСЏР»Рё Р±СЂРёРґР¶
ip l s br0 up

# Р·Р°СЃРѕРІС‹РІР°РµРј РґСЂСѓРіРёРµ РєРѕРЅС†С‹ С€РЅСѓСЂРєРѕРІ РІ Р±СЂРёРґР¶ Рё РїРѕРґРЅРёРјР°РµРј РёРЅС‚РµСЂС„РµР№СЃС‹
brctl addif br0 veth_h2
ip l s veth_h2 up
brctl addif br0 veth_h3
ip l s veth_h3 up
brctl addif br0 veth_h4
ip l s veth_h4 up


#СЃРѕР·РґР°Р»Рё vlan 10 РґР»СЏ Р±СЂРёРґР¶Р°
ip l a link br0 name br0_10 type vlan id 10
#РїРѕРІРµСЃРёР»Рё РЅР° РЅРµРіРѕ ip
ip a a 10.0.0.1/24 dev br0_10
#РїРѕРґРЅРёРјР°РµРј
ip l s br0_10 up

#СЃРѕР·РґР°Р»Рё vlan 20 РґР»СЏ Р±СЂРёРґР¶Р°
ip l a link br0 name br0_20 type vlan id 20
#РїРѕРІРµСЃРёР»Рё РЅР° РЅРµРіРѕ ip
ip a a 20.0.0.1/24 dev br0_20
#РїРѕРґРЅРёРјР°РµРј
ip l s br0_20 up

# РїРѕРґРЅРёРјР°РµРј lowback
ip net e h2 ip l s lo up
ip net e h3 ip l s lo up
ip net e h4 ip l s lo up

#СЂР°СѓС‚ С‚Р°Р±Р»РёС†Р°
ip net e h4 ip r a 10.0.0.2 dev veth4_20
ip net e h4 ip r a 10.0.0.1 dev veth4_20
ip net e h3 ip r a 20.0.0.2 dev veth3_10
ip net e h3 ip r a 20.0.0.1 dev veth3_10

ip net e h2 ip r a default via 20.0.0.1 
ip net e h3 ip r a default via 20.0.0.1
ip net e h4 ip r a default via 10.0.0.1


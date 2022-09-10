```
iptables -t nat -I PREROUTING -p tcp -d 192.168.1.254 --dport 11080 -j DNAT --to-destination 172.17.210.10:80
iptables -t nat -I PREROUTING -p tcp -d 192.168.1.254 --dport 10980 -j DNAT --to-destination 172.17.210.9:80
iptables -t nat -I PREROUTING -p tcp -d 192.168.1.254 --dport 10880 -j DNAT --to-destination 172.17.210.8:80
iptables -t nat -I PREROUTING -p tcp -d 192.168.1.254 --dport 10780 -j DNAT --to-destination 172.17.210.7:80
# datalake has no 80 port
iptables -t nat -I PREROUTING -p tcp -d 192.168.1.254 --dport 10580 -j DNAT --to-destination 172.17.210.5:80

iptables -t nat -I PREROUTING -p tcp -d 192.168.1.254 --dport 25432 -j DNAT --to-destination 172.17.210.9:5432
iptables -t nat -I PREROUTING -p tcp -d 192.168.1.254 --dport 28443 -j DNAT --to-destination 172.17.210.8:443
iptables -t nat -I PREROUTING -p tcp -d 192.168.1.254 --dport 29001 -j DNAT --to-destination 172.17.210.6:9001

iptables -t nat -I PREROUTING -p tcp -d 192.168.1.254 --dport 11022 -j DNAT --to-destination 172.17.210.10:22
iptables -t nat -I PREROUTING -p tcp -d 192.168.1.254 --dport 10922 -j DNAT --to-destination 172.17.210.9:22
iptables -t nat -I PREROUTING -p tcp -d 192.168.1.254 --dport 10822 -j DNAT --to-destination 172.17.210.8:22
iptables -t nat -I PREROUTING -p tcp -d 192.168.1.254 --dport 10722 -j DNAT --to-destination 172.17.210.7:22
iptables -t nat -I PREROUTING -p tcp -d 192.168.1.254 --dport 10622 -j DNAT --to-destination 172.17.210.6:22
iptables -t nat -I PREROUTING -p tcp -d 192.168.1.254 --dport 10522 -j DNAT --to-destination 172.17.210.5:22

iptables -I FORWARD -m state -d 172.17.210.0/24 --state NEW,RELATED,ESTABLISHED -j ACCEPT

```
where:
 - `192.168.1.254 --dport 10080` - host IP with port to listen
 - `172.17.210.0/24` - VM network
 - `172.17.210.10:80` - target VM with port

to delete rule use "-D" instead of "-I"

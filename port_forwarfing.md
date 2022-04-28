```
iptables -t nat -I OUTPUT -p tcp -d 192.168.1.254 --dport 10080 -j DNAT --to-destination 172.17.210.10:80
iptables -t nat -I PREROUTING -p tcp -d 192.168.1.254 --dport 10080 -j DNAT --to-destination 172.17.210.10:80
iptables -I FORWARD -m state -d 172.17.210.0/24 --state NEW,RELATED,ESTABLISHED -j ACCEPT
```
where:
 - `192.168.1.254 --dport 10080` - host IP with port to listen
 - `172.17.210.0/24` - VM network
 - `172.17.210.10:80` - target VM with port

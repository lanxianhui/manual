# Dokodemo-Door

[![English](../../resources/english.svg)](https://www.v2ray.com/en/configuration/protocols/dokodemo.html) [![Chinese](../../resources/chinese.svg)](https://www.v2ray.com/chapter_02/protocols/dokodemo.html) [![German](../../resources/german.svg)](https://www.v2ray.com/de/configuration/protocols/dokodemo.html) [![Russian](../../resources/russian.svg)](https://www.v2ray.com/ru/configuration/protocols/dokodemo.html)

Dokodemo-door is a protocol for inbound connections. Es nimmt alle Verbindungen und übergibt sie an das angegebene Ziel.

Dokodemo-door can also (if configured) work as a transparent proxy.

* Name: dokodemo-door
* Typ: Eingehend
* Aufbau:

```javascript
{
  "address": "8.8.8.8",
  "port": 53,
  "network": "tcp",
  "followRedirect": false,
  "userLevel": 0
}
```

Woher:

* `address`: Adresse des Zielservers. Kann eine IPv4-, IPv6- oder eine Domäne in Zeichenfolgenform sein. 
  * Wenn `followRedirect` (siehe unten) ist `wahr`, `Adresse` kann leer sein.
* `port`: Port des Zielservers. Ganze Zahl.
* `Netzwerk`: Typ des Netzwerks, entweder "TCP" oder "UDP".
* `followRedirect`: Wenn der Wert `true`, erkennt dokodemo-door das Ziel von TProxy und verwendet es als Ziel. 
  * Funktioniert nur unter Linux
  * Unterstützt TCP / IPv4-Verbindungen
  * Unterstützt UDP / IPv4-Pakete. Erfordert die Berechtigung root (CAP \ _NET \ _ADMIN)
* `userLevel`: Benutzerebene. Alle Verbindungen teilen diese Ebene. Siehe [Richtlinie](../policy.md) für Details.

## Beispiele für transparenten Proxy

Fügen Sie eine dokodemo-Tür eingehend wie folgt hinzu.

```javascript
{
  "network": "tcp,udp",
  "timeout": 30,
  "followRedirect": true
}
```

Konfigurieren Sie iptables wie folgt.

```plain
# Create new chain
root@Wrt:~# iptables -t nat -N V2RAY
root@Wrt:~# iptables -t mangle -N V2RAY
root@Wrt:~# iptables -t mangle -N V2RAY_MARK

# Ignore your V2Ray server's addresses
# It's very IMPORTANT, just be careful.
root@Wrt:~# iptables -t nat -A V2RAY -d 123.123.123.123 -j RETURN

# Ignore LANs and any other addresses you'd like to bypass the proxy
# See Wikipedia and RFC5735 for full list of reserved networks.
root@Wrt:~# iptables -t nat -A V2RAY -d 0.0.0.0/8 -j RETURN
root@Wrt:~# iptables -t nat -A V2RAY -d 10.0.0.0/8 -j RETURN
root@Wrt:~# iptables -t nat -A V2RAY -d 127.0.0.0/8 -j RETURN
root@Wrt:~# iptables -t nat -A V2RAY -d 169.254.0.0/16 -j RETURN
root@Wrt:~# iptables -t nat -A V2RAY -d 172.16.0.0/12 -j RETURN
root@Wrt:~# iptables -t nat -A V2RAY -d 192.168.0.0/16 -j RETURN
root@Wrt:~# iptables -t nat -A V2RAY -d 224.0.0.0/4 -j RETURN
root@Wrt:~# iptables -t nat -A V2RAY -d 240.0.0.0/4 -j RETURN

# Anything else should be redirected to Dokodemo-door's local port
root@Wrt:~# iptables -t nat -A V2RAY -p tcp -j REDIRECT --to-ports 12345

# Add any UDP rules
root@Wrt:~# ip route add local default dev lo table 100
root@Wrt:~# ip rule add fwmark 1 lookup 100
root@Wrt:~# iptables -t mangle -A V2RAY -p udp --dport 53 -j TPROXY --on-port 12345 --tproxy-mark 0x01/0x01
root@Wrt:~# iptables -t mangle -A V2RAY_MARK -p udp --dport 53 -j MARK --set-mark 1

# Apply the rules
root@Wrt:~# iptables -t nat -A OUTPUT -p tcp -j V2RAY
root@Wrt:~# iptables -t mangle -A PREROUTING -j V2RAY
root@Wrt:~# iptables -t mangle -A OUTPUT -j V2RAY_MARK
```
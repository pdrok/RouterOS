
## Configuración de RouterOS:

### Básica: 

Nombre del router:
```
 /system identity set name="RO_EMPRESA"
 ```
 
Interfaz bridge lan y los puertos ethernet:
 
 ```
/interface bridge add fast-forward=no name=bridge_lan
/interface bridge port add bridge=bridge_lan hw=no interface=ether2
/interface bridge port add bridge=bridge_lan hw=no interface=ether3
/interface bridge port add bridge=bridge_lan hw=no interface=ether4
/interface bridge port add bridge=bridge_lan hw=no interface=ether5
```

IP para la interfaz lan:
```
/ip address add address=192.0.2.1/24 interface=bridge_lan network=192.0.2.0
```

IP para la interfaz WAN:

```
/ip address add address=203.0.113.2/24 interface=ether1 network=203.0.113.0
```
Puerta de enlace predeterminada:

```
/ip route add distance=1 gateway=203.0.113.1
```
Servidores DNS:
```
/ip dns set allow-remote-requests=yes cache-size=4096KiB servers=203.0.113.3,203.0.113.4
```

IP disponibles para el Servidor DHCP:
```
/ip pool add name=dhcp_pool_lan ranges=192.0.2.100-192.0.2.199
```

Servidor de DHCP:
```
/ip dhcp-server add address-pool=dhcp_pool_lan disabled=no interface=bridge_lan lease-time=12h name=dhcp_lan
/ip dhcp-server network add address=192.0.2.0/24 dns-server=192.0.2.1 domain=empresa.lan gateway=192.0.2.1 ntp-server=192.0.2.1
```

RFC1918 en la lista de direcciones:

```
/ip firewall address-list add address=10.0.0.0/8 list=RFC1918
/ip firewall address-list add address=172.16.0.0/12 list=RFC1918
/ip firewall address-list add address=192.168.0.0/16 list=RFC1918
```

Enmascaramiento IP (NAT):
```
/ip firewall nat add action=masquerade chain=srcnat dst-address-list=!RFC1918 out-interface=ether1 src-address-list=RFC1918
/ip firewall nat add action=masquerade chain=srcnat dst-address-type=!local log-prefix="NAT interno" src-address-list=RFC1918
```



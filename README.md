# prime

# DNSMASQ
- `apt-get install dnsmasq`
- Add `conf-dir=/etc/dnsmasq.d/,*.conf` at the end of the file `conf-dir=/etc/dnsmasq.d/,*.conf`
- Create a file `/etc/dnsmasq.d/maas.conf`
```
port=0

log-dhcp

dhcp-no-override

dhcp-boot=pxelinux.0,pxeserver,<ip-pxe-server>

pxe-prompt="Booting FOG Client", 1

dhcp-range=<subnet>,proxy
```
Example :
```
port=0

log-dhcp

dhcp-no-override

dhcp-boot=pxelinux.0,pxeserver,192.168.1.34

pxe-prompt="Booting FOG Client", 1

dhcp-range=192.168.1.0,proxy
```

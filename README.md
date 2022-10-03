# systemd-resolved
Next generation DNS resolve scheme.

## 0. Recommended DNS servers

- IPv4
    - 208.67.222.222
    - 8.8.4.4

- IPv6
    - 2001:4860:4860::8888
    - 2620:119:53::53

## 1.Examine DNS status

```
resolvectl status
```

Tip: With systemd-resolved.service, 127.0.0.53#53(127.0.0.53) is the local DNS server for resolving.

## 2.Configure **Global** interface DNS

```[bash]
sudo vim /etc/systemd/resolved.conf
```

In *[Resolve]* section, modify the DNS ip:

```[vim]
[Resolve]
DNS=208.67.220.220 208.67.222.222
```

## (Advanced)Custom **Global** DNS target

```[bash]
sudo vim /etc/systemd/resolved.conf
```

In *[Resolve]* section, modify the **Domains**:

```[vim]
[Resolve]
Domains=~. ~github.com ~microsoft.com
```

In *Domains* field, Specify which domain you want to reolve prefix with `~`.

> :warning: `~.` means resolve the *root DNS(.)*, but there are usually two `~.` value of different items shown by `resolvectl status`. One is **Golbal**, another is **Link <num>** which is dynamic set by receiving over DHCP, information provided via NetworkManager.

> :hankey: Usually, **Link [NUM]** interface (generated by DHCP, local dns) is faster than  **Global**, so software prefer to use it because both have `~.` value in *Domains* field. That's why **Global** need to speficy **Domains** value in `/etc/systemd/resolved.conf` file.


## Active configuration

```[bash]
systemctl restart systemd-resolved.service
```

## 3.Configure **Link [NUM]** interface DNS

Its DNS configuration is from NetworkManager, for example:

```[bash]
Link 3 (wlp3s0)
    Current Scopes: DNS LLMNR/IPv4 LLMNR/IPv6                                   
         Protocols: +DefaultRoute +LLMNR -mDNS -DNSOverTLS DNSSEC=no/unsupported
Current DNS Server: 208.67.222.222                                              
       DNS Servers: 1.1.1.1 208.67.222.222 208.67.220.220                       
        DNS Domain: ~.                                                          
```

NetworkManager Configure file:

Previous:

```[bash]
cd /etc/sysconfig/network-scripts/		#Location
man nm-settings-ifcfg-rh			#Reference
```

Present:

```[bash]
cd /etc/NetworkManager/system-connections/	#Location
man nm-settings					#Reference
```

Custom DNS ip and abandon ISP DNS ip

```[vim]
dns=1.1.1.1;208.67.222.222;208.67.220.220;	#semicolon(;)
ignore-auto-dns=true
```

> :warning: Use **semicolon(;)** to split multiple DNS ip, if any.

## Active configuration

```[bash]
systemctl restart NetworkManager.service 
```

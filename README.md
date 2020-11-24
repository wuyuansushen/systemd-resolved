# systemd-resolved
Next generation DNS resolve scheme.

## 1.Examine DNS status

```
resolvectl status
```

Tip: With systemd-resolved.service, 127.0.0.53#53(127.0.0.53) is the local DNS server for resolving.

## 2.Custom DNS target

```[bash]
sudo vim /etc/systemd/resolved.conf
```

In *[Resolve]* section, modify the information as following demo:

```[vim]
[Resolve]
DNS=208.67.220.220 208.67.222.222
Domains=~. ~github.com ~microsoft.com
```

In *Domains* field, Specify which domain you want to reolve prefix with `~`.

> :warning: `~.` means resolve the *root DNS(.)*, but there are usually two `~.` value of different items shown by `resolvectl status`. One is **Golbal**, another is **Link <num>** which is dynamic set by receiving over DHCP, information provided via resolvectl.

> :hankey: Usually, **Link <num>**(generated by DHCP) local dns is faster than  **Global** dns, so software prefer to use it because both have `~.` value in *Domains* field. That's why **Global** need to speficy **Domains** value in `/etc/systemd/resolved.conf` file.

## 3.Active configuration

```[bash]
systemctl restart systemd-resolved.service
```
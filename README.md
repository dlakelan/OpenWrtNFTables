# OpenWrtNFTables
An NFTables firewall for OpenWrt with DSCP tagging


To install first do:

```
opkg update
opkg install nftables kmod-nft-nat
```

place the nftables.conf file in /etc/ and place the nftables file in /etc/init.d

```
chmod 600 /etc/nftables.conf
chmod 744 /etc/init.d/nftables
/etc/init.d/nftables enable
/etc/init.d/firewall disable
echo "nf_nat" >> /etc/modules
```


If you want to add other specialty features you may need further kmods
etc. You can read the
[wiki](https://openwrt.org/docs/guide-user/firewall/misc/nftables) on
getting nftables on OpenWrt.

You will also need to configure the interfaces in the top stanza:

```

define wan = eth1
define lan = eth0
define guest = eth0.10 # or remove these if you don't use guest or iot networks
define iot = eth0.5 ## but be sure to also remove the reference to them below

define bulksize = 35000000 ## total transfer before being sent to CS1
define voipservers = {10.0.98.113} ## add ipv4 addresses of fixed voip / telephone servers you use here

```

and because you can't use a variable in an ingress interface name,
you'll also need to put your WAN interface in the tagging table here:

```
      	    type filter hook ingress device eth1 priority 0; ## FIXME, this can't be a variable so put your WAN device here

```

Finally when things are working a reboot should have you using
nftables instead of the standard firewall. To check the rules are
running:

```
nft list ruleset
```

# Troubleshooting: Static IP on Debian with NAT Network (virt-manager)

This file documents the issues encountered when configuring a static IP on a Debian VM, and how they were resolved.

---

## Issue: 169.254.x.x (link-local) IP address

### Description

When setting up the static IP, an additional `169.254.x.x` address appeared. This indicates that no IP was obtained via DHCP and the static configuration was not applied correctly.

### Solution

1. Check that **no DHCP client is running**:

```sh
ps aux | grep dhclient
```

If found, stop it:

```sh
sudo killall dhclient
```

Remove the conflicting IP:

```sh
sudo ip addr flush dev enp1s0
```

Restart networking:

```sh
sudo systemctl restart networking
```

## Issue: Static IP does not persist after reboot

### Description

After rebooting, the static IP configuration is not retained.

### Solution

Make sure the `/etc/network/interfaces` file is saved correctly and is not being overwritten by cloud-init or another network management system.

Check that NetworkManager is not enabled instead of ifupdown.

To disable NetworkManager (if installed):

```sh
sudo systemctl stop NetworkManager
sudo systemctl disable NetworkManager
```

General recommendations  
Use `ip a` to check the status of network interfaces.

Use `ip route` to verify the default gateway.

Test connectivity with:

```sh
ping 8.8.8.8
ping google.com
```

Additional notes  
The default libvirt NAT network (virbr0) uses `192.168.122.0/24`. Make sure to assign a free IP within this range.

Avoid conflicts with other VMs by assigning static IPs outside the DHCP range provided by dnsmasq
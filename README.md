# Configure Static IP on Debian with NAT Network (Virt-Manager)

```sh
debian-static-ip-nat/
├── config/ # Network configurations used in the VM
│ └── interfaces.static.conf # File with the static IP configuration
├── docs/ # Visual resources and screenshots
│ └── ip-address-check.png # Image showing the static IP on the interface
├── LICENSE # Project license (MIT, GPL, etc.)
├── notes/ # Technical notes and troubleshooting
│ └── troubleshooting.md # Common errors and how they were resolved
└── README.md # Main project documentation
```

This mini-project documents how to set up a **static IP** on a Debian virtual machine connected to a **NAT network (virbr0)** using **Virt-Manager** (KVM/libvirt).

---

## Step-by-step process

### 1. Identified the active network interface

- Used `ip a` and confirmed the interface was `enp1s0`
- The network used was NAT with the range `192.168.122.0/24`

---

### 2. Configured the static IP

- Edited `/etc/network/interfaces` with the following configuration:

```sh
source /etc/network/interfaces.d/*


auto lo
iface lo inet loopback

auto enp1s0
iface enp1s0 inet static
    address 192.168.122.83
    netmask 255.255.255.0
    gateway 192.168.122.1
    dns-nameservers 8.8.8.8 8.8.4.4
```

- See the full file at [`config/interfaces.static.conf`](config/interfaces.static.conf)

---

### 3. Resolved conflicts

- A 169.254.x.x (link-local) IP appeared

- Checked that there were no dhclient processes running

- Manually removed the conflicting IP with ip addr flush

- Restarted the network and verified only the desired IP remained

---

### 4. Verified connectivity

- Confirmed that enp1s0 had the IP 192.168.122.83

- Tested internet access

- Verified persistence after reboot

#### Final result

- Item Status
- Static IP configured  Yes (192.168.122.83)
- No DHCP conflicts
- No link-local IPs (169.254)
- Expected connectivity
- Persistent after reboot

---

## Result screenshot

![Result of `ip a`](docs/ip-address-check.png)
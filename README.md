# 🧪 Basic VLAN & Router-on-a-Stick Lab (Packet Tracer)

This lab simulates a simple network with **two VLANs** and **router-on-a-stick** configuration using Cisco Packet Tracer. The purpose is to practice basic VLAN segmentation and inter-VLAN routing.

---

## 🛠️ Lab Topology

* **PC0**: VLAN 10 - IP: `192.168.10.2`
* **PC1**: VLAN 10 - IP: `192.168.10.3`
* **PC2**: VLAN 20 - IP: `192.168.20.2`
* **Switch0**: Connected to all PCs and to Router0 via trunk port.
* **Router0**: Interfaces configured with sub-interfaces for each VLAN:

  * `G0/0.10`: VLAN 10 - IP: `192.168.10.1`
  * `G0/0.20`: VLAN 20 - IP: `192.168.20.1`

---

## 🧠 What I Learned

This README explains things in simple terms — as if you're learning from scratch (like me!).

---

## 🔧 Step-by-Step Configuration (with Explanation)

### 1. **Configure VLANs on the switch**

```bash
Switch(config)# vlan 10
Switch(config-vlan)# name VLAN10
Switch(config-vlan)# exit
Switch(config)# vlan 20
Switch(config-vlan)# name VLAN20
Switch(config-vlan)# exit
```

**💡What’s a VLAN?**
Think of it as creating **separate virtual lanes** inside a switch. Even though devices are physically connected to the same switch, VLANs allow us to **logically separate** them like they’re on different switches.

---

### 2. **Assign switch ports to VLANs**

```bash
Switch(config)# interface range fa0/1 - 2
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 10

Switch(config)# interface fa0/3
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 20
```

**💡Why `interface range fa0/1 - 2`?**
We want **both PC0 and PC1** to be in VLAN 10. So we configure **both ports (fa0/1 and fa0/2)** at once.

**💡What does `switchport mode access` do?**
It tells the switch, “This port is only for a single VLAN.” Without it, it might not accept VLAN assignments or pass traffic correctly.

---

### 3. **Configure trunk port on the switch (to connect to the router)**

```bash
Switch(config)# interface fa0/5
Switch(config-if)# switchport mode trunk
```

**💡What is a trunk?**
A **trunk port** can carry traffic for **multiple VLANs**. It’s used to send VLAN-tagged data from the switch to the router.

---

### 4. **Set up sub-interfaces on the router (Router-on-a-Stick)**

```bash
Router(config)# interface g0/0.10
Router(config-subif)# encapsulation dot1Q 10
Router(config-subif)# ip address 192.168.10.1 255.255.255.0

Router(config)# interface g0/0.20
Router(config-subif)# encapsulation dot1Q 20
Router(config-subif)# ip address 192.168.20.1 255.255.255.0
```

**💡What is `encapsulation dot1Q`?**
It means “tag this sub-interface with VLAN X.” The router knows which VLAN the data belongs to.

**💡Why `.10` in `g0/0.10`?**
It’s a sub-interface — like a mini-connection inside the main interface. We name it `.10` to represent **VLAN 10**, but it’s just a naming convention.

---

### 5. **Enable the main interface**

```bash
Router(config)# interface g0/0
Router(config-if)# no shutdown
```

**💡Why not `no shutdown` on the switch?**
Most switch ports are up by default, but VLAN interfaces (`interface vlan 1`) are for **management** — not needed here. We only needed to enable the router’s physical interface.

---

### 6. **Configure PCs**

Manually assign:

* PC0 → IP: `192.168.10.2`, GW: `192.168.10.1`
* PC1 → IP: `192.168.10.3`, GW: `192.168.10.1`
* PC2 → IP: `192.168.20.2`, GW: `192.168.20.1`

---

### ✅ Test Connectivity

* Ping from **PC0 to PC1** → ✅
* Ping from **PC0 to PC2** → ✅ (thanks to inter-VLAN routing!)

---

## ❓Frequently Asked Questions

> ❓ What is a VLAN again?
> A VLAN is like a virtual room inside a big hall (the switch). Even if people are in the same building, VLANs separate them.

> ❓ Is a VLAN just a chunk of IP addresses?
> Not exactly. A VLAN is a *logical network*. You then assign IPs that match that network, like `192.168.10.x` for VLAN 10.

> ❓ Why use trunking?
> To send traffic from **multiple VLANs** over a single physical cable. Dot1Q tagging helps identify which VLAN each packet belongs to.

> ❓ Why did PC2 fail to ping at first?
> I forgot to set its **default gateway**. Without that, it didn’t know how to reach other networks.

> ❓ Why do PC0 and PC2 need different gateways?
> Because they’re in different VLANs (subnets). Each VLAN needs its own IP interface on the router.

> ❓ If VLANs are separated, why can they ping each other?
> Because of the router. It routes packets between VLANs via sub-interfaces.

> ❓ What’s `line vty` and `line con`?
> Those are CLI access lines:

* `line con 0` → Local console
* `line vty 0 4` → Remote access (telnet/SSH)
  You can secure them with passwords, configure login, etc. Try this in your next lab!

---

## 🔍 Next Steps

* Set passwords with `enable secret`, `line vty`, and `line con`
* Try adding a **third VLAN**
* Experiment with `show vlan`, `show ip interface brief`, and `show run`

---

## 📁 Files

* `vlan-router-lab.pkt` – Cisco Packet Tracer file
* `README.md` – This file

---

## 🧑‍💻 Author

Noel - Beginner Network Enthusiast – learning by doing and documenting to understand deeply.

---

Let me know if you want me to generate the actual `.pkt` file or help make this README more advanced as you progress!

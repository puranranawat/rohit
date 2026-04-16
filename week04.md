# Week 04 – Routing Tables and Dynamic Routing (OSPF)

## Student Details

* Name: Baswa
* Student ID: 12307969
* Unit: COIT20261 – Network Services and Automation
* Week: 04

---

# Part A – Static Routing and Routing Table Analysis

## Aim

The aim of this task is to understand how routing works between different subnets using a router, configure IP addressing, enable packet forwarding, and analyze routing tables.

---

## Network Topology

A network was designed with three Linux hosts, one Linux router, and one Ethernet switch. The network is divided into two subnets connected through the router.

![Network Topology](screenshot/View-Routes-12307969-network.png)

---

## Network Design

### Subnet 1: 10.1.1.0/24

* Host1 → 10.1.1.1
* Host2 → 10.1.1.2
* Router (eth0) → 10.1.1.254

### Subnet 2: 10.1.2.0/24

* Host3 → 10.1.2.1
* Router (eth1) → 10.1.2.254

This design allows communication between two separate networks using a router.

---

## Configuration

### Host Configuration Example

```id="h1cfg"
auto eth0
iface eth0 inet static
   address 10.1.1.1
   netmask 255.255.255.0
   gateway 10.1.1.254
   up sysctl net.ipv4.ip_forward=0
```

All hosts were configured similarly with appropriate IP addresses and gateways.

---

### Router Configuration

```id="routercfg"
auto eth0
iface eth0 inet static
   address 10.1.1.254
   netmask 255.255.255.0

auto eth1
iface eth1 inet static
   address 10.1.2.254
   netmask 255.255.255.0

up sysctl net.ipv4.ip_forward=1
```

The router connects both subnets and enables packet forwarding.

---

## Forwarding Verification

Command used:

```id="fwcmd"
sysctl net.ipv4.ip_forward
```

* Router → Output = 1 (Enabled)
* Hosts → Output = 0 (Disabled)

This confirms correct routing behavior.

---

## Routing Table Analysis

Command used:

```id="routecmd"
ip route show
```

### Host Routing Table

* Default route via gateway
* Local subnet route

### Router Routing Table

* Route to 10.1.1.0/24 via eth0
* Route to 10.1.2.0/24 via eth1

![Routing Tables](screenshot/View-Routes-12307969-routing.png)

This shows that the router knows how to reach both networks.

---

## Connectivity Testing

Command:

```id="pingcmd"
ping 10.1.2.1
```

![Ping Test](screenshot/View-Routes-12307969-ping.png)

Result:

* Successful communication between subnets
* 0% packet loss

This confirms correct routing and configuration.

---

## Analysis

This task demonstrates how routing enables communication between different subnets. Without a router, devices in separate networks cannot communicate. The router forwards packets based on routing tables.

The gateway configured in hosts directs traffic to the router when the destination is outside the local subnet. Enabling IP forwarding on the router is essential, as it allows the device to behave like a network-layer device.

Routing tables contain destination networks and next-hop information, which determines how packets travel through the network.

---

## Key Concepts Learned

* Static IP configuration
* Subnetting and network separation
* Role of router and gateway
* IP forwarding mechanism
* Routing table interpretation

---

## Reflection

This task improved my understanding of how networks communicate across subnets. I learned how to configure a router and understand its role in forwarding packets. Initially, understanding routing tables was challenging, but after testing with ping and observing outputs, it became clear how packets are routed.

---

## Conclusion

The task was successfully completed by creating a multi-subnet network, configuring routing, and verifying communication. This provides a strong foundation for understanding real-world networking systems.

---

# Part B – Dynamic Routing with OSPF

## Aim

To observe how dynamic routing protocols automatically update routing tables and handle network changes.

---

## Network Setup

A pre-configured OSPF network template with four routers and two hosts was used.

![OSPF Topology](screenshot/OSPF-Basics-12307969-network.png)

---

## Commands Used

```id="ospf1"
show ip ospf neighbor
```

```id="ospf2"
show ip ospf route
```

```id="ospf3"
show ip route
```

---

## Observations

### OSPF Neighbours

![OSPF Neighbors](screenshot/OSPF-neighbor.png)

* Routers automatically discover each other
* Neighbour relationships are established

---

### Routing Table

![OSPF Routing](screenshot/OSPF-routing.png)

* Routes learned dynamically
* Next-hop routers identified

---

## Path Testing

### Before Link Failure

```id="trace1"
traceroute <destination>
```

![Traceroute Before](screenshot/OSPF-trace-before.png)

---

### After Link Failure

A link was disconnected using NETem.

```id="trace2"
traceroute <destination>
```

![Traceroute After](screenshot/OSPF-trace-after.png)

---

## Analysis

OSPF automatically updates routing tables when network topology changes. When a link fails, the protocol recalculates the best path and reroutes traffic.

This demonstrates the advantage of dynamic routing over static routing, where manual changes are not required.

---

## Key Concepts Learned

* Dynamic routing
* OSPF protocol behavior
* Route recalculation
* Network resilience

---

## Reflection

This task helped me understand how modern networks adapt to failures. Observing real-time routing changes provided deeper insight into network behavior.

---

## Conclusion

Dynamic routing using OSPF improves network reliability and scalability. The task successfully demonstrated automatic route management and fault tolerance.

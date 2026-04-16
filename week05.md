# Week 05 – VLAN Configuration using OpenvSwitch

## Student Details
- Name: Baswa  
- Student ID: 12307969  
- Unit: COIT20261  
- Week: 05  

---

# Task 1: VLAN Setup on Switch

## Aim
The aim of this task is to configure VLANs on a managed switch using OpenvSwitch and understand how VLANs isolate network communication.

---

## Network Topology

![Network Topology](screenshot/Vlan-Basics-12307969-network.png)

The network consists of:
- 4 Linux Hosts (PC1, PC2, PC3, PC4)
- 1 OpenvSwitch

Each host is connected to switch ports eth1–eth4. Port eth0 is left unused as required.

---

## IP Address Configuration

All hosts were configured in the same subnet:

| Host | IP Address |
|------|-----------|
| PC1 | 192.168.1.1 |
| PC2 | 192.168.1.2 |
| PC3 | 192.168.1.3 |
| PC4 | 192.168.1.4 |

### Commands Used

```bash
sudo ifconfig eth0 192.168.1.1 netmask 255.255.255.0 up
sudo ifconfig eth0 192.168.1.2 netmask 255.255.255.0 up
sudo ifconfig eth0 192.168.1.3 netmask 255.255.255.0 up
sudo ifconfig eth0 192.168.1.4 netmask 255.255.255.0 up
```

---

## Connectivity Testing (Before VLAN)

All hosts were able to communicate with each other:

```bash
ping 192.168.1.X
```

Result: All pings successful  
Conclusion: All devices are in same broadcast domain  

---

## VLAN Configuration

VLAN IDs based on student ID (12307969):

- VLAN 969 → PC1, PC2  
- VLAN 970 → PC3, PC4  

### Commands Used

```bash
ovs-vsctl set port eth1 tag=969
ovs-vsctl set port eth2 tag=969
ovs-vsctl set port eth3 tag=970
ovs-vsctl set port eth4 tag=970
```

---

## VLAN Verification

```bash
ovs-vsctl show
```

![VLAN Ports](screenshot/Vlan-Basics-12307969-ports.png)

Observation:
- eth1, eth2 → VLAN 969  
- eth3, eth4 → VLAN 970  

---

## Connectivity Testing (After VLAN)

### Same VLAN

```bash
ping 192.168.1.2
```

Result: Successful  

---

### Different VLAN

```bash
ping 192.168.1.3
ping 192.168.1.4
```

Result: Failed  

Conclusion: VLAN successfully isolates network  

---

## ARP Table Verification

```bash
arp -a
```

Observation:
- Only same VLAN devices appear  
- No entries for different VLAN devices  

---

# Task 2: VLAN Routing using Router

## Aim
To configure VLANs on a router and enable communication between VLANs.

---

## Network Topology

![Router Topology](screenshot/Vlan-Router-12307969-network.png)

Components:
- 4 Hosts  
- OpenvSwitch  
- Linux Router  

---

## IP Addressing

### VLAN 969 (Subnet 1)
- PC1 → 10.1.1.1  
- PC2 → 10.1.1.2  

### VLAN 970 (Subnet 2)
- PC3 → 10.1.2.1  
- PC4 → 10.1.2.2  

---

## Switch Configuration

```bash
ovs-vsctl set port eth1 tag=969
ovs-vsctl set port eth2 tag=969
ovs-vsctl set port eth3 tag=970
ovs-vsctl set port eth4 tag=970
```

---

## Trunk Port Configuration

```bash
ovs-vsctl set port eth0 trunks=[]
```

Allows all VLAN traffic to router  

---

## Router Configuration

### Create VLAN Interfaces

```bash
ip link add link eth0 name eth0.969 type vlan id 969
ip link add link eth0 name eth0.970 type vlan id 970
```

---

### Assign IP Addresses

```bash
ip address add 10.1.1.254/24 dev eth0.969
ip address add 10.1.2.254/24 dev eth0.970
```

---

### Enable Interfaces

```bash
ip link set eth0 up
ip link set eth0.969 up
ip link set eth0.970 up
```

---

### Enable Forwarding

```bash
sysctl -w net.ipv4.ip_forward=1
```

---

## Default Gateway Configuration

| Host | Gateway |
|------|--------|
| PC1 | 10.1.1.254 |
| PC2 | 10.1.1.254 |
| PC3 | 10.1.2.254 |
| PC4 | 10.1.2.254 |

---

## Final Connectivity Testing

```bash
ping 10.1.2.1
```

 Result: Successful  

 Conclusion: Inter-VLAN routing working  

---

## Key Learning Outcomes

- VLAN separates broadcast domains  
- Access ports belong to one VLAN  
- Trunk ports carry multiple VLANs  
- VLAN blocks communication between groups  
- Router enables inter-VLAN communication  

---

## Reflection

This task helped me understand how VLANs logically separate networks and improve security. Before VLAN configuration, all hosts were able to communicate freely, but after applying VLANs, communication was restricted based on VLAN membership. This demonstrated the importance of VLANs in controlling broadcast domains.

The second part of the task showed how a router allows communication between VLANs using trunk ports and sub-interfaces. This reflects real-world enterprise network design where segmentation and controlled communication are essential.

---

## Conclusion

The VLAN configuration successfully divided the network into separate logical segments, preventing unnecessary communication. Inter-VLAN routing enabled controlled connectivity between networks. This task provided a strong understanding of switching and routing concepts used in real-world network environments.

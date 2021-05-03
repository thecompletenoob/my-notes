# Junos OS Fundamentals

## Outline

- Describe Junos and its basic design architecture
- Explain traffic processing for transit and exception traffic
- Brief overview of Junos devices

## Junos OS

- Robust modular OS
- Junos OS is compartmentalized into multiple softwarer processes
- Each process has its own memory space and cannot directly interfere with another
- When a single process fails, the entire system doesn't fail
- Built on modified or hardened FreeBSD or Linux
- Single software Release across devices
- Separation of Control and Forwarding planes

### Routing Engine(RE) - Control Plane

![Junos Architecture](/images/Module_1/junos_architecture.png)

- Based on X86 or PowerPC architecture
- Maintains the routing tables, bridging tables and primary forwarding table
- Connects to PFE through an internal link
- Brain of the platform
- Responsible for performing protocol updates and system management
- Runs protocols and management software processes that are in a protected memory space
- Provides:
  - Routing Engine Intelligence Maintenance
  - Chassis Monitoring and Control (Provides CLI and JWeb UIs)
  - PFE Management - Provides L2 and L3 forwarding tables and manages PFE processes

### Packet Forwarding Engine - Forwarding Plane

- Runs on separate hardware
- Forwards transit traffic through the device
- Uses Application-Specific Integrated Circuits(ASICs) for increased performance
- Receives Forwarding Table from RE by the internal link
- Systematically forwards traffic based on its local copy of the forwarding table.
  - The forwarding table on PFE is a synchronized copy of the information created by the RE
  - This enables Junos to forward traffic during control plane instabilities

**Examples** of services implemented through the forwarding engine:

- Policers
- Stateless Firewall Filters
- Class of Service (COS)

### Advantages of Junos OS

- New features can be added without affecting current features
- Junos OS runs on a modified or hardened FreeBSD or Linux
- Junos OS processes run in a protected memory space ensuring that one process cannot directly interfere with another

## Transit Traffic

Transit traffic consists of all traffic that enters an ingress network port, compared against the forwarding table entries and then forwarded out an egress network port.  
Transit traffic is forwarded through the local system.

- Transit traffic passes through the forwarding plane and never sent to the control plane
- PFE uses the forwarding table provided by the RE.

**Examples**:

- Unicast traffic
- Multicast traffic

## Exception Traffic

Unlike transit traffic, exception traffic does not pass through the device. It is processed by the local system and requires some form of handling.  
**Examples** include:

- Packets addressed to the chassis - routing updates, Telnet/SSH sessions, pings, traceroutes and replies to traffic sourced from the RE.
- IP Packets with IP options field
- Traffic that requires the generation of Internet Control Message Protocol (ICMP) - TTL expired messages, destination unreachable messages

## Internal Link between RE and PFE

Exception traffic is rate-limited on the internal link to prevent DoS attacks.  
Control traffic is given preference when there's congestion.  
The built-in rate limiter is not configurable

## Devices

### Routing Devices

- PTX - 460.8 Tbps throughput in a single chassis. Ideal for SPs supercore
- MX - 80 Tbps aggregate half-duplex throughput. Dense access aggregation and provider edge
- ACX - Simplified end to end provisioning

### Switching Devices

Ex - up to 13.2 Tbps throughput. Designed for access aggregation and core deployments from low to high density enterprise/data center.
QFX - High performance, ultra-low latency, feature-rich. For data center use. Provides a ready solution for Juniper's QFabric System.

### Security Devices

- SRX - security services for all kinds of environments

### SDN Products

Group of products designed to integrate Junos into cloud solutions. **Examples** include:

- NFX - CPEs designed to provide fast custom service delivery on demand.
- Contrail Cloud - Suite of software products enabling SDN, network function virtualization and service orchestration. Uses open technologies such as OpenStack
- NorthStar Controller - Brings SDN to WAN. Automates creation of traffic-engineering paths across the network, increasing network utilization and enables customized programming of network configuration.
- WANDL IP/MPLSView - NMS, traffic-engineering, traffic management. It can model and simulate large multi-vendor IP/MPLS networks and generate optimized designs based on traffic patterns and constraints. Provides fault, configs, accounting, performance and security (FCAPS) management for the network.

### Virtual Devices

#### vSRX Virtual Firewall

The Junos OS can be run as a VM using:

- VMWare
- Kernel-based Virtual Machine (KVM)

vSRX protects VM traffic and the virtualized network at the tenant virtual network edge.  

cSRX is a containerized, simplified version of the SRX. Provides faster deployment. Useful in DC environments with fluid virtual topologies.

#### vMX Router

Software emulation of MX hardware is executed down to the level of compiling the microcode of forwarding ASICs into x86 instructions. This ensures that vMX packet handling and forwarding is identical to that of the physical MX router.

## Evolution of Junos

![Evolution of Junos](/images/Module_1/Junos_evolution.png)

### Advantages of Disaggregated Junos

- Separating platform drivers and Forwarding Plane from control plane and running them in another thread improves control plane performance
- In-service software upgrade is facilitated. Different versions of Junos can run in separate VMs.
- Junos VM is now hardware independent and focused on control plane only - speeding up the innovation process
- Virtualization allows users to install third-party applications and tools supported on the various platforms

### Junos Evolved

- Aimed at Data Center/Cloud use cases
- Follows trend of cloud computing where systems leverage pools of hardware
- Daemons have been redesigned to run independent of the kernel
- FreeBSD kernel no longer needed
- Daemons no longer store information they processed. Information is stored in a centralized database as such in the event of a restart or a new instance, the daemon just accesses the database and continues
- 3rd Party Applications can access and write to the database and provide information for administrators and consumers

## Software releases

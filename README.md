ENTERPRISE NETWORK DESIGN 

AND TROUBLESHOOTING LAB 

  

Designed by: Sharanya Gouda 

Technology: Cisco Packet Tracer 

Date: 2026 



 

  

1. PROJECT OVERVIEW 

Objective 

Design, implement, secure, and troubleshoot a three-tier enterprise network simulating a real corporate environment with three departments — HR, Finance, and IT. This project directly demonstrates skills required for enterprise network engineer roles including routing and switching, VLAN configuration, access control, dynamic routing, NAT, and systematic fault resolution. 

Technologies Used 

Cisco VLANs and 802.1q trunking 

Router on a Stick inter-VLAN routing 

OSPF dynamic routing protocol 

NAT overload for internet access 

Extended ACLs for department access control 

Network fault simulation and troubleshooting 

Cisco IOS show commands for diagnosis 

Network Design Decisions 

Router on a Stick was chosen over Layer 3 switch inter-VLAN routing to ensure ACL enforcement on all inter-department traffic. By routing all VLAN traffic through physical router subinterfaces, access control policies are applied consistently to every packet regardless of source or destination department. 

 

  

2. NETWORK TOPOLOGY 

The network follows a three-tier hierarchical design commonly used in enterprise environments: 

Tier 1 — Core:  ISP-Router and Core-Router handling internet connectivity, NAT, and security policy 

Tier 2 — Distribution:  Dist-Switch (3560) aggregating all access switches via trunk links 

Tier 3 — Access:  SW-HR, SW-Finance, SW-IT connecting end user PCs to the network 

 

 

 

 

 

 

Device Inventory 

Device 

Model 

Role 

Location 

ISP-Router 

Cisco 2911 

Simulates internet ISP 

Edge 

Core-Router 

Cisco 2911 

NAT, ACL, inter-VLAN routing 

Core 

Dist-Switch 

Cisco 3560 

Aggregation, trunking 

Distribution 

SW-HR 

Cisco 2960 

HR department access 

Access 

SW-Finance 

Cisco 2960 

Finance department access 

Access 

SW-IT 

Cisco 2960 

IT department access 

Access 

HR-PC1, HR-PC2 

PC-PT 

HR end users 

HR Dept 

Finance-PC1, PC2 

PC-PT 

Finance end users 

Finance Dept 

IT-PC1, IT-PC2 

PC-PT 

IT end users 

IT Dept 

 

  

3. IP ADDRESSING TABLE 

Device 

Interface 

IP Address 

Subnet Mask 

Gateway 

ISP-Router 

Gi0/0 

203.0.113.2 

255.255.255.252 

N/A 

ISP-Router 

Loopback0 

8.8.8.8 

255.255.255.255 

N/A 

Core-Router 

Gi0/0 

203.0.113.1 

255.255.255.252 

N/A 

Core-Router 

Gi0/1.10 

192.168.10.1 

255.255.255.0 

N/A 

Core-Router 

Gi0/1.20 

192.168.20.1 

255.255.255.0 

N/A 

Core-Router 

Gi0/1.30 

192.168.30.1 

255.255.255.0 

N/A 

HR-PC1 

Fa0 

192.168.10.10 

255.255.255.0 

192.168.10.1 

HR-PC2 

Fa0 

192.168.10.11 

255.255.255.0 

192.168.10.1 

Finance-PC1 

Fa0 

192.168.20.10 

255.255.255.0 

192.168.20.1 

Finance-PC2 

Fa0 

192.168.20.11 

255.255.255.0 

192.168.20.1 

IT-PC1 

Fa0 

192.168.30.10 

255.255.255.0 

192.168.30.1 

IT-PC2 

Fa0 

192.168.30.11 

255.255.255.0 

192.168.30.1 

 

  

4. VLAN CONFIGURATION 

VLAN ID 

Name 

Subnet 

Gateway 

Devices 

10 

HR 

192.168.10.0/24 

192.168.10.1 

HR-PC1, HR-PC2 

20 

Finance 

192.168.20.0/24 

192.168.20.1 

Finance-PC1, PC2 

30 

IT 

192.168.30.0/24 

192.168.30.1 

IT-PC1, IT-PC2 

  

Trunk Links 

From 

To 

Encapsulation 

VLANs Allowed 

Core-Router Gi0/1 

Dist-Switch Gi0/1 

802.1q 

10, 20, 30 

Dist-Switch Fa0/1 

SW-HR Gi0/1 

802.1q 

10 

Dist-Switch Fa0/2 

SW-Finance Gi0/1 

802.1q 

20 

Dist-Switch Fa0/3 

SW-IT Gi0/1 

802.1q 

30 

 

  

5. SECURITY POLICY — ACL CONFIGURATION 

Extended ACL named DEPT-POLICY was implemented on Core-Router to enforce department access control. The ACL is applied inbound on all three subinterfaces (Gi0/1.10, Gi0/1.20, Gi0/1.30) ensuring all inter-department traffic is inspected. 

ACL Rules 

Rule 

Source 

Destination 

Action 

Reason 

10 

192.168.30.0/24 (IT) 

Any 

PERMIT 

IT manages all depts 

20 

Any 

192.168.30.0/24 (IT) 

PERMIT 

Allow return traffic to IT 

30 

192.168.20.0/24 (Finance) 

192.168.10.0/24 (HR) 

DENY 

Protect HR data 

40 

192.168.10.0/24 (HR) 

192.168.20.0/24 (Finance) 

DENY 

Protect Finance data 

50 

Any 

Any 

PERMIT 

Allow all other traffic 

  

Verification Results 

Source 

Destination 

Result 

Status 

Finance-PC1 (192.168.20.10) 

HR-PC1 (192.168.10.10) 

BLOCKED 

✓ Policy enforced 

HR-PC1 (192.168.10.10) 

Finance-PC1 (192.168.20.10) 

BLOCKED 

✓ Policy enforced 

IT-PC1 (192.168.30.10) 

HR-PC1 (192.168.10.10) 

ALLOWED 

✓ IT access confirmed 

IT-PC1 (192.168.30.10) 

Finance-PC1 (192.168.20.10) 

ALLOWED 

✓ IT access confirmed 

All PCs 

8.8.8.8 (Internet) 

ALLOWED 

✓ NAT working 

 

  

6. PHASE 4 — FAULT SIMULATION AND TROUBLESHOOTING 

Five real-world network faults were deliberately introduced and resolved using systematic troubleshooting methodology. Each fault simulates a common enterprise network incident. 

Fault 1 — Trunk Link Failure 

Fault Introduced: 

shutdown — interface FastEthernet0/1 on Dist-Switch 

Symptom Observed: 

HR-PC1 lost all network connectivity. ping 192.168.10.1 — request timed out 

Diagnosis Commands: 

show interfaces FastEthernet0/1  →  administratively down detected 

show interfaces trunk  →  Fa0/1 missing from trunk list 

show vlan brief  →  VLAN 10 showing no active ports 

Fix Applied: 

no shutdown — interface FastEthernet0/1 on Dist-Switch 

Verification: 

show interfaces trunk confirmed Fa0/1 trunking. ping 192.168.10.1 from HR-PC1 successful 

  

Fault 2 — VLAN Misconfiguration 

Fault Introduced: 

switchport access vlan 99 — interface FastEthernet0/2 on SW-Finance 

Symptom Observed: 

Finance-PC1 lost all connectivity. ping 192.168.20.1 — request timed out 

Diagnosis Commands: 

show vlan brief  →  Fa0/2 moved to nonexistent VLAN 99 

VLAN 20 showing only Fa0/3 — Fa0/2 missing 

Fix Applied: 

switchport access vlan 20 — interface FastEthernet0/2 on SW-Finance 

Verification: 

show vlan brief confirmed Fa0/2 and Fa0/3 under VLAN 20. ping 192.168.20.1 successful 

  

Fault 3 — Router Interface Failure 

Fault Introduced: 

shutdown — interface GigabitEthernet0/1 on Core-Router 

Symptom Observed: 

All PCs lost complete network connectivity. ping 8.8.8.8 — request timed out 

Diagnosis Commands: 

show ip interface brief  →  Gi0/1 administratively down 

show ip route  →  all subinterfaces down, O routes missing 

Gateway of last resort not set 

Fix Applied: 

no shutdown — interface GigabitEthernet0/1 on Core-Router 

Verification: 

show ip interface brief confirmed Gi0/1 up/up. All O routes restored. ping 8.8.8.8 successful 

  

Fault 4 — OSPF Network Statement Removed 

Fault Introduced: 

no network 172.16.0.0 0.0.0.3 area 0 — router ospf 1 on Core-Router 

Symptom Observed: 

All PCs lost internet access. ping 8.8.8.8 — request timed out 

Diagnosis Commands: 

show ip ospf neighbor  →  blank output, no neighbours 

show ip route  →  no O routes, gateway of last resort not set 

show running-config | section ospf  →  network statement missing 

Fix Applied: 

network 172.16.0.0 0.0.0.3 area 0 — router ospf 1 on Core-Router 

Verification: 

show ip ospf neighbor confirmed FULL state. O routes restored. ping 8.8.8.8 successful 

  

Fault 5 — Wrong Default Gateway on PC 

Fault Introduced: 

Default gateway changed from 192.168.30.1 to 192.168.30.99 on IT-PC1 

Symptom Observed: 

IT-PC1 lost all inter-subnet connectivity. Same subnet ping worked. ping 8.8.8.8 failed 

Diagnosis Commands: 

Observed pattern: same subnet works, outside subnet fails 

This pattern immediately indicates gateway misconfiguration 

Checked IT-PC1 IP Configuration — gateway 192.168.30.99 confirmed incorrect 

Fix Applied: 

Default gateway corrected to 192.168.30.1 on IT-PC1 IP Configuration 

Verification: 

ping 8.8.8.8 successful. ping 192.168.10.10 successful. Full connectivity restored 

  

 

  

7. LESSONS LEARNED 

1. Router on a Stick vs Layer 3 Switching 

Initially configured inter-VLAN routing using Layer 3 switching on the Dist-Switch with SVIs. Discovered that Packet Tracer has limitations enforcing ACLs on virtual SVI interfaces. Redesigned using Router on a Stick which forces all inter-VLAN traffic through physical router subinterfaces enabling reliable ACL enforcement on every packet. 

2. ACL Placement is Critical 

ACLs must be placed where traffic physically passes through. Traffic routed internally by a Layer 3 switch can bypass router ACLs entirely. Understanding traffic flow paths is essential before applying access control policies. 

3. OSPF Dependency Chain 

Removing a single OSPF network statement caused complete loss of internet access for all departments. This demonstrated how critical dynamic routing configuration is and why changes should always be verified with show ip ospf neighbor and show ip route. 

4. Systematic Troubleshooting 

The most effective troubleshooting approach was working layer by layer — physical first (show interfaces), then data link (show vlan brief, show interfaces trunk), then network (show ip route, show ip ospf neighbor). This OSI model approach consistently identified root causes efficiently. 

5. Gateway Misconfiguration Pattern 

A wrong default gateway causes loss of all inter-subnet connectivity while same-subnet communication still works. Recognising this symptom pattern immediately points to a gateway issue without needing extensive diagnosis. 

 

  

8. CONCLUSION 

This project successfully demonstrated the design, implementation, security, and troubleshooting of a three-tier enterprise network. All core network engineering skills were applied and verified including VLAN segmentation, dynamic routing, internet access via NAT, access control enforcement, and systematic fault resolution. 

The skills demonstrated directly align with enterprise network engineering requirements and provide a practical foundation for roles requiring hands-on Cisco IOS experience, network troubleshooting capability, and understanding of enterprise security architecture. 

Future Enhancements 

Add redundant trunk links with STP verification and failover testing 

Implement DHCP server for dynamic IP assignment across all VLANs 

Add wireless access points per department with separate SSIDs 

Configure port security on access switches to prevent unauthorised devices 

Implement QoS policies for voice and video traffic prioritisation 

Add a dedicated management VLAN for network device administration 

 

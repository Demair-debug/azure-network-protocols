
<p align="center">
  <img src="https://github.com/user-attachments/assets/84c2e791-0e06-4e92-b532-9a3c21195fce" width="250" />
  <img src="https://github.com/user-attachments/assets/c0a3d1ee-6a3a-4065-943c-76ae58d30254" width="250" />
  <img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination" width="250" />
</p>


# Network Security Groups (NSGs) and Inspecting Network Traffic in Azure

This project demonstrates how **Network Security Groups (NSGs)** control traffic flow between Azure Virtual Machines and how common **network protocols** can be observed and analyzed using **Wireshark**.

The lab combines cloud networking concepts, packet-level inspection, and security controls to illustrate how traffic is allowed, denied, and observed in Azure environments.

---

## Video Demonstration

*(Coming soon — video walk-through and screen recording will be added)*

---

## Environments and Technologies Used

- Microsoft Azure (Virtual Machines / Compute)
- Azure Virtual Network (VNet)
- Network Security Groups (NSGs)
- Remote Desktop Protocol (RDP)
- Command-line tools (PowerShell, Bash)
- Network protocols:
  - ICMP
  - SSH
  - RDP
  - DNS
  - HTTP / HTTPS
- Wireshark (Protocol Analyzer)

---

## Operating Systems Used

- Windows 10 (21H2)
- Ubuntu Server 20.04

---

## Lab Objective

In this lab, you will:

- Deploy two Azure virtual machines on the same virtual network
- Capture and analyze live network traffic using Wireshark
- Generate traffic using common protocols
- Modify Network Security Group rules to allow and deny traffic
- Correlate NSG behavior with packet-level observations

---

## High-Level Steps

1. Create two Azure virtual machines on the same VNet and subnet
2. Install Wireshark on the Windows virtual machine
3. Observe baseline network traffic
4. Generate and inspect traffic (ICMP, SSH, DNS, HTTP/HTTPS)
5. Modify NSG rules and observe the effect on traffic
6. Validate findings and document results

---

## Actions and Observations

> Keep Wireshark running on the Windows VM throughout the lab to compare baseline traffic with behavior after NSG rule changes.

---

## 1) Create Azure Virtual Machines

Create two virtual machines in Azure using the **same virtual network and subnet**.

### Requirements

- Both VMs must be deployed to the same VNet and subnet
- Both VMs must have private IP addresses
- RDP access to the Windows VM must be allowed (TCP 3389 from your IP)
  > **Note:** Creating `vm-windows` will automatically create the resource group, virtual network (VNet), and default subnet. When creating `vm-linux`, select the existing resource group and VNet created with `vm-windows`, and use the default subnet.

### Windows VM (Traffic Analyzer)

- OS: Windows 10 (21H2)
- Name: `vm-windows`
- Purpose: Wireshark capture and traffic inspection

### Linux VM (Traffic Source)

- OS: Ubuntu Server 20.04
- Name: `vm-linux`
- Purpose: Generate traffic (ICMP, SSH, DNS tests)

<img width="1905" height="904" alt="image" src="https://github.com/user-attachments/assets/c00d9178-cd81-4f82-b837-35d5b4d975ba" />

---
## Identify Virtual Machine Private IP Addresses

Before capturing or generating traffic, note the private IP addresses assigned to each virtual machine. These addresses will be used for ping, SSH, and traffic analysis throughout the lab.

### Windows VM Private IP Address

1. In the Azure Portal, navigate to **Virtual Machines**
2. Select `vm-windows`
3. Click **Networking**
4. Locate the **Private IP address**

<img width="1170" height="735" alt="image" src="https://github.com/user-attachments/assets/099c0a7d-57fe-43ce-8e5d-319cabbbb30a" />


Record this IP address for use in later steps.

### Linux VM Private IP Address

1. In the Azure Portal, navigate to **Virtual Machines**
2. Select `vm-linux`
3. Click **Networking**
4. Locate the **Private IP address**

<img width="1183" height="730" alt="image" src="https://github.com/user-attachments/assets/1f7dfe90-d680-4f29-838a-b0cc9a9b8af1" />


Record this IP address for use in later steps.

---

## 2) Install and Start Wireshark on the Windows VM

1. RDP into `VM-Windows`
2. Download and install Wireshark
3. Launch Wireshark
4. Select the active network interface (typically Ethernet)
   <img width="937" height="698" alt="image" src="https://github.com/user-attachments/assets/8de35509-404e-4072-9ed9-d2a318e03ded" />

5. Start a live packet capture

At this stage, you should already observe background traffic such as ARP, DNS, and TCP handshakes.
<img width="1039" height="738" alt="image" src="https://github.com/user-attachments/assets/4e930568-5cde-4ef1-90a0-ff229f3bfcb8" />

---

## 3) Observe ICMP Traffic (Ping)

ICMP is commonly used to test basic connectivity.

### Generate ICMP traffic

From `VM-Windows`, run:

ping <VM-Linux-private-IP>

### Observe in Wireshark

Apply the display filter:

icmp

You should see ICMP Echo Requests and Echo Replies between the two VMs.
<img width="1573" height="738" alt="image" src="https://github.com/user-attachments/assets/f4a39151-62c8-48ac-8db3-d3dd4d652f10" />

---

## 4) Observe SSH Traffic

SSH traffic is encrypted, making it a good example of secure communication.

### Generate SSH traffic

From `VM-Windows`, run:

ssh <username>@<VM-Linux-private-IP>

Accept the host key if prompted and authenticate.

### Observe in Wireshark

Apply the display filter:

ssh

You will see the SSH session traffic, but the payload will be unreadable due to encryption.
<img width="1668" height="735" alt="image" src="https://github.com/user-attachments/assets/f41d73d6-97d5-47f7-ae67-a026987bdfb0" />

---

## 5) Observe DNS Traffic

DNS traffic demonstrates name resolution behavior.

### Generate DNS traffic

From either VM, run:

nslookup www.google.com

### Observe in Wireshark

Apply the display filter:

dns

Observe DNS query and response packets.
<img width="1468" height="741" alt="image" src="https://github.com/user-attachments/assets/c7abefa6-b09e-49ca-9ccf-b94da1cd12d1" />

---

## 6) Observe HTTP vs HTTPS Traffic

This step highlights the difference between unencrypted and encrypted web traffic.

### Generate HTTP traffic

From `VM-Windows`, browse to:

http://example.com

### Observe in Wireshark
- Apply the filter `http` for HTTP traffic
  
<img width="1453" height="971" alt="image" src="https://github.com/user-attachments/assets/aff958ed-9e14-4ab7-8d9d-68aa1e001b48" />

### Generate HTTPS traffic

From `VM-Windows`, browse to:

https://www.google.com

### Observe in Wireshark
- Apply the filter `tls` for HTTPS traffic

HTTP traffic may display readable metadata, while HTTPS traffic remains encrypted.
<img width="1454" height="971" alt="image" src="https://github.com/user-attachments/assets/d5ea2060-a3b7-4c81-aba7-d2cc96d8da33" />

---

## 7) Modify Network Security Group Rules (Block ICMP)

This step demonstrates how NSGs can deny traffic.

### Identify the NSG

1. In the Azure Portal, open `VM-Linux`
2. Navigate to **Networking**
3. Identify the associated Network Security Group

<img width="1493" height="646" alt="image" src="https://github.com/user-attachments/assets/ee6a8921-ff7a-4711-a5a8-6d68d0ebca17" />

---
## Configure an Inbound NSG Rule to Deny ICMP Traffic

To demonstrate how Network Security Groups control traffic at the network layer, an inbound rule is created to explicitly deny ICMP traffic.

### Steps

1. In the Azure Portal, navigate to the **Network Security Group (NSG)** associated with the Windows virtual machine.
2. Click **+ Create port rule** to create a new **Inbound** security rule.
3. Configure the rule with the following settings:

   - **Source**: `Any`
   - **Source port ranges**: `*`
   - **Destination**: `Any`
   - **Service**: `Custom`
   - **Destination port ranges**: `*`
   - **Protocol**: `ICMPv4`
   - **Action**: `Deny`
   - **Priority**: `200`  
     *(Evaluated before default allow rules)*
   - **Name**: `Deny-ICMP`

5. Click **Add** to save and apply the rule.


### Screenshot Reference

The screenshots below shows the inbound rule configuration, highlighting the protocol selection and deny action used to block ICMP traffic.

<img width="719" height="833" alt="image" src="https://github.com/user-attachments/assets/dd4b3323-fa4d-499f-b9b6-e18aee723365" />

<img width="713" height="838" alt="image" src="https://github.com/user-attachments/assets/2f1623f3-af14-4fc4-a71b-19c9d0fa3a47" />

### Technical Notes

- ICMP does not use ports, which is why **destination port ranges** remain set to `*`.
- Selecting **Service = Custom** allows explicit control over the ICMP protocol.
- Network Security Group rules are processed by priority, with lower numbers evaluated first.  
  Assigning a priority of `200` ensures this rule is enforced before broader allow rules.

### Expected Outcome

- ICMP Echo Requests sent from the Windows VM no longer receive Echo Replies.
- Wireshark captures show outbound ICMP requests without corresponding responses, confirming the NSG rule is actively blocking traffic.

### Why This Matters (Production Relevance)

Network Security Groups function as a distributed firewall in Azure, controlling traffic before it reaches the operating system.  
Being able to intentionally block protocols such as ICMP demonstrates an understanding of:

- Cloud-native network security controls
- Rule evaluation order and priority
- Defense-in-depth principles
- Real-world troubleshooting of connectivity issues caused by security policy

This mirrors how production environments restrict unnecessary protocols to reduce attack surface while allowing required services to function.


---

## 8) Validate NSG Rule Behavior

### Test connectivity again

From `VM-Windows`, run:

ping <VM-Linux-private-IP>

### Expected result

- The ping request should time out
- In Wireshark, ICMP Echo Requests may appear, but no Echo Replies should be received

This confirms the NSG is actively blocking ICMP traffic.
<img width="1593" height="736" alt="image" src="https://github.com/user-attachments/assets/43a5c453-3c46-48b1-bb27-54e2b9241fa3" />

---

## 9) Restore Connectivity

1. Return to the NSG inbound rules
2. Disable or delete the Deny-ICMP rule
3. Test the ping again

ICMP connectivity should be restored.

<img width="1494" height="499" alt="image" src="https://github.com/user-attachments/assets/2dfc751b-a052-4505-8856-aedc494a30fd" />

<img width="1593" height="738" alt="image" src="https://github.com/user-attachments/assets/353b68c4-e09e-4b11-9ad7-caa141dabf75" />

---

## Key Takeaways

- Network Security Groups act as stateful firewalls
- Allowed traffic flows normally and is observable in Wireshark
- Blocked traffic is dropped according to NSG rules
- Encrypted protocols protect payload data while remaining observable
- Packet analysis is a powerful tool for validating security controls

---

## Final Outcome

By completing this lab, you demonstrated:

- Deployment of Azure virtual machines on a shared virtual network
- Packet capture and analysis using Wireshark
- Generation and inspection of common network protocols
- Implementation and validation of NSG rules
- Understanding of how security policies affect real network traffic

These skills are directly applicable to roles in IT support, networking, cloud operations, and cybersecurity.

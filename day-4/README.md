# VPC

---
A **VPC (Virtual Private Cloud)** in AWS **is a logically isolated section of the AWS cloud where you can launch AWS resources, like EC2 instances, within a virtual network that you define.** 

**Key concepts** of VPC:
- **Subnets: Segments of the VPC where resources are placed. Subnets can be public (internet-facing) or private (internal use only).**
- **Route Tables: Control the routing of traffic within the VPC and to/from the internet.**
- **Internet Gateway: Allows resources in a public subnet to access the internet.**
- **Security Groups & Network ACLs (Access Control Lists): Manage inbound and outbound traffic to control access to your resources.**

**VPCs give you control over network configuration, security, and IP addressing, "similar to a traditional on-premises data center, but in the cloud".**

Imagine you want to set up a private, secure, and isolated area in the cloud where you can run your applications and store your data. This is where a VPC comes into play.

**A VPC (Virtual Private Cloud) is a virtual network that you create in the cloud. It allows you to have your own private section of the internet, just like having your own network within a larger network**. Within this VPC, you can create and manage various resources, such as servers, databases, and storage.

Think of it as having your own little "internet" within the bigger internet. **This virtual network is completely isolated from other users' networks, so your data and applications are secure and protected.**

Just like a physical network, **a VPC has its own set of rules and configurations. You can define the IP address range for your VPC and create smaller subnetworks within it called subnets. These subnets help you organize your resources and control how they communicate with each other.**

**To connect your VPC to the internet or other networks, you can set up gateways or routers.** These act as entry and exit points for traffic going in and out of your VPC. You can control the flow of traffic and set up security measures to protect your resources from unauthorized access.

With a VPC, you have control over your network environment. You can define access rules, set up firewalls, and configure security groups to regulate who can access your resources and how they can communicate.

![VPC sample](./img/0_VPC.png)

*By default, when you create an AWS account, AWS will create a default VPC for you* but this default VPC is just to get started with AWS. You should create VPCs for applications or projects. 


---
## VPC components 

The following features help you configure a VPC to provide the connectivity that your applications need:

**1. Virtual private clouds (VPC):**

**A VPC is a virtual network that closely resembles a traditional network that you'd operate in your own data center.** After you create a VPC, you can add subnets.

**2. Subnets:**

**A subnet is a range of IP addresses in your VPC. A subnet must reside in a single Availability Zone.** After you add subnets, you can deploy AWS resources in your VPC.

**3. IP addressing:**

**You can assign IP addresses, both IPv4 and IPv6, to your VPCs and subnets.** You can also bring your public IPv4 and IPv6 GUA addresses to AWS and allocate them to resources in your VPC, such as EC2 instances, NAT gateways, and Network Load Balancers.

**4. Network Access Control List (NACL):**

**A Network Access Control List is a stateless firewall that controls inbound and outbound traffic at the subnet level.** It operates at the IP address level and can allow or deny traffic based on rules that you define. NACLs provide an additional layer of network security for your VPC.
   
**5. Security Group:**

**A security group acts as a virtual firewall for instances (EC2 instances or other resources) within a VPC. It controls inbound and outbound traffic at the instance level.** Security groups allow you to define rules that permit or restrict traffic based on protocols, ports, and IP addresses.  

**6. Routing:**

Use **route tables to determine where network traffic from your subnet or gateway is directed.**

**7. Gateways and endpoints:**

**A gateway connects your VPC to another network.** For example, use an internet gateway to connect your VPC to the internet. Use a VPC endpoint to connect to AWS services privately, without the use of an internet gateway or NAT device.

**8. Peering connections:**

Use a **VPC peering connection to route traffic between the resources in two VPCs.**

**8. Traffic Mirroring:**

**Copy network traffic from network interfaces and send it to security and monitoring appliances for deep packet inspection.**

**9. Transit gateways:**

**Use a transit gateway, which acts as a central hub, to route traffic between your VPCs, VPN connections,** and AWS Direct Connect connections.

**10. VPC Flow Logs:**

**A flow log captures information about the IP traffic going to and from network interfaces in your VPC.**

**11. VPN connections:**

Connect your VPCs to your on-premises networks using AWS Virtual Private Network (AWS VPN).


**12. Resources:** 

VPC with servers in private subnets and NAT

https://docs.aws.amazon.com/vpc/latest/userguide/vpc-example-private-subnets-nat.html


---
![VPC complete sample](./img/1_VPC_complete_sample.png)

### **ELB (Elastic Load Balancer)**
   An **Elastic Load Balancer automatically distributes incoming traffic across multiple targets, such as EC2 instances, containers, or IP addresses, within one or more "availability zones".** It helps improve fault tolerance and availability of your applications. AWS offers three types of load balancers:
   - **Application Load Balancer (ALB): Best for HTTP/HTTPS traffic and routing based on advanced application-layer rules.**
   - **Network Load Balancer (NLB)**: Best for handling high-performance, low-latency TCP/UDP traffic.
   - **Classic Load Balancer (CLB)**: Legacy option for basic load balancing needs.

### **NAT Gateway**
   A **NAT (Network Address Translation) Gateway allows instances in a "private subnet" to access the "internet"** (e.g., to download updates or patches) while keeping them isolated from inbound internet traffic. **It translates private IP addresses to public IP addresses for outbound communication, but the reverse is not allowed.** This ensures that instances in private subnets can maintain internet connectivity without being exposed to inbound internet traffic.

Both ELB and NAT Gateway are essential for managing traffic and ensuring security in cloud environments.


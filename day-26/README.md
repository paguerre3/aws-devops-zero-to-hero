# ELB (Elastic Load Balancer)

**AWS Elastic Load Balancer (ELB) is a fully managed load-balancing service that automatically distributes incoming application or network traffic across multiple targets, such as Amazon EC2 instances, containers, IP addresses, or Lambda functions. ELB improves fault tolerance, scalability, and performance of applications by balancing incoming traffic and routing it to the healthiest and most available resources.**

AWS offers **3 types** of Elastic Load Balancers, each tailored to specific use cases and traffic patterns:



---
### Types of Elastic Load Balancers

**1. Application Load Balancer (ALB):**

   - **Layer 7 Load Balancer: ALB operates at the application layer (Layer 7 of the OSI model), making it well-suited for "HTTP/HTTPS" traffic and advanced routing.**
   - **Use Cases:**
     - **Web applications requiring flexible routing.**
     - Microservices or container-based applications.
     - Host-based or path-based routing.
     - **Redirects (HTTP to HTTPS).**
     - WebSocket support for real-time communication.
   - **Routing Features:**
     - **Host-based routing:** Direct traffic to different targets based on the domain name.
     - **Path-based routing:** Route traffic to specific services based on the URL path (e.g., `/api/*` to one target group and `/app/*` to another).
     - **Content-based routing:** Route requests to different target groups based on the content of the request headers.

**2. Network Load Balancer (NLB):**

   - **Layer 4 Load Balancer: NLB operates at the network layer (Layer 4 of the OSI model) and is designed to handle "TCP/UDP" traffic with low latency.**
   - **Use Cases:**
     - **High-performance applications requiring low latency (e.g., real-time gaming or high-throughput applications).**
     - Applications with TCP or UDP traffic.
     - **When IP address preservation is needed (NLB preserves the client’s IP address).**
     - Load balancing between on-premises infrastructure and AWS via **AWS Direct Connect** or **VPN**.
   - **Performance:**
     - Capable of handling millions of requests per second with ultra-low latency.
     - Ideal for extreme performance requirements.

**3. Gateway Load Balancer (GWLB):**
   - **Layer 3 Load Balancer:** GWLB operates at the network layer and is **used to distribute traffic to** a fleet of virtual appliances, such as **firewalls**, **intrusion detection systems (IDS)**, or **network traffic analyzers**.
   - **Use Cases:**
     - Distributing traffic across third-party virtual appliances in VPCs.
     - **Centralizing traffic inspection or applying security policies.**



---
### Key Features of AWS Elastic Load Balancer

1. **High Availability:**
   **ELB automatically distributes incoming traffic across multiple availability zones (AZs), ensuring that applications remain highly available even if a specific zone or instance becomes unhealthy.**

2. **Automatic Scaling:**
   ELB works seamlessly with **Auto Scaling**, which can automatically add or remove instances from the load balancer target group based on traffic demand.

3. **Health Checks:**
   **ELB performs periodic health checks on registered targets (EC2 instances, containers, etc.) to ensure that traffic is only routed to healthy resources.** If an instance or target becomes unhealthy, it stops sending traffic to it until it recovers.

4. **Security Features:**
   - **Integration with AWS Identity and Access Management (IAM):** Control access to your ELB with IAM policies.
   - **SSL Termination (ALB and NLB):** ELB supports SSL/TLS termination to offload SSL decryption from backend instances, simplifying the management of certificates and encryption.
   - **Security Groups (ALB and NLB):** Define rules to allow or block traffic based on source IP addresses, protocols, and ports.
   - **VPC Integration:** ELB operates within your Virtual Private Cloud (VPC), allowing you to leverage VPC security features like network ACLs and security groups.

5. **Sticky Sessions:**
   - Also known as **"session affinity, sticky sessions" ensure that traffic from a client is routed to the same target instance.** "This is **useful for stateful applications, where session data must be stored on the same server**.

6. **Cross-Zone Load Balancing:**
   - ELB distributes traffic evenly across all instances in different AZs. This improves fault tolerance and ensures even distribution of load.

7. **TLS Termination and HTTPS Support:**
   - ALB and NLB support SSL/TLS termination, which helps you offload the SSL processing from your backend instances. With **AWS Certificate Manager (ACM)**, you can easily manage SSL/TLS certificates.

8. **WebSockets and HTTP/2 Support (ALB):**
   - ALB supports WebSockets for real-time communication and HTTP/2 for improved web performance.



---

### **How AWS Elastic Load Balancer Works**

1. **Traffic Distribution:**
   ELB acts as a single point of access to your application. It distributes incoming traffic across registered targets (EC2 instances, containers, Lambda functions, etc.) in one or more Availability Zones, based on configured routing rules.

2. **Health Checks:**
   ELB continuously monitors the health of registered targets. If a target becomes unhealthy, ELB stops sending traffic to it until it recovers. This ensures only healthy resources serve requests.

3. **Target Groups:**
   - **Application Load Balancer:** Target groups can consist of EC2 instances, Lambda functions, or IP addresses. The ALB directs requests to the target group based on routing rules.
   - **Network Load Balancer:** Target groups can only contain instances or IP addresses.
   - **Health Checks:** Target groups are associated with health checks, allowing ELB to direct traffic only to healthy resources.

---

### **Elastic Load Balancer Pricing**

AWS Elastic Load Balancer pricing is based on the type of load balancer, the number of load balancer hours, and the amount of data processed.

- **Application Load Balancer:** Charges are based on the number of Load Balancer Capacity Units (LCU) and the number of requests processed. An LCU measures the dimensions (like active connections, new connections, and data processed) that impact load balancing.
- **Network Load Balancer:** Pricing is based on the number of hours the load balancer is running and the amount of data processed by the load balancer.
- **Gateway Load Balancer:** Charged based on the number of hours the load balancer is running and the data processed.

---

### **Common Use Cases for AWS ELB**

1. **Web Applications:**
   Use **ALB** to route HTTP and HTTPS requests to your backend EC2 instances or containers. For example, if you are running a microservices architecture, ALB can route traffic based on the path (e.g., `/api/*` goes to one target, and `/app/*` goes to another).
   
2. **High-Performance Networking:**
   Use **NLB** for applications requiring high throughput, low latency, and handling millions of requests per second. For instance, real-time applications like multiplayer gaming servers or financial trading platforms benefit from the performance of NLB.

3. **Security Appliances:**
   Use **Gateway Load Balancer** to distribute traffic across virtual firewalls, intrusion detection systems, and traffic inspection services, ensuring scalability and high availability for security appliances.

4. **Disaster Recovery:**
   With ELB’s multi-AZ support, you can ensure that traffic is automatically routed to healthy instances even if one AZ or instance becomes unavailable, which is essential for disaster recovery setups.

---
### ***Example Architecture with ALB***

In a typical web application architecture:
- **ALB serves as the entry point for all incoming traffic.**
- The **ALB performs SSL/TLS termination and routes the traffic to the appropriate target group "based on the path"** (e.g., `/api/*` routes to the API servers and `/app/*` routes to the front-end servers).
- **The target group consists of EC2 instances in an auto-scaling group spread across multiple Availability Zones** for high availability.
- **Health checks ensure that traffic is only routed to healthy instances.**



---
### ***Example of Terraform Configuration for an ALB***

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_lb" "example_alb" {
  name               = "example-alb"
  load_balancer_type = "application"
  security_groups    = [aws_security_group.lb_sg.id]
  subnets            = aws_subnet.public_subnets[*].id
}

resource "aws_lb_target_group" "example_tg" {
  name     = "example-tg"
  port     = 80
  protocol = "HTTP"
  vpc_id   = aws_vpc.main.id
}

resource "aws_lb_listener" "example_listener" {
  load_balancer_arn = aws_lb.example_alb.arn
  port              = 80
  protocol          = "HTTP"

  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.example_tg.arn
  }
}

resource "aws_lb_target_group_attachment" "example_attachment" {
  target_group_arn = aws_lb_target_group.example_tg.arn
  target_id        = aws_instance.example.id
  port             = 80
}
```

In this example:
- An **ALB** is created, and it listens for HTTP traffic on port 80.
- Traffic is forwarded to an **EC2 instance** in the target group.
- The target group is associated with the VPC, and the instances in the group receive traffic from the ALB.




---
### Conclusion:

**AWS Elastic Load Balancer is a critical component for distributing traffic across multiple resources to ensure**
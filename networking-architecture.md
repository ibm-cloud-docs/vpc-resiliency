---

copyright:
  years: 2023
lastupdated: "2023-12-14"

subcollection: vpc-resiliency

keywords:

---

# Networking architecture decisions
{: #networking-architecture}

The following tables summarize the networking architecture decisions for deploying resilient workloads on IBM Cloud VPC infrastructure.

## Load balancing architecture decisions
{: #load-balancing}

| Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Application Load Balancer | Distribute application requests among available compute instances. | - VPC ALB \n - VPC NLB | VPC ALB | VPC ALB is recommended for web-based workloads. \n - Provides layer 4 and layer 7 load balancing \n - Supports HTTP, HTTPS, and TCP requests \n - Supports SSL offloading. |
| | | | VPC NLB | VPC NLB is recommended for workloads that require low latency and high data throughput or VNF routing. \n - Provides layer 4 load balancing \n - Supports Source IP Preservation \n - Supports Direct Server Return |
| Global Load Balancing | Distribute application requests across regions or fail over application to alternate region in the event of failure in the primary region. | - IBM Cloud DNS (Private) \n - IBM Cloud Internet Services (public) | IBM Cloud Internet Services (public) | The Cloud Internet Services (CIS) Global Load Balancer distributes user requests across regions in multi-region application deployments. |
{: caption="Table 1. Load balancing architecture decisions" caption-side="bottom"}

## Domain name system architecture decisions
{: #dns}

| Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Public DNS | Provide DNS resolution to support use of hostnames instead of IP addresses for applications. | - IBM Cloud Internet Services (CIS) \n - IBM Cloud DNS | IBM Cloud Internet Services (CIS) | IBM Cloud Internet Services supports provisioning and configuring DNS records for public DNS resolution and can be integrated with the public VPC ALBs used for the application's front end. |
| Private DNS | | - IBM Cloud DNS | IBM Cloud DNS | IBM Cloud DNS manages private DNS records, resolves domain names from IBM Cloud's private network, and can be integrated with the private VPC ALBs used for the application's back end. |
{: caption="Table 2. Domain name system (DNS) architecture decisions" caption-side="bottom"}

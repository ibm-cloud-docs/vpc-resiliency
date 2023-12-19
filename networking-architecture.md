---

copyright:
  years: 2023
lastupdated: "2023-12-15"

subcollection: vpc-resiliency

keywords:

---

# Architecture decisions for networking
{: #networking-architecture}

The following tables summarize the networking architecture decisions for deploying resilient workloads on IBM Cloud VPC infrastructure.

## Architecture decisions for load balancing
{: #load-balancing}

| Architecture decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Application load balancing | Distribute application requests among available compute instances. | - VPC ALB \n - VPC NLB | VPC ALB | VPC ALB is recommended for web-based workloads. \n - Provides layer 4 and layer 7 load balancing \n - Supports HTTP, HTTPS, and TCP requests \n - Supports SSL offloading. |
| | | | VPC NLB | VPC NLB is recommended for workloads that require low latency and high data throughput or VNF routing. \n - Provides layer 4 load balancing \n - Supports Source IP Preservation \n - Supports Direct Server Return |
| Global load balancing | Distribute application requests across regions or fail over application to an alternative region if failure in the primary region occurs. | - IBM Cloud DNS (Private) \n - IBM Cloud Internet Services (public) | IBM Cloud Internet Services (public) | IBM Cloud Internet Services provides a global load balancer that can distribute user requests across regions in multi-region application deployments. |
{: caption="Table 1. Architecture decisions for load balancing" caption-side="bottom"}

## Architecture decisions for domain name system
{: #dns}

| Architecture decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Public DNS | Provide DNS resolution to support the use of hostnames instead of IP addresses for applications. | - IBM Cloud Internet Services (CIS) \n - IBM Cloud DNS | IBM Cloud Internet Services (CIS) | IBM Cloud Internet Services supports provisioning and configuring DNS records for public DNS resolution and can be integrated with the public VPC ALBs used for the application's front end. |
| Private DNS | Provide DNS resolution within IBM Cloud's private network. |   IBM Cloud DNS | IBM Cloud DNS | IBM Cloud DNS manages private DNS records, resolves domain names from IBM Cloud's private network, and can be integrated with the private VPC ALBs used for the application's back end. |
{: caption="Table 2. Architecture decisions for domain name system" caption-side="bottom"}

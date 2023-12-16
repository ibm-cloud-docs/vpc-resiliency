---

copyright:
  years: 2023
lastupdated: "2023-12-15"

subcollection: vpc-resiliency

keywords:

---
{{site.data.keyword.attribute-definition-list}}

# Networking design
{: #networking-design}

IBM Cloud virtual private cloud infrastructure and network services support multi-zone and multi-region deployment architectures for high availability and disaster recovery.

Load balancers for VPC are used to distribute traffic across multiple zones within a region and support highly available configurations. There are two types of load balancers for VPC:

- Application load balancers (ALB): Provide layer 4 and layer 7 load balancing, support SSL offloading and are recommended for web-based workloads.

- Network load balancers: Provide layer 4 load balancing and supports source IP address preservation and direct server return. It is recommended for workloads that require low latency and high data throughput.

The following table shows a comparison of these Load Balancers.

| Category       | Network load balancer (NLB)      | Application load balancer (ALB)        |
|------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Features                | - Layer 4 load balancing \n - VNF routing \n - TCP and UDP support \n - Direct server return (DSR) \n - Source IP address preserved                                                                                                                                 | - Layer 4 and 7 load balancing \n - Policy-based routing \n - HTTP, HTTPS, TCP support \n - SSL offloading \n - Source IP address preserved (proxy)            |
| Recommended use         | Workloads that require low latency and high data throughput                                                                                                                                                                                       | Web-based workloads                                                                                                                        |
| HA mode                 | Active-standby (with single VIP)                                                                                                                                                                                                                  | Active-active (with multiple VIPs assigned to a DNS name)                                                                                    |
| Instance group support   | Yes (see [Integrating NLB for VPC with instance groups](https://cloud.ibm.com/docs/vpc?topic=vpc-nlb-integration-with-instance-groups))                                                                                                         | Yes (see [Integrating ALB for VPC with instance groups](https://cloud.ibm.com/docs/vpc?topic=vpc-lbaas-integration-with-instance-groups))  |
| Monitoring metrics       | Yes                                                                                                                                                                                                                                             | Yes                                                                                                                                        |
| Multi-zone support       | Accepts members across all three availability zones. Deploy in each zone along with Global Load Balancer for multi-zone availability (see [Multi-zone support](https://cloud.ibm.com/docs/vpc?topic=vpc-network-load-balancers#nlb-use-case-2)) | Deploy in region and configure subnets across availability zones.                                                                     |
| Security group support   | No                                                                                                                                                                                                                                              | Yes (see [Integrating an ALB for VPC with security groups](https://cloud.ibm.com/docs/vpc?topic=vpc-alb-integration-with-security-groups)) |
| Source IP address preserved      | Yes                                                                                                                                                                                                                                             | Yes ([with proxy protocol](https://cloud.ibm.com/docs/vpc?topic=vpc-advanced-traffic-management#preserving-end-client-ip-address))         |
| SSL offloading          | No                                                                                                                                                                                                                                              | Yes                                                                                                                                        |
| Types of load balancers  | Public and private                                                                                                                                                                                                                              | Public and private                                                                                                                         |
| Virtual IP address (VIP) | Single                                                                                                                                                                                                                                          | Multiple                                                                                                                                   |
| Route mode for VNFs     | Yes (see [Setting up high availability for virtual network functions (VNF)](https://cloud.ibm.com/docs/vpc?topic=vpc-about-vnf-ha))                                                                                                                | No                                                                                                                                         |
| Member type              | Virtual server instances                                                                                                                                                                                                                        | Virtual server instances, Bare Metal, Power Systems Virtual Server                                                                         |
{: caption="Table 1. Comparison of load balancers for VPC" caption-side="bottom"}

{{site.data.keyword.cis_full}} ({{site.data.keyword.cis_short_notm}}) provides a global load balancer that can distribute traffic on the public network across availability zones within a region in a multi-zone deployment or across multiple regions in a multi-region deployment.

In active-active DR deployments, use {{site.data.keyword.cis_full}} to reduce network latency for application users in different geographies. Define location-based pools and configure CIS to redirect users to the closest VPC load balancer based on the geographical location of the user requests.

CIS can also be configured as a proxy to a VPC Application Load Balancer in multi-zone or multi-region deployments to use CIS application security capabilities such as Web Application Firewall (WAF) and Distributed Denial of Service (DDoS) protection. See [Proxying DNS records and global load balancers](https://cloud.ibm.com/docs/cis?topic=cis-dns-concepts#dns-concepts-proxying-dns-records) for details.

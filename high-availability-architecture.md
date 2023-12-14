---

copyright:
  years: 2023
lastupdated: "2023-12-12"

subcollection: whitepaper-vpc-resiliency

keywords:

---

# Resiliency architecture decisions
{: #resiliency-architecture}

The following table summarizes the high availability architecture decisions for deploying resilient workloads on IBM Cloud VPC infrastructure.

## High Availability Architecture Decisions
{: #high-availability}

| Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| High Availability Deployment | - Ensure availability of resources in the event of outages. \n - Support SLA targets for application availability. | - Single-zone, single-region \n - Multi-zone, single-region \n - Multi-zone, multi region | Single-zone, single-region  | **Recommended approach for  low to medium priority applications or non-production workloads** \n * Provides protection from host failures \n  * Supports 99.9% availability |
| | | | Multi-zone, single-region | **Recommended deployment for Multi-Zone Resiliency Pattern** \n - Provides protection from zone outage \n * Supports 99.99% availability for active-active application architecture with virtual servers deployed across 3 zones \n Recommended approach for: \n - Core business applications \n - Production level workloads with stringent resiliency requirements\ n  - Business continuity policies with country boundaries or geo data residence constraints |
| | | | Multi-zone, multi-region | **Recommended deployment for Cross-Region Resiliency Pattern** \n - Provides protection from region outage \n * Supports \>99.99% availability for active-active application architecture with application-aware data replication across regions \n  Recommended approach for: \n * Mission critical applications with continuous or near continuous availability requirements \n * Business continuity policies with cross geo or distance requirements \n * Disaster Recovery |
{: caption="Table 1. High availability architecture decisions" caption-side="bottom"}

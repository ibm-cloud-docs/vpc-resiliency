---

copyright:
  years: 2023
lastupdated: "2023-12-14"

subcollection: pattern-vpc-vsi-cross-region-resiliency

keywords:

---

# Security architecture decisions
{: #security-architecture}

The following tables summarize the security architecture decisions for deploying resilient workloads on IBM Cloud VPC infrastructure.

## Data encryption architecture decisions
{: #encryption-architecture}

| Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Encryption Approach | Encrypt all data to protect from unauthorized disclosure. | - Encryption with provider keys \n - Encryption with customer-managed keys | Encryption with customer-managed keys | Encrypting data with customer-managed keys is recommended to provide added security and customer control often required to meet regulatory compliance requirements. |
| Data Encryption of Application | Encrypt all application data to protect from unauthorized disclosure. | - Storage encryption with customer-managed keys \n - App-level encryption with customer-managed keys | Storage Encryption with customer-managed keys | Application data could be stored on File Storage for VPC, Block Storage for VPC, or Object Storage which support encryption with customer-managed keys by selecting a Key Management Service (KMS) for the respective storage service. |
| Data Encryption of Backups | Encrypt all backup data to protect from unauthorized disclosure. | Storage encryption with customer-managed keys App-level encryption | Storage encryption with customer-managed keys | Backup data could be stored in Object Storage or Block Storage for VPC which support encryption with customer-managed keys by selecting a KMS. |
| Data Encryption of Logs | Encrypt all operational and audit logs at rest to protect from unauthorized disclosure. | Storage encryption with customer-managed keys App-level encryption | Storage encryption with customer-managed keys | All operational and audit logs are stored in Object Storage which supports encryption with customer-managed keys by selecting a KMS. |
{: caption="Table 1. Data encryption architecture decisions" caption-side="bottom"}

## Key management architecture decisions
{: #kms-architecture}

| Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Key Lifecycle Management & HSM | Encrypt data using customer managed keys to protect from unauthorized access.  | Key Protect \n Hyper Protect Crypto Services (HPCS) | Key Protect | Key Protect is recommended for applications that need to comply with regulations requiring encryption of data with customer-managed keys. Key Protect provides key management services using a shared (multi-tenant) FIPS 140-2 Level 3 certified hardware security modules (HSMs). |
| | | | Hyper Protect Crypto Services (HPCS) | HPCS is recommended for financial services and highly regulated industry applications. HPCS provides Key Management Services with the highest level of security and control offered by any cloud provider in the industry. It uses a dedicated (single-tenant) FIPS 140-2 Level 4 certified Hardware Security Module and supports customer-managed master keys, giving the customer exclusive control of the entire key hierarchy. |
| Certificate Management | Protect secrets through their entire lifecycle and secure them using access control measures | Secrets Manager \n BYO Certificate Manager | Secrets Manager | IBM Secrets Manager creates, leases, and centrally manages secrets used by IBM Cloud Services or customer applications. Secrets are stored in a dedicated instance of Secrets Manager and can be encrypted using any of IBM Cloud Key Management Services. |
{: caption="Table 2. Key management architecture decisions" caption-side="bottom"}

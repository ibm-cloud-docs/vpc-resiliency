---

copyright:
  years: 2023
lastupdated: "2023-12-15"

subcollection: vpc-resiliency

keywords:

---

# Security design
{: #security-design}

Data security is important to protect application data from unauthorized disclosure and changes. All application data, including configuration and metadata, as well as all security data, including logs and credentials to access application or cloud resources must be protected.

IBM Cloud data services encrypt data by default by using randomly generated keys and support encryption with customer-managed keys by using a Key Management Service (KMS).

The following table provides an overview of data protection services in IBM Cloud.

| Services        | Secret types        | Use         | Tenancy   | HSM Backed        |
|-------------------------------|----------------------------------------------------------------------|-----------------------------------------|---------------|------------------------|
| Secrets Manager               | Arbitrary secrets IAM credentials Key-value secrets User Credentials | Access to resources                     | Single tenant | N/A                    |
|                               | SSL/TLS certificates                                                 | Data in-transit encryption              |               |                        |
| Key Protect                   | Symmetric encryption keys                                            | Data at-rest encryption TLS/SSL Offload | Multi-tenant  | FIPS 140-2 Level 3 HSM |
| Hyper Protect Crypto Services | Symmetric encryption keys                                            | Data at-rest-encryption TLS/SSL Offload | Single tenant | FIPS 140-2 Level 4 HSM |
{: caption="Table 1. Key features of IBM Cloud data protection services" caption-side="bottom"}

Secrets Manager and Key Management Services in IBM Cloud are regional services that are deployed across multiple availability zones in a region for resiliency. For optimal performance and security, keep the root keys in the same region as the encrypted resources and follow disaster recovery recommendations for each data protection service, as follows:

- To ensure cross-region availability of the secrets, provision a Secrets Manager instance in an alternative region and copy the secrets in the alternative region. For more information, see [Manually backing up secrets](/docs/secrets-manager?topic=secrets-manager-ha-dr&interface=ui#manual-backup).

- If you are using Key Protect, configure the service in the primary region with failover to DR region to enable automatic rerouting of Key Protect requests if a regional service outage occurs. If you are using HPCS, configure failover crypto units in the DR region, and initialize and configure failover crypto units the same as the operational crypto units before the disaster happens, so they are available if a regional outage occurs. For more information, see [Restoring your data by using failover crypto units](/docs/hs-crypto?topic=hs-crypto-restore-data#restore-data-failover-crypto-units).

- "Disaster recovery" does not include the ability to recover from user-initiated accidental deletions. IBM Cloud provides a [30-day grace period to restore a deleted key](/docs/key-protect?topic=key-protect-delete-purge-keys). To protect a key from accidental deletion after the 30-day grace period, the user must create and maintain up-to-date backups of the key.

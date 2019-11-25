---
title: Firewall access rules
description: Configure rules to access an Azure container registry from behind a firewall.
ms.topic: article
ms.date: 07/17/2019
ms.openlocfilehash: 6a0a169f7e5a7e07771cb9fee474b7f4a9391a4e
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455183"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Configure rules to access an Azure container registry behind a firewall

This article explains how to configure rules on your firewall to allow access to an Azure container registry. For example, an Azure IoT Edge device behind a firewall or proxy server might need to access a container registry to pull a container image. Or, a locked-down server in an on-premises network might need access to push an image.

If instead you want to configure inbound network access rules on a container registry to allow access only within an Azure virtual network or a public IP address range, see [Restrict access to an Azure container registry from a virtual network](container-registry-vnet.md).

## <a name="about-registry-endpoints"></a>About registry endpoints

To pull or push images or other artifacts to an Azure container registry, a client such as a Docker daemon needs to interact over HTTPS with two distinct endpoints.

* **Registry REST API endpoint** - Authentication and registry management operations are handled through the registry's public REST API endpoint. This endpoint is the login server URL of the registry, or an associated IP address range. 

* **Storage endpoint** - Azure [allocates blob storage](container-registry-storage.md) in Azure storage accounts on behalf of each registry to manage container images and other artifacts. When a client accesses image layers in an Azure container registry, it makes requests using a storage account endpoint provided by the registry.

If your registry is [geo-replicated](container-registry-geo-replication.md), a client might need to interact with REST and storage endpoints in a specific region or in multiple replicated regions.

## <a name="allow-access-to-rest-and-storage-urls"></a>Allow access to REST and storage URLs

* **REST endpoint** - Allow access to the registry server URL, such as  `myregistry.azurecr.io`
* **Storage endpoint** - Allow access to all Azure blob storage accounts using the wildcard `*.blob.core.windows.net`


## <a name="allow-access-by-ip-address-range"></a>Allow access by IP address range

If you need to allow access to specific IP addresses, download [Azure IP Ranges and Service Tags – Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519).

To find the ACR REST endpoint IP ranges, search for **AzureContainerRegistry** in the JSON file.

> [!IMPORTANT]
> IP address ranges for Azure services can change, and updates are published weekly. Download the JSON file regularly, and make necessary updates in your access rules. If your scenario involves configuring network security group rules in an Azure virtual network to access Azure Container Registry, use the **AzureContainerRegistry** [service tag](#allow-access-by-service-tag) instead.
>

### <a name="rest-ip-addresses-for-all-regions"></a>REST IP addresses for all regions

```json
{
  "name": "AzureContainerRegistry",
  "id": "AzureContainerRegistry",
  "properties": {
    "changeNumber": 10,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.66.140.72/29",
    [...]
```

### <a name="rest-ip-addresses-for-a-specific-region"></a>REST IP addresses for a specific region

Search for the specific region, such as **AzureContainerRegistry.AustraliaEast**.

```json
{
  "name": "AzureContainerRegistry.AustraliaEast",
  "id": "AzureContainerRegistry.AustraliaEast",
  "properties": {
    "changeNumber": 1,
    "region": "australiaeast",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.70.72.136/29",
    [...]
```

### <a name="storage-ip-addresses-for-all-regions"></a>Storage IP addresses for all regions

```json
{
  "name": "Storage",
  "id": "Storage",
  "properties": {
    "changeNumber": 19,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "13.65.107.32/28",
    [...]
```

### <a name="storage-ip-addresses-for-specific-regions"></a>Storage IP addresses for specific regions

Search for the specific region, such as **Storage.AustraliaCentral**.

```json
{
  "name": "Storage.AustraliaCentral",
  "id": "Storage.AustraliaCentral",
  "properties": {
    "changeNumber": 1,
    "region": "australiacentral",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "52.239.216.0/23"
    [...]
```

## <a name="allow-access-by-service-tag"></a>Allow access by service tag

In an Azure virtual network, use network security rules to filter traffic from a resource such as a virtual machine to a container registry. To simplify the creation of the Azure network rules, use the **AzureContainerRegistry** [service tag](../virtual-network/security-overview.md#service-tags). A service tag represents a group of IP address prefixes to access an Azure service globally or per Azure region. The tag is automatically updated when addresses change. 

For example, create an outbound network security group rule with destination **AzureContainerRegistry** to allow traffic to an Azure container registry. To allow access to the service tag only in a specific region, specify the region in the following format: **AzureContainerRegistry**.[*region name*].

## <a name="next-steps"></a>Passos seguintes

* Learn about [Azure best practices for network security](../security/fundamentals/network-best-practices.md)

* Learn more about [security groups](/azure/virtual-network/security-overview) in an Azure virtual network



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->


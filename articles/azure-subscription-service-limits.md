---
title: Azure subscription limits and quotas
description: Provides a list of common Azure subscription and service limits, quotas, and constraints. This article includes information on how to increase limits along with maximum values.
services: multiple
author: rothja
manager: jeffreyg
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: cost-management-billing
ms.topic: article
ms.date: 05/30/2019
ms.author: byvinyal
ms.openlocfilehash: 08c459a3c32b44df2d9e5cf783087dd34d660292
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463293"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Subscrição do Azure e limites de serviço, quotas e restrições
This document lists some of the most common Microsoft Azure limits, which are also sometimes called quotas. This document doesn't currently cover all Azure services. Over time, the list will be expanded and updated to cover more services.

To learn more about Azure pricing, see [Azure pricing overview](https://azure.microsoft.com/pricing/). There, you can estimate your costs by using the [pricing calculator](https://azure.microsoft.com/pricing/calculator/). You also can go to the pricing details page for a particular service, for example, [Windows VMs](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). For tips to help manage your costs, see [Prevent unexpected costs with Azure billing and cost management](billing/billing-getting-started.md).

> [!NOTE]
> If you want to raise the limit or quota above the default limit, [open an online customer support request at no charge](azure-resource-manager/resource-manager-quota-errors.md). The limits can't be raised above the maximum limit value shown in the following tables. If there's no maximum limit column, the resource doesn't have adjustable limits.
>
> [Free Trial subscriptions](https://azure.microsoft.com/offers/ms-azr-0044p) aren't eligible for limit or quota increases. If you have a [Free Trial subscription](https://azure.microsoft.com/offers/ms-azr-0044p), you can upgrade to a [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) subscription. For more information, see [Upgrade your Azure Free Trial subscription to a Pay-As-You-Go subscription](billing/billing-upgrade-azure-subscription.md) and the [Free Trial subscription FAQ](https://azure.microsoft.com/free/free-account-faq).
>

## <a name="limits-and-azure-resource-manager"></a>Limits and Azure Resource Manager
It's now possible to combine multiple Azure resources into a single Azure resource group. When you use resource groups, limits that once were global become managed at a regional level with Azure Resource Manager. For more information about Azure resource groups, see [Azure Resource Manager overview](azure-resource-manager/resource-group-overview.md).

In the following list of limits, a new table reflects any differences in limits when you use Azure Resource Manager. For example, there's a **Subscription limits** table and a **Subscription limits - Azure Resource Manager** table. When a limit applies to both scenarios, it's only shown in the first table. Unless otherwise indicated, limits are global across all regions.

> [!NOTE]
> Quotas for resources in Azure resource groups are per-region accessible by your subscription, not per-subscription as the service management quotas are. Let's use vCPU quotas as an example. To request a quota increase with support for vCPUs, you must decide how many vCPUs you want to use in which regions. You then make a specific request for Azure resource group vCPU quotas for the amounts and regions that you want. If you need to use 30 vCPUs in West Europe to run your application there, you specifically request 30 vCPUs in West Europe. Your vCPU quota isn't increased in any other region--only West Europe has the 30-vCPU quota.
> <!-- -->
> As a result, decide what your Azure resource group quotas must be for your workload in any one region. Then request that amount in each region into which you want to deploy. For help in how to determine your current quotas for specific regions, see [Troubleshoot deployment issues](resource-manager-common-deployment-errors.md).
>
>

## <a name="service-specific-limits"></a>Service-specific limits
* [Active Directory](#active-directory-limits)
* [Gestão de API](#api-management-limits)
* [Serviço de Aplicações](#app-service-limits)
* [Gateway de Aplicação](#application-gateway-limits)
* [Automatização](#automation-limits)
* [Cache do Azure para Redis](#azure-cache-for-redis-limits)
* [Azure Cloud Services](#azure-cloud-services-limits)
* [Azure Cognitive Search](#azure-cognitive-search-limits)
* [Serviços Cognitivos do Azure](#azure-cognitive-services-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [Base de Dados do Azure para MySQL](#azure-database-for-mysql)
* [Base de Dados do Azure para PostgreSQL](#azure-database-for-postgresql)
* [DNS do Azure](#azure-dns-limits)
* [Azure Firewall](#azure-firewall-limits)
* [Funções do Azure](#functions-limits)
* [Serviço Kubernetes do Azure](#azure-kubernetes-service-limits)
* [Azure Machine Learning](#azure-machine-learning-limits)
* [Azure Maps](#azure-maps-limits)
* [Azure Monitor](#azure-monitor-limits)
* [Azure Policy](#azure-policy-limits)
* [Azure SignalR Service](#azure-signalr-service-limits)
* [Cópia de segurança](#backup-limits)
* [Batch](#batch-limits)
* [BizTalk Services](#biztalk-services-limits)
* [Container Instances](#container-instances-limits)
* [Container Registry](#container-registry-limits)
* [Rede de Entrega de Conteúdos](#content-delivery-network-limits)
* [Data Factory](#data-factory-limits)
* [Data Lake Analytics](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [Database Migration Service](#database-migration-service-limits)
* [Event Grid](#event-grid-limits)
* [Hubs de Eventos](#event-hubs-limits)
* [Front Door Service](#azure-front-door-service-limits)
* [Identity Manager](#identity-manager-limits)
* [Centro de IoT](#iot-central-limits)
* [Hub IoT](#iot-hub-limits)
* [Serviço de Aprovisionamento de Dispositivos no Hub IoT](#iot-hub-device-provisioning-service-limits)
* [Cofre de Chaves](#key-vault-limits)
* [Serviços de Multimédia](#media-services-limits)
* [Mobile Services](#mobile-services-limits)
* [Multi-Factor Authentication](#multi-factor-authentication-limits)
* [Redes](#networking-limits)
  * [Gateway de Aplicação](#application-gateway-limits)
  * [Azure Bastion](#azure-bastion-limits)
  * [DNS do Azure](#azure-dns-limits)
  * [Azure Front Door Service](#azure-front-door-service-limits)
  * [Azure Firewall](#azure-firewall-limits)
  * [ExpressRoute](#expressroute-limits)
  * [Balanceador de Carga](#load-balancer)
  * [Network Watcher](#network-watcher-limits)
  * [Endereço IP público](#publicip-address)
  * [Ligação Privada](#private-link-limits)
  * [Gestor de Tráfego](#traffic-manager-limits)
  * [Rede Virtual](#networking-limits)
  * [Virtual WAN](#virtual-wan-limits)
* [Hubs de Notificação](#notification-hubs-limits)
* [Grupo de recursos](#resource-group-limits)
* [Controlo de acesso baseado em funções](#role-based-access-control-limits)
* [Scheduler](#scheduler-limits)
* [Service Bus](#service-bus-limits)
* [Site Recovery](#site-recovery-limits)
* [Base de Dados SQL](#sql-database-limits)
* [SQL Data Warehouse](#sql-data-warehouse-limits)
* [Armazenamento](#storage-limits)
* [StorSimple System](#storsimple-system-limits)
* [Stream Analytics](#stream-analytics-limits)
* [Subscrição](#subscription-limits)
* [Máquinas Virtuais](#virtual-machines-limits)
* [Conjuntos de dimensionamento de máquinas virtuais](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Limites da subscrição
#### <a name="subscription-limits---azure-service-management-classic-deployment-model"></a>Subscription limits - Azure Service Management (classic deployment model)
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Subscription limits - Azure Resource Manager
The following limits apply when you use Azure Resource Manager and Azure resource groups. Limits that haven't changed with Azure Resource Manager aren't listed. See the previous table for those limits.

For information about Resource Manager API read and write limits, see [Throttling Resource Manager requests](resource-manager-request-limits.md).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Resource group limits
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Virtual Machines limits
#### <a name="virtual-machines-limits"></a>Virtual Machines limits
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Virtual Machines limits - Azure Resource Manager
The following limits apply when you use Azure Resource Manager and Azure resource groups.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

#### <a name="shared-image-gallery-limits"></a>Shared Image Gallery limits

There are limits, per subscription, for deploying resources using Shared Image Galleries:
- 100 shared image galleries, per subscription, per region
- 1,000 image definitions, per subscription, per region
- 10,000 image versions, per subscription, per region

### <a name="virtual-machine-scale-sets-limits"></a>Virtual machine scale sets limits
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Container Instances limits
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Container Registry limits
The following table details the features and limits of the Basic, Standard, and Premium [service tiers](./container-registry/container-registry-skus.md).

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="azure-kubernetes-service-limits"></a>Azure Kubernetes Service limits
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="azure-machine-learning-limits"></a>Azure Machine Learning limits
The latest values for Azure Machine Learning Compute quotas can be found in the [Azure Machine Learning quota page](../articles/machine-learning/service/how-to-manage-quotas.md)

### <a name="networking-limits"></a>Limites de rede
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="expressroute-limits"></a>ExpressRoute limits
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="virtual-wan-limits"></a>Virtual WAN limits
[!INCLUDE [virtual-wan-limits](../includes/virtual-wan-limits.md)]

#### <a name="application-gateway-limits"></a>Application Gateway limits

The following table applies to v1, v2, Standard, and WAF SKUs unless otherwise stated.
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Network Watcher limits
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="private-link-limits"></a>Private Link limits
[!INCLUDE [private-link-limits](../includes/private-link-limits.md)]

#### <a name="traffic-manager-limits"></a>Traffic Manager limits
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="azure-bastion-limits"></a>Azure Bastion limits
[!INCLUDE [Azure Bastion limits](../includes/bastion-limits.md)]

#### <a name="azure-dns-limits"></a>Azure DNS limits
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

#### <a name="azure-firewall-limits"></a>Azure Firewall limits
[!INCLUDE [azure-firewall-limits](../includes/firewall-limits.md)]

#### <a name="azure-front-door-service-limits"></a>Azure Front Door Service limits
[!INCLUDE [azure-front-door-service-limits](../includes/front-door-limits.md)]

### <a name="storage-limits"></a>Storage limits
<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

For more information on storage account limits, see [Azure Storage scalability and performance targets](storage/common/storage-scalability-targets.md).

#### <a name="storage-resource-provider-limits"></a>Storage resource provider limits

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Azure Blob storage limits
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Azure Files limits
For more information on Azure Files limits, see [Azure Files scalability and performance targets](storage/files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../includes/storage-files-scale-targets.md)]

#### <a name="azure-file-sync-limits"></a>Azure File Sync limits
[!INCLUDE [storage-sync-files-scale-targets](../includes/storage-sync-files-scale-targets.md)]

#### <a name="azure-queue-storage-limits"></a>Azure Queue storage limits
[!INCLUDE [storage-queues-scale-targets](../includes/storage-queues-scale-targets.md)]

#### <a name="azure-table-storage-limits"></a>Azure Table storage limits
[!INCLUDE [storage-tables-scale-targets](../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>Virtual machine disk limits
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

For more information, see [Virtual machine sizes](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

#### <a name="managed-virtual-machine-disks"></a>Managed virtual machine disks

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>Unmanaged virtual machine disks

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="azure-cloud-services-limits"></a>Azure Cloud Services limits
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="azure-cognitive-services-limits"></a>Azure Cognitive Services limits
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cognitive-services-limits.md)]

### <a name="app-service-limits"></a>App Service limits
The following App Service limits include limits for Web Apps, Mobile Apps, and API Apps.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="functions-limits"></a>Functions limits
[!INCLUDE [functions-limits](../includes/functions-limits.md)]

### <a name="scheduler-limits"></a>Scheduler limits
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Batch limits
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>BizTalk Services limits
The following table shows the limits for Azure BizTalk Services.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Azure Cosmos DB limits
For Azure Cosmos DB limits, see [Limits in Azure Cosmos DB](cosmos-db/concepts-limits.md).

### <a name="azure-database-for-mysql"></a>Base de Dados do Azure para MySQL
For Azure Database for MySQL limits, see [Limitations in Azure Database for MySQL](mysql/concepts-limits.md).

### <a name="azure-database-for-postgresql"></a>Base de Dados do Azure para PostgreSQL
For Azure Database for PostgreSQL limits, see [Limitations in Azure Database for PostgreSQL](postgresql/concepts-limits.md).

### <a name="azure-cognitive-search-limits"></a>Azure Cognitive Search limits
Pricing tiers determine the capacity and limits of your search service. Tiers include:

* **Free** multitenant service, shared with other Azure subscribers, is intended for evaluation and small development projects.
* **Basic** provides dedicated computing resources for production workloads at a smaller scale, with up to three replicas for highly available query workloads.
* **Standard**, which includes S1, S2, S3, and S3 High Density, is for larger production workloads. Multiple levels exist within the Standard tier so that you can choose a resource configuration that best matches your workload profile.

**Limits per subscription**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Limits per search service**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

To learn more about limits on a more granular level, such as document size, queries per second, keys, requests, and responses, see [Service limits in Azure Cognitive Search](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Media Services limits
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="content-delivery-network-limits"></a>Content Delivery Network limits
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Mobile Services limits
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="azure-monitor-limits"></a>Azure Monitor limits

#### <a name="alerts"></a>Alertas

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-alerts.md)]

#### <a name="action-groups"></a>Grupos de ação

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-action-groups.md)]

#### <a name="log-queries-and-language"></a>Log queries and language

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-log-queries.md)]

#### <a name="log-analytics-workspaces"></a>Áreas de trabalho do Log Analytics

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-workspaces.md)]

#### <a name="application-insights"></a>Estatísticas das Aplicações

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-app-insights.md)]




### <a name="notification-hubs-limits"></a>Notification Hubs limits
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Event Hubs limits
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Service Bus limits
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-central-limits"></a>IoT Central limits
[!INCLUDE [iot-central-limits](../includes/iot-central-limits.md)]

### <a name="iot-hub-limits"></a>IoT Hub limits
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="iot-hub-device-provisioning-service-limits"></a>IoT Hub Device Provisioning Service limits
[!INCLUDE [azure-iotdps-limits](../includes/iot-dps-limits.md)]

### <a name="data-factory-limits"></a>Data Factory limits
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Data Lake Analytics limits
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Data Lake Store limits
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="database-migration-service-limits"></a>Database Migration Service Limits
[!INCLUDE [database-migration-service-limits](../includes/database-migration-service-limits.md)]

### <a name="stream-analytics-limits"></a>Stream Analytics limits
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Active Directory limits
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="event-grid-limits"></a>Event Grid limits
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="azure-maps-limits"></a>Azure Maps limits
[!INCLUDE [maps-limits](../includes/maps-limits.md)]

### <a name="azure-policy-limits"></a>Azure Policy limits
[!INCLUDE [policy-limits](../includes/azure-policy-limits.md)]

### <a name="storsimple-system-limits"></a>StorSimple System limits
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="backup-limits"></a>Backup limits
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="azure-signalr-service-limits"></a>Azure SignalR Service limits
[!INCLUDE [signalr-service-limits](../includes/signalr-service-limits.md)]

### <a name="site-recovery-limits"></a>Limites do Site Recovery
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="api-management-limits"></a>API Management limits
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-cache-for-redis-limits"></a>Azure Cache for Redis limits
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Key Vault limits
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication-limits"></a>Multi-Factor Authentication limits
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Automation limits
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="identity-manager-limits"></a>Identity Manager limits
[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

### <a name="role-based-access-control-limits"></a>Role-based access control limits
[!INCLUDE [role-based-access-control-limits](../includes/role-based-access-control-limits.md)]

### <a name="sql-database-limits"></a>SQL Database limits
For SQL Database limits, see [SQL Database resource limits for single databases](sql-database/sql-database-vcore-resource-limits-single-databases.md), [SQL Database resource limits for elastic pools and pooled databases](sql-database/sql-database-vcore-resource-limits-elastic-pools.md), and [SQL Database resource limits for managed instances](sql-database/sql-database-managed-instance-resource-limits.md).

### <a name="sql-data-warehouse-limits"></a>SQL Data Warehouse limits
For SQL Data Warehouse limits, see [SQL Data Warehouse resource limits](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="see-also"></a>Ver também
- [Understand Azure limits and increases](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
- [Virtual machine and cloud service sizes for Azure](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Sizes for Azure Cloud Services](cloud-services/cloud-services-sizes-specs.md)

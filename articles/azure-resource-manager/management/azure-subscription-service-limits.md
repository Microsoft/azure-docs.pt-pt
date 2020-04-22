---
title: Limites e quotas de subscrição do Azure
description: Fornece uma lista de limites comuns de subscrição e serviço do Azure, quotas e constrangimentos. Este artigo inclui informações sobre como aumentar os limites juntamente com os valores máximos.
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 00bd85febe34a7bd9b4e993d5697fa810347711b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768776"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Subscrição do Azure e limites, quotas e limitações do serviço (Azure subscription and service limits, quotas, and constraints)

Este documento enumera alguns dos limites mais comuns do Microsoft Azure, que também são por vezes chamados de quotas.

Para saber mais sobre os preços do Azure, consulte a [visão geral dos preços do Azure.](https://azure.microsoft.com/pricing/) Aí, pode estimar os seus custos utilizando a calculadora de [preços.](https://azure.microsoft.com/pricing/calculator/) Também pode ir à página de dados de preços para um determinado serviço, por exemplo, [VMs do Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). Para obter dicas para ajudar a gerir os seus custos, consulte [Prevenir custos inesperados com faturação azure e gestão de custos.](../../billing/billing-getting-started.md)

## <a name="managing-limits"></a>Limites de gestão

> [!NOTE]
> Alguns serviços têm limites ajustáveis.
>
> Quando um serviço não tem limites ajustáveis, as tabelas seguintes utilizam o **limite**do cabeçalho . Nesses casos, o incumprimento e os limites máximos são os mesmos.
>
> Quando o limite pode ser ajustado, as tabelas incluem **o limite predefinido** e os cabeçalhos **limite máximos.** O limite pode ser elevado acima do limite de incumprimento, mas não acima do limite máximo.
>
> Se pretender elevar o limite ou quota acima do limite de incumprimento, abra um pedido de apoio ao [cliente online gratuitamente.](../templates/error-resource-quota.md)

[As subscrições de Teste Gratuito](https://azure.microsoft.com/offers/ms-azr-0044p) não são elegíveis para aumentos de limites ou quotas. Se tiver uma [subscrição de Teste Gratuito,](https://azure.microsoft.com/offers/ms-azr-0044p)pode fazer upgrade para uma subscrição [Pay-As-You-Go.](https://azure.microsoft.com/offers/ms-azr-0003p/) Para mais informações, consulte [O upgrade da subscrição Do Teste Gratuito Do Azure para uma subscrição Pay-As-You-Go](../../billing/billing-upgrade-azure-subscription.md) e a faQ de [subscrição free Trial](https://azure.microsoft.com/free/free-account-faq).

Alguns limites são geridos a nível regional.

Vamos usar as quotas vCPU como exemplo. Para solicitar um aumento de quota com apoio para vCPUs, deve decidir quantas vCPUs pretende utilizar em que regiões. Em seguida, faça um pedido específico para quotas vCPU do grupo de recursos Azure para os valores e regiões que deseja. Se precisa de utilizar 30 vCPUs na Europa Ocidental para executar a sua candidatura lá, solicita especificamente 30 vCPUs na Europa Ocidental. A sua quota vCPU não é aumentada em nenhuma outra região - só a Europa Ocidental tem a quota de 30 vCPU.

Como resultado, decida quais as suas quotas de grupo de recursos Azure para a sua carga de trabalho em qualquer região. Em seguida, solicite esse montante em cada região para onde pretende implantar. Para obter ajuda na forma de determinar as suas quotas atuais para regiões específicas, consulte [resolver erros para quotas](../templates/error-resource-quota.md)de recursos .

## <a name="general-limits"></a>Limites gerais

Para limites aos nomes de recursos, consulte [regras de nomeação e restrições para os recursos Azure.](resource-name-rules.md)

Para obter informações sobre o Gestor de Recursos API leia e escreva limites, consulte [os pedidos do Throttling Resource Manager](request-limits-and-throttling.md).

### <a name="management-group-limits"></a>Limites de grupo de gestão

Os seguintes limites aplicam-se aos [grupos de gestão.](../../governance/management-groups/overview.md)

[!INCLUDE [management-group-limits](../../../includes/management-group-limits.md)]

### <a name="subscription-limits"></a>Limites da subscrição

Os seguintes limites aplicam-se quando utiliza o Gestor de Recursos Azure e os grupos de recursos Azure.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Limites de grupo de recursos

[!INCLUDE [azure-resource-groups-limits](../../../includes/azure-resource-groups-limits.md)]

## <a name="active-directory-limits"></a>Limites de Diretório Ativo

[!INCLUDE [AAD-service-limits](../../../includes/active-directory-service-limits-include.md)]

## <a name="api-management-limits"></a>Limites de Gestão DaPi

[!INCLUDE [api-management-service-limits](../../../includes/api-management-service-limits.md)]

## <a name="app-service-limits"></a>Limites do serviço de aplicações

Os seguintes limites do Serviço de Aplicações incluem limites para aplicações web, aplicações móveis e aplicações API.

[!INCLUDE [azure-websites-limits](../../../includes/azure-websites-limits.md)]

## <a name="automation-limits"></a>Limites de automação

[!INCLUDE [automation-limits](../../../includes/azure-automation-service-limits.md)]

## <a name="azure-cache-for-redis-limits"></a>Azure Cache para os limites dos Redis

[!INCLUDE [redis-cache-service-limits](../../../includes/redis-cache-service-limits.md)]

## <a name="azure-cloud-services-limits"></a>Limites dos Serviços Azure Cloud

[!INCLUDE [azure-cloud-services-limits](../../../includes/azure-cloud-services-limits.md)]

## <a name="azure-cognitive-search-limits"></a>Limites de pesquisa cognitiva azure

Os níveis de preços determinam a capacidade e os limites do seu serviço de pesquisa. Os níveis incluem:

* **O** serviço gratuito de multiarrendatismo, partilhado com outros subscritores do Azure, destina-se a projetos de avaliação e desenvolvimento de pequenos empreendimentos.
* **O basic** fornece recursos de computação dedicados para cargas de trabalho de produção em menor escala, com até três réplicas para cargas de trabalho altamente disponíveis.
* **A Standard**, que inclui S1, S2, S3 e S3 High Density, destina-se a maiores cargas de trabalho de produção. Existem vários níveis dentro do nível Standard para que possa escolher uma configuração de recurso que melhor corresponda ao seu perfil de carga de trabalho.

**Limites por subscrição**

[!INCLUDE [azure-search-limits-per-subscription](../../../includes/azure-search-limits-per-subscription.md)]

**Limites por serviço de pesquisa**

[!INCLUDE [azure-search-limits-per-service](../../../includes/azure-search-limits-per-service.md)]

Para saber mais sobre limites a um nível mais granular, como tamanho de documento, consultas por segundo, chaves, pedidos e respostas, consulte [os limites de serviço na Pesquisa Cognitiva Azure.](../../search/search-limits-quotas-capacity.md)

## <a name="azure-cognitive-services-limits"></a>Limites dos Serviços Cognitivos Azure

[!INCLUDE [azure-cognitive-services-limits](../../../includes/azure-cognitive-services-limits.md)]

## <a name="azure-cosmos-db-limits"></a>Limites de DB do Azure Cosmos

Para os limites de DB da Azure Cosmos, consulte [Limits in Azure Cosmos DB](../../cosmos-db/concepts-limits.md).

## <a name="azure-data-explorer-limits"></a>Limites do Explorador de Dados Azure

[!INCLUDE [azure-data-explorer-limits](../../../includes/data-explorer-limits.md)]

## <a name="azure-database-for-mysql"></a>Base de Dados do Azure para MySQL

Para a Base de Dados Azure para os limites MySQL, consulte Limitações na Base de [Dados Azure para MySQL](../../mysql/concepts-limits.md).

## <a name="azure-database-for-postgresql"></a>Base de Dados do Azure para PostgreSQL

Para a Base de Dados Azure para os limites postgresQL, consulte Limitações na Base de [Dados Azure para PostgreSQL](../../postgresql/concepts-limits.md).

## <a name="azure-functions-limits"></a>Limites de funções azure

[!INCLUDE [functions-limits](../../../includes/functions-limits.md)]

## <a name="azure-kubernetes-service-limits"></a>Limites de serviço azure Kubernetes

[!INCLUDE [container-service-limits](../../../includes/container-service-limits.md)]

## <a name="azure-machine-learning-limits"></a>Limites de aprendizagem automática azure

Os valores mais recentes para as quotas azure machine learning compute podem ser encontrados na página de [cotações azure machine learning](../../machine-learning/how-to-manage-quotas.md)

## <a name="azure-maps-limits"></a>Limites dos Mapas Azure

[!INCLUDE [maps-limits](../../../includes/maps-limits.md)]

## <a name="azure-monitor-limits"></a>Limites do Monitor Azure

### <a name="alerts"></a>Alertas

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-alerts.md)]

### <a name="action-groups"></a>Grupos de ação

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-action-groups.md)]

### <a name="log-queries-and-language"></a>Consultas de registo e linguagem

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-log-queries.md)]

### <a name="log-analytics-workspaces"></a>Áreas de trabalho do Log Analytics

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-workspaces.md)]

### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-app-insights.md)]

## <a name="azure-policy-limits"></a>Limites da Política Azure

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="azure-signalr-service-limits"></a>Limites de serviço de sinalização Azure

[!INCLUDE [signalr-service-limits](../../../includes/signalr-service-limits.md)]

## <a name="backup-limits"></a>Limites de backup

[!INCLUDE [azure-backup-limits](../../../includes/azure-backup-limits.md)]

## <a name="batch-limits"></a>Limites de lote

[!INCLUDE [azure-batch-limits](../../../includes/azure-batch-limits.md)]

## <a name="classic-deployment-model-limits"></a>Limites clássicos do modelo de implementação

Se utilizar o modelo de implementação clássico em vez do modelo de implementação do Gestor de Recursos Azure, aplicam-se os seguintes limites.

[!INCLUDE [azure-subscription-limits](../../../includes/azure-subscription-limits.md)]

## <a name="container-instances-limits"></a>Limites de instâncias de contentores

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

## <a name="container-registry-limits"></a>Limites do registo de contentores

A tabela seguinte detalha as características e limites dos níveis de [serviço](../../container-registry/container-registry-skus.md)Basic, Standard e Premium.

[!INCLUDE [container-registry-limits](../../../includes/container-registry-limits.md)]

## <a name="content-delivery-network-limits"></a>Limites da Rede de Entrega de Conteúdos

[!INCLUDE [cdn-limits](../../../includes/cdn-limits.md)]

## <a name="data-factory-limits"></a>Limites da Fábrica de Dados

[!INCLUDE [azure-data-factory-limits](../../../includes/azure-data-factory-limits.md)]

## <a name="data-lake-analytics-limits"></a>Limites de Análise do Lago de Dados

[!INCLUDE [azure-data-lake-analytics-limits](../../../includes/azure-data-lake-analytics-limits.md)]

## <a name="data-lake-store-limits"></a>Limites da Data Lake Store

[!INCLUDE [azure-data-lake-store-limits](../../../includes/azure-data-lake-store-limits.md)]

## <a name="data-share-limits"></a>Limites de partilha de dados

[!INCLUDE [azure-data-share-limits](../../../includes/azure-data-share-limits.md)]

## <a name="database-migration-service-limits"></a>Limites do serviço de migração de bases de dados

[!INCLUDE [database-migration-service-limits](../../../includes/database-migration-service-limits.md)]

## <a name="event-grid-limits"></a>Limites da Grelha de Eventos

[!INCLUDE [event-grid-limits](../../../includes/event-grid-limits.md)]

## <a name="event-hubs-limits"></a>Limites dos Hubs de Eventos

[!INCLUDE [azure-servicebus-limits](../../../includes/event-hubs-limits.md)]

## <a name="identity-manager-limits"></a>Limites do Gestor de Identidade

[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

## <a name="iot-central-limits"></a>Limites centrais do IoT
[!INCLUDE [iot-central-limits](../../../includes/iot-central-limits.md)]

## <a name="iot-hub-limits"></a>Limites do Hub IoT

[!INCLUDE [azure-iothub-limits](../../../includes/iot-hub-limits.md)]

## <a name="iot-hub-device-provisioning-service-limits"></a>Limites do serviço de provisionamento de dispositivos IoT Hub

[!INCLUDE [azure-iotdps-limits](../../../includes/iot-dps-limits.md)]

## <a name="key-vault-limits"></a>Limites do cofre chave

[!INCLUDE [key-vault-limits](../../../includes/key-vault-limits.md)]

## <a name="media-services-limits"></a>Limites dos Serviços de Media

[!INCLUDE [azure-mediaservices-limits](../../../includes/media-servieces-limits-quotas-constraints.md)]

### <a name="media-services-v2-legacy"></a>Serviços de Multimédia v2 (legados)

Para limites específicos dos Serviços de Media v2 (legado), consulte [Media Services v2 (legado)](https://docs.microsoft.com/azure/media-services/previous/media-services-quotas-and-limitations)

## <a name="mobile-services-limits"></a>Limites dos Serviços Móveis

[!INCLUDE [mobile-services-limits](../../../includes/mobile-services-limits.md)]

## <a name="multi-factor-authentication-limits"></a>Limites de autenticação de vários fatores

[!INCLUDE [azure-mfa-service-limits](../../../includes/azure-mfa-service-limits.md)]

## <a name="networking-limits"></a>Limites de rede

[!INCLUDE [azure-virtual-network-limits](../../../includes/azure-virtual-network-limits.md)]

### <a name="expressroute-limits"></a>Limites ExpressRoute

[!INCLUDE [expressroute-limits](../../../includes/expressroute-limits.md)]

### <a name="virtual-wan-limits"></a>Limites virtuais de WAN

[!INCLUDE [virtual-wan-limits](../../../includes/virtual-wan-limits.md)]

### <a name="application-gateway-limits"></a>Limites de Gateway de Aplicação

O quadro seguinte aplica-se às V1, v2, Standard e WAF SKUs, salvo indicação em contrário.
[!INCLUDE [application-gateway-limits](../../../includes/application-gateway-limits.md)]

### <a name="network-watcher-limits"></a>Limites do Observador de Rede

[!INCLUDE [network-watcher-limits](../../../includes/network-watcher-limits.md)]

### <a name="private-link-limits"></a>Limites do Private Link

[!INCLUDE [private-link-limits](../../../includes/private-link-limits.md)]

### <a name="traffic-manager-limits"></a>Limites do Gestor de Tráfego

[!INCLUDE [traffic-manager-limits](../../../includes/traffic-manager-limits.md)]

### <a name="azure-bastion-limits"></a>Limites do Bastião Azure

[!INCLUDE [Azure Bastion limits](../../../includes/bastion-limits.md)]

### <a name="azure-dns-limits"></a>Limites de DNS azure

[!INCLUDE [dns-limits](../../../includes/dns-limits.md)]

### <a name="azure-firewall-limits"></a>Limites de Firewall Azure

[!INCLUDE [azure-firewall-limits](../../../includes/firewall-limits.md)]

### <a name="azure-front-door-service-limits"></a>Limites de serviço de porta frontal Azure

[!INCLUDE [azure-front-door-service-limits](../../../includes/front-door-limits.md)]

## <a name="notification-hubs-limits"></a>Limites dos Centros de Notificação

[!INCLUDE [notification-hub-limits](../../../includes/notification-hub-limits.md)]

## <a name="role-based-access-control-limits"></a>Limites de controlo de acesso baseados em funções

[!INCLUDE [role-based-access-control-limits](../../../includes/role-based-access-control-limits.md)]

## <a name="service-bus-limits"></a>Limites de ônibus de serviço

[!INCLUDE [azure-servicebus-limits](../../../includes/service-bus-quotas-table.md)]

## <a name="site-recovery-limits"></a>Limites do Site Recovery

[!INCLUDE [site-recovery-limits](../../../includes/site-recovery-limits.md)]

## <a name="sql-database-limits"></a>Limites de Base de Dados SQL

Para os limites da Base de Dados SQL, consulte os limites de recursos de base de [dados SQL para bases de dados únicas,](../../sql-database/sql-database-vcore-resource-limits-single-databases.md)limites de recursos de base de dados [SQL para piscinas elásticas e bases de dados agrofadas](../../sql-database/sql-database-vcore-resource-limits-elastic-pools.md)e limites de recursos de base de [dados SQL para instâncias geridas](../../sql-database/sql-database-managed-instance-resource-limits.md).

## <a name="sql-data-warehouse-limits"></a>Limites do Armazém de Dados SQL

Para os limites do Armazém de Dados SQL, consulte os limites de recursos do Armazém de [Dados SQL](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="storage-limits"></a>Limites de armazenamento

<!--like # storage accts -->
[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

Para obter mais informações sobre os limites das contas de armazenamento padrão, consulte os objetivos de [escalabilidade para as contas de armazenamento padrão](../../storage/common/scalability-targets-standard-account.md).

### <a name="storage-resource-provider-limits"></a>Limites de fornecedor de recursos de armazenamento

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="azure-blob-storage-limits"></a>Limites de armazenamento de Blob Azure

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

### <a name="azure-files-limits"></a>Limites de Ficheiros Azure

Para obter mais informações sobre os limites dos Ficheiros Azure, consulte [a escalabilidade e os alvos](../../storage/files/storage-files-scale-targets.md)de desempenho do Azure Files .

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-limits"></a>Limites de sincronização de ficheiros Azure

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-queue-storage-limits"></a>Limites de armazenamento da fila Azure

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

### <a name="azure-table-storage-limits"></a>Limites de armazenamento de mesa azul

[!INCLUDE [storage-tables-scale-targets](../../../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
### <a name="virtual-machine-disk-limits"></a>Limites de disco de máquina virtual

[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

Para mais informações, consulte [tamanhos de máquina virtual](../../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="managed-virtual-machine-disks"></a>Discos de máquinas virtuais geridos

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

### <a name="unmanaged-virtual-machine-disks"></a>Discos de máquinas virtuais não geridos

[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="storsimple-system-limits"></a>Limites do sistema StorSimple

[!INCLUDE [storsimple-limits-table](../../../includes/storsimple-limits-table.md)]

## <a name="stream-analytics-limits"></a>Limites de Análise de Fluxo

[!INCLUDE [stream-analytics-limits-table](../../../includes/stream-analytics-limits-table.md)]

## <a name="virtual-machines-limits"></a>Limites das Máquinas Virtuais

### <a name="virtual-machines-limits"></a>Limites das Máquinas Virtuais

[!INCLUDE [azure-virtual-machines-limits](../../../includes/azure-virtual-machines-limits.md)]

### <a name="virtual-machines-limits---azure-resource-manager"></a>Limites das Máquinas Virtuais - Gestor de Recursos Azure

Os seguintes limites aplicam-se quando utiliza o Gestor de Recursos Azure e os grupos de recursos Azure.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="shared-image-gallery-limits"></a>Limites da Galeria de Imagem Partilhada

Existem limites, por subscrição, para a implantação de recursos utilizando galerias de imagem partilhada:

- 100 galerias de imagem partilhada, por subscrição, por região
- 1.000 definições de imagem, por subscrição, por região
- 10.000 versões de imagem, por subscrição, por região

## <a name="virtual-machine-scale-sets-limits"></a>A escala de máquina virtual estabelece limites

[!INCLUDE [virtual-machine-scale-sets-limits](../../../includes/azure-virtual-machine-scale-sets-limits.md)]

## <a name="see-also"></a>Consulte também

* [Compreender limites e aumentos de Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
* [Tamanhos de serviço de máquina virtual e nuvem para Azure](../../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Tamanhos para serviços de nuvem azure](../../cloud-services/cloud-services-sizes-specs.md)
* [Regras de nomenclatura e restrições para recursos do Azure](resource-name-rules.md)

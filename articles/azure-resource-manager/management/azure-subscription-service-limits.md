---
title: Limites e quotas de subscrição do Azure
description: Fornece uma lista de limites comuns de subscrição e serviço da Azure, quotas e constrangimentos. Este artigo inclui informações sobre como aumentar limites, juntamente com valores máximos.
ms.topic: conceptual
ms.date: 03/15/2021
ms.openlocfilehash: 4dd62c926cce765ebc2b21378308650e313716ff
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103471010"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Limites de subscrição e serviços da Azure, quotas e constrangimentos

Este documento enumera alguns dos limites mais comuns do Microsoft Azure, que também são por vezes chamados de quotas.

Para saber mais sobre os preços da Azure, consulte a [visão geral dos preços do Azure](https://azure.microsoft.com/pricing/). Aí, pode estimar os seus custos utilizando [a calculadora de preços.](https://azure.microsoft.com/pricing/calculator/) Também pode ir à página de detalhes de preços para um determinado serviço, por exemplo, [VMs Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). Para obter dicas para ajudar a gerir os seus custos, consulte [Prevenir custos inesperados com faturação da Azure e gestão de custos.](../../cost-management-billing/cost-management-billing-overview.md)

## <a name="managing-limits"></a>Limites de gestão

> [!NOTE]
> Alguns serviços têm limites ajustáveis.
>
> Quando um serviço não tem limites ajustáveis, as seguintes tabelas utilizam o limite do **cabeçalho**. Nesses casos, o incumprimento e os limites máximos são os mesmos.
>
> Quando o limite pode ser ajustado, as tabelas incluem **limite de predefinição** e cabeçalhos **limite máximos.** O limite pode ser elevado acima do limite de incumprimento, mas não acima do limite máximo.
>
> Se pretender elevar o limite ou quota acima do limite por defeito, [abra gratuitamente um pedido de apoio ao cliente online.](../templates/error-resource-quota.md)
>
> Os termos *limite suave* e *limite rígido* são frequentemente usados informalmente para descrever o limite atual, ajustável (limite suave) e o limite máximo (limite máximo). Se um limite não for ajustável, não haverá um limite suave, apenas um limite difícil.
>

[As assinaturas de teste gratuito](https://azure.microsoft.com/offers/ms-azr-0044p) não são elegíveis para aumentos de limites ou quotas. Se tiver uma [subscrição de Teste Gratuito,](https://azure.microsoft.com/offers/ms-azr-0044p)pode fazer upgrade para uma subscrição [Pay-As-You-Go.](https://azure.microsoft.com/offers/ms-azr-0003p/) Para obter mais informações, consulte [a atualização da subscrição do Azure Free Trial para uma subscrição Pay-As-You-Go](../../cost-management-billing/manage/upgrade-azure-subscription.md) e a [subscrição de teste gratuito FAQ](https://azure.microsoft.com/free/free-account-faq).

Alguns limites são geridos a nível regional.

Vamos usar as quotas vCPU como exemplo. Para solicitar um aumento de quota com apoio para vCPUs, você deve decidir quantos vCPUs você quer usar em que regiões. Em seguida, faça um pedido específico para quotas vCPU do grupo de recursos Azure para os montantes e regiões que deseja. Se precisa de utilizar 30 vCPUs na Europa Ocidental para executar a sua aplicação lá, solicite especificamente 30 vCPUs na Europa Ocidental. A sua quota vCPU não é aumentada em nenhuma outra região, só a Europa Ocidental tem a quota de 30 vCPU.

Como resultado, decida quais as suas quotas de grupo de recursos Azure para a sua carga de trabalho em qualquer região. Em seguida, solicite esse montante em cada região em que pretende implantar. Para obter ajuda na forma de determinar as suas quotas atuais para regiões específicas, consulte [os erros de Resolução para quotas de recursos.](../templates/error-resource-quota.md)

## <a name="general-limits"></a>Limites gerais

Para limites de nomes de recursos, consulte [as regras de nomeação e as restrições para os recursos da Azure.](resource-name-rules.md)

Para obter informações sobre a API do Gestor de Recursos, leia e escreva limites, consulte [os pedidos do Gestor de Recursos Throttling](request-limits-and-throttling.md).

### <a name="management-group-limits"></a>Limites do grupo de gestão

Os seguintes limites aplicam-se aos [grupos de gestão.](../../governance/management-groups/overview.md)

[!INCLUDE [management-group-limits](../../../includes/management-group-limits.md)]

### <a name="subscription-limits"></a>Limites da subscrição

Os seguintes limites aplicam-se quando utiliza grupos de recursos Azure Resource Manager e Azure.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Limites de grupo de recursos

[!INCLUDE [azure-resource-groups-limits](../../../includes/azure-resource-groups-limits.md)]

## <a name="active-directory-limits"></a>Limites de diretório ativo

[!INCLUDE [AAD-service-limits](../../../includes/active-directory-service-limits-include.md)]

## <a name="api-management-limits"></a>Limites de gestão da API

[!INCLUDE [api-management-service-limits](../../../includes/api-management-service-limits.md)]

## <a name="app-service-limits"></a>Limites do Serviço de Aplicações

Os seguintes limites do Serviço de Aplicações incluem limites para aplicações web, aplicações móveis e aplicações API.

[!INCLUDE [azure-websites-limits](../../../includes/azure-websites-limits.md)]

## <a name="automation-limits"></a>Limites de automação

[!INCLUDE [automation-limits](../../../includes/azure-automation-service-limits.md)]

## <a name="azure-app-configuration"></a>Configuração da Aplicação Azure

[!INCLUDE [app-configuration-limits](../../../includes/app-configuration-limits.md)]

## <a name="azure-cache-for-redis-limits"></a>Cache Azure para os limites de Redis

[!INCLUDE [redis-cache-service-limits](../../../includes/redis-cache-service-limits.md)]

## <a name="azure-cloud-services-limits"></a>Limites dos Serviços Azure Cloud

[!INCLUDE [azure-cloud-services-limits](../../../includes/azure-cloud-services-limits.md)]

## <a name="azure-cognitive-search-limits"></a>Limites de Pesquisa Cognitiva Azure

Os níveis de preços determinam a capacidade e os limites do seu serviço de pesquisa. Os níveis incluem:

* **O** serviço gratuito multi-inquilino, partilhado com outros subscritores do Azure, destina-se a avaliação e pequenos projetos de desenvolvimento.
* **A Basic** fornece recursos computamento dedicados para cargas de trabalho de produção em menor escala, com até três réplicas para cargas de trabalho de consulta altamente disponíveis.
* **Standard**, que inclui S1, S2, S3 e S3 High Density, destina-se a maiores cargas de trabalho de produção. Existem vários níveis dentro do nível Standard para que possa escolher uma configuração de recurso que melhor corresponda ao seu perfil de carga de trabalho.

**Limites por subscrição**

[!INCLUDE [azure-search-limits-per-subscription](../../../includes/azure-search-limits-per-subscription.md)]

**Limites por serviço de pesquisa**

[!INCLUDE [azure-search-limits-per-service](../../../includes/azure-search-limits-per-service.md)]

Para saber mais sobre limites a um nível mais granular, como tamanho de documento, consultas por segundo, chaves, pedidos e respostas, consulte [os limites de serviço na Pesquisa Cognitiva Azure.](../../search/search-limits-quotas-capacity.md)

## <a name="azure-cognitive-services-limits"></a>Limites dos Serviços Cognitivos Azure

[!INCLUDE [azure-cognitive-services-limits](../../../includes/azure-cognitive-services-limits.md)]

## <a name="azure-cosmos-db-limits"></a>Limites de DB de Azure Cosmos

Para os limites de DB de Azure Cosmos, consulte [Limits in Azure Cosmos DB](../../cosmos-db/concepts-limits.md).

## <a name="azure-data-explorer-limits"></a>Limites do Explorador de Dados Azure

[!INCLUDE [azure-data-explorer-limits](../../../includes/data-explorer-limits.md)]

## <a name="azure-database-for-mysql"></a>Base de Dados do Azure para MySQL

Para a base de dados Azure para os limites do MySQL, consulte [as limitações na Base de Dados Azure para o MySQL](../../mysql/concepts-limits.md).

## <a name="azure-database-for-postgresql"></a>Base de Dados do Azure para PostgreSQL

Para a base de dados Azure para limites pós-SQL, consulte [limitações na Base de Dados Azure para PostgreSQL](../../postgresql/concepts-limits.md).

## <a name="azure-functions-limits"></a>Limites de funções Azure

[!INCLUDE [functions-limits](../../../includes/functions-limits.md)]

Para obter mais informações, consulte [a comparação de planos de hospedagem de funções.](../../azure-functions/functions-scale.md)

## <a name="azure-kubernetes-service-limits"></a>Limites de serviço Azure Kubernetes

[!INCLUDE [container-service-limits](../../../includes/container-service-limits.md)]

## <a name="azure-machine-learning-limits"></a>Limites de aprendizagem automática Azure

Os valores mais recentes para as quotas de cálculo de aprendizagem automática Azure podem ser encontrados na página de quotas de [aprendizagem automática Azure](../../machine-learning/how-to-manage-quotas.md)

## <a name="azure-maps-limits"></a>Limites do Azure Maps

[!INCLUDE [maps-limits](../../../includes/maps-limits.md)]

## <a name="azure-monitor-limits"></a>Limites do Monitor Azure

### <a name="alerts"></a>Alertas

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-alerts.md)]

### <a name="action-groups"></a>Grupos de ação

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-action-groups.md)]

### <a name="autoscale"></a>Dimensionamento Automático

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-autoscale.md)]

### <a name="log-queries-and-language"></a>Consultas de registo e linguagem

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-log-queries.md)]

### <a name="log-analytics-workspaces"></a>Áreas de trabalho do Log Analytics

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-workspaces.md)]

### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../../../includes/application-insights-limits.md)]

## <a name="azure-policy-limits"></a>Limites da Política Azure

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="azure-quantum-limits"></a>Limites quânticos Azure

[!INCLUDE [quantum-limits](../../../includes/azure-quantum-limits.md)]

## <a name="azure-role-based-access-control-limits"></a>Limites de controlo de acesso baseados em função Azure

[!INCLUDE [role-based-access-control-limits](../../../includes/role-based-access-control/limits.md)]

## <a name="azure-signalr-service-limits"></a>Limites de serviço Azure SignalR

[!INCLUDE [signalr-service-limits](../../../includes/signalr-service-limits.md)]

## <a name="azure-vmware-solution-limits"></a>Limites de solução Azure VMware

[!INCLUDE [azure-vmware-solutions-limits](../../azure-vmware/includes/azure-vmware-solutions-limits.md)]

## <a name="backup-limits"></a>Limites de backup

[!INCLUDE [azure-backup-limits](../../../includes/azure-backup-limits.md)]

## <a name="batch-limits"></a>Limites de lote

[!INCLUDE [azure-batch-limits](../../../includes/azure-batch-limits.md)]

## <a name="classic-deployment-model-limits"></a>Limites clássicos do modelo de implantação

Se utilizar o modelo de implementação clássico em vez do modelo de implementação do Gestor de Recursos Azure, aplicam-se os seguintes limites.

[!INCLUDE [azure-subscription-limits](../../../includes/azure-subscription-limits.md)]

## <a name="container-instances-limits"></a>Limites de casos de contentores

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

## <a name="container-registry-limits"></a>Limites do registo de contentores

O quadro seguinte detalha as características e limites dos [níveis](../../container-registry/container-registry-skus.md)de serviço Básico, Standard e Premium.

[!INCLUDE [container-registry-limits](../../../includes/container-registry-limits.md)]

## <a name="content-delivery-network-limits"></a>Limites da Rede de Entrega de Conteúdos

[!INCLUDE [cdn-limits](../../../includes/cdn-limits.md)]

## <a name="data-factory-limits"></a>Limites da Fábrica de Dados

[!INCLUDE [azure-data-factory-limits](../../../includes/azure-data-factory-limits.md)]

## <a name="data-lake-analytics-limits"></a>Limites do Data Lake Analytics

[!INCLUDE [azure-data-lake-analytics-limits](../../../includes/azure-data-lake-analytics-limits.md)]

## <a name="data-lake-storage-limits"></a>Limites de armazenamento do Lago de Dados

[!INCLUDE [azure-data-lake-store-limits](../../../includes/azure-data-lake-store-limits.md)]

## <a name="data-share-limits"></a>Limites de partilha de dados

[!INCLUDE [azure-data-share-limits](../../../includes/azure-data-share-limits.md)]

## <a name="database-migration-service-limits"></a>Limites do serviço de migração de bases de dados

[!INCLUDE [database-migration-service-limits](../../../includes/database-migration-service-limits.md)]

## <a name="digital-twins-limits"></a>Limites de Gémeos Digitais

> [!NOTE]
> Algumas áreas deste serviço têm limites ajustáveis, e outras não. Isto está representado nas tabelas abaixo com a coluna *ajustável?* Quando o limite pode ser ajustado, o valor *ajustável* é *Sim*.

[!INCLUDE [digital-twins-limits](../../../includes/digital-twins-limits.md)]

## <a name="event-grid-limits"></a>Limites da grelha de eventos

[!INCLUDE [event-grid-limits](../../../includes/event-grid-limits.md)]

## <a name="event-hubs-limits"></a>Limites de Centros de Eventos

[!INCLUDE [azure-servicebus-limits](../../../includes/event-hubs-limits.md)]

## <a name="iot-central-limits"></a>Limites centrais IoT
[!INCLUDE [iot-central-limits](../../../includes/iot-central-limits.md)]

## <a name="iot-hub-limits"></a>Limites do Hub IoT

[!INCLUDE [azure-iothub-limits](../../../includes/iot-hub-limits.md)]

## <a name="iot-hub-device-provisioning-service-limits"></a>Limites de serviço de fornecimento de dispositivos ioT hub

[!INCLUDE [azure-iotdps-limits](../../../includes/iot-dps-limits.md)]

## <a name="key-vault-limits"></a>Limites do cofre-chave

[!INCLUDE [key-vault-limits](../../../includes/key-vault-limits.md)]

## <a name="managed-identity-limits"></a>Limites de identidade geridos

[!INCLUDE [Managed-Identity-Limits](../../../includes/managed-identity-limits.md)]


## <a name="media-services-limits"></a>Limites dos Serviços de Mídia

[!INCLUDE [azure-mediaservices-limits](../../../includes/media-servieces-limits-quotas-constraints.md)]

### <a name="media-services-v2-legacy"></a>Serviços de Multimédia v2 (legados)

Para limites específicos dos Serviços de Mídia v2 (legado), consulte [Os Serviços de Mídia v2 (legado)](../../media-services/previous/media-services-quotas-and-limitations.md)

## <a name="mobile-services-limits"></a>Limites de Serviços Móveis

[!INCLUDE [mobile-services-limits](../../../includes/mobile-services-limits.md)]

## <a name="multi-factor-authentication-limits"></a>Limites de autenticação multi-factor

[!INCLUDE [azure-mfa-service-limits](../../../includes/azure-mfa-service-limits.md)]

## <a name="networking-limits"></a>Limites de rede

[!INCLUDE [azure-virtual-network-limits](../../../includes/azure-virtual-network-limits.md)]

### <a name="expressroute-limits"></a>Limites ExpressRoute

[!INCLUDE [expressroute-limits](../../../includes/expressroute-limits.md)]

### <a name="virtual-network-gateway-limits"></a>Limites de Gateway de rede virtual

[!INCLUDE [virtual-network-gateway-limits](../../../includes/azure-virtual-network-gateway-limits.md)]

### <a name="nat-gateway-limits"></a>Limites do GATEWAY NAT

[!INCLUDE [nat-gateway-limits](../../../includes/azure-nat-gateway-limits.md)]

### <a name="virtual-wan-limits"></a>Limites de WAN virtuais

[!INCLUDE [virtual-wan-limits](../../../includes/virtual-wan-limits.md)]

### <a name="application-gateway-limits"></a>Limites do Gateway de Aplicação

O quadro a seguir aplica-se aos SKUs V1, V2, Standard e WAF, salvo indicação em contrário.
[!INCLUDE [application-gateway-limits](../../../includes/application-gateway-limits.md)]

### <a name="network-watcher-limits"></a>Limites do Observador de Rede

[!INCLUDE [network-watcher-limits](../../../includes/network-watcher-limits.md)]

### <a name="private-link-limits"></a>Limites do Private Link

[!INCLUDE [private-link-limits](../../../includes/private-link-limits.md)]

## <a name="purview-limits"></a>Limites de competência

Os valores mais recentes para as quotas Azure Purview podem ser encontrados na página de [quotas Azure Purview](../../purview/how-to-manage-quotas.md)

### <a name="traffic-manager-limits"></a>Limites do Gestor de Tráfego

[!INCLUDE [traffic-manager-limits](../../../includes/traffic-manager-limits.md)]

### <a name="azure-bastion-limits"></a>Limites do Bastião Azure

[!INCLUDE [Azure Bastion limits](../../../includes/bastion-limits.md)]

### <a name="azure-dns-limits"></a>Limites de DNS de Azure

[!INCLUDE [dns-limits](../../../includes/dns-limits.md)]

### <a name="azure-firewall-limits"></a>Limites de Firewall Azure

[!INCLUDE [azure-firewall-limits](../../../includes/firewall-limits.md)]

### <a name="azure-front-door-service-limits"></a>Limites de serviço da porta da frente Azure

[!INCLUDE [azure-front-door-service-limits](../../../includes/front-door-limits.md)]

## <a name="notification-hubs-limits"></a>Limites dos Centros de Notificação

[!INCLUDE [notification-hub-limits](../../../includes/notification-hub-limits.md)]

## <a name="service-bus-limits"></a>Limites de autocarros de serviço

[!INCLUDE [azure-servicebus-limits](../../../includes/service-bus-quotas-table.md)]

## <a name="site-recovery-limits"></a>Limites do Site Recovery

[!INCLUDE [site-recovery-limits](../../../includes/site-recovery-limits.md)]

## <a name="sql-database-limits"></a>Limites de base de dados SQL

Para os limites da base de dados [SQL, consulte os limites de recursos da Base de Dados SQL para bases de dados únicas,](../../azure-sql/database/resource-limits-vcore-single-databases.md) [limites de recursos da Base de Dados SQL para piscinas elásticas e bases de dados em piscinas,](../../azure-sql/database/resource-limits-vcore-elastic-pools.md)e [limites de recursos de base de dados SQL para sql Managed Instance](../../azure-sql/managed-instance/resource-limits.md).

## <a name="azure-synapse-analytics-limits"></a>Limites Azure Synapse Analytics

Para os limites Azure Synapse Analytics, consulte [os limites de recursos da Azure Synapse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="azure-files-and-azure-file-sync"></a>Ficheiros Azure e Sincronização de Ficheiros Azure
Para saber mais sobre os limites para ficheiros Azure e Sincronização de [Ficheiros, consulte os objetivos de escala e desempenho dos Ficheiros Azure.](../../storage/files/storage-files-scale-targets.md)

## <a name="storage-limits"></a>Limites de armazenamento

<!--like # storage accts -->
[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

Para obter mais informações sobre os limites das contas de armazenamento padrão, consulte [os objetivos de Escalaability para contas de armazenamento padrão](../../storage/common/scalability-targets-standard-account.md).

### <a name="storage-resource-provider-limits"></a>Limites do fornecedor de recursos de armazenamento

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="azure-blob-storage-limits"></a>Limites de armazenamento Azure Blob

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

### <a name="azure-queue-storage-limits"></a>Limites de armazenamento da fila Azure

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

### <a name="azure-table-storage-limits"></a>Limites de armazenamento da mesa Azure

[!INCLUDE [storage-tables-scale-targets](../../../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
### <a name="virtual-machine-disk-limits"></a>Limites de disco de máquina virtual

[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

Para obter mais informações, consulte [os tamanhos da máquina virtual.](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

#### <a name="disk-encryption-sets"></a>Conjuntos de encriptação de disco

Há uma limitação de 1000 conjuntos de encriptação de disco por região, por subscrição. Para obter mais informações, consulte a documentação de encriptação para máquinas virtuais [Linux](../../virtual-machines/disk-encryption.md#restrictions) ou [Windows.](../../virtual-machines/disk-encryption.md#restrictions) Se precisar de aumentar a quota, contacte o suporte da Azure.

### <a name="managed-virtual-machine-disks"></a>Discos de máquinas virtuais geridos

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

### <a name="unmanaged-virtual-machine-disks"></a>Discos de máquinas virtuais não geridos

[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="storsimple-system-limits"></a>Limites do Sistema StorSimple

[!INCLUDE [storsimple-limits-table](../../../includes/storsimple-limits-table.md)]

## <a name="stream-analytics-limits"></a>Limites de Análise de Fluxo

[!INCLUDE [stream-analytics-limits-table](../../../includes/stream-analytics-limits-table.md)]

## <a name="virtual-machines-limits"></a>Limites de máquinas virtuais

### <a name="virtual-machines-limits"></a>Limites de máquinas virtuais

[!INCLUDE [azure-virtual-machines-limits](../../../includes/azure-virtual-machines-limits.md)]

### <a name="virtual-machines-limits---azure-resource-manager"></a>Limites de máquinas virtuais - Gestor de Recursos Azure

Os seguintes limites aplicam-se quando utiliza grupos de recursos Azure Resource Manager e Azure.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="shared-image-gallery-limits"></a>Limites da Galeria de Imagens Partilhadas

Existem limites, por subscrição, para a implantação de recursos utilizando galerias de imagem partilhadas:

- 100 galerias de imagem partilhadas, por subscrição, por região
- 1.000 definições de imagem, por subscrição, por região
- 10.000 versões de imagem, por subscrição, por região

## <a name="virtual-machine-scale-sets-limits"></a>Escala de máquina virtual define limites

[!INCLUDE [virtual-machine-scale-sets-limits](../../../includes/azure-virtual-machine-scale-sets-limits.md)]

## <a name="see-also"></a>Ver também

* [Compreender os limites e aumentos do Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
* [Tamanhos de serviço de máquina virtual e nuvem para Azure](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Tamanhos para Serviços cloud Azure](../../cloud-services/cloud-services-sizes-specs.md)
* [Regras de nomenclatura e restrições para recursos do Azure](resource-name-rules.md)

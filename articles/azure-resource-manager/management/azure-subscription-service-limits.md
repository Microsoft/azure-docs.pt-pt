---
title: Cotas e limites de assinatura do Azure
description: Fornece uma lista de assinaturas e limites de serviço, cotas e restrições comuns do Azure. Este artigo inclui informações sobre como aumentar os limites juntamente com os valores máximos.
tags: billing
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: c5714dd30be4d557086667a74fff2ff8117e2a2b
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901418"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Subscrição do Azure e limites de serviço, quotas e restrições

Este documento lista alguns dos limites de Microsoft Azure mais comuns, que também são chamados de cotas. Este documento não abrange atualmente todos os serviços do Azure. Ao longo do tempo, a lista será expandida e atualizada para abranger mais serviços.

Para saber mais sobre os preços do Azure, consulte [visão geral de preços do Azure](https://azure.microsoft.com/pricing/). Lá, você pode estimar seus custos usando a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/). Você também pode ir para a página de detalhes de preços de um serviço específico, por exemplo, [VMs do Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). Para obter dicas para ajudar a gerenciar seus custos, confira [evitar custos inesperados com o gerenciamento de custos e cobrança do Azure](../../billing/billing-getting-started.md).

Para limites aos nomes de recursos, consulte [regras de nomeação e restrições para os recursos Azure.](resource-name-rules.md)

> [!NOTE]
> Se você quiser aumentar o limite ou a cota acima do limite padrão, [abra uma solicitação de atendimento ao cliente online sem encargos](../templates/error-resource-quota.md). Os limites não podem ser gerados acima do valor de limite máximo mostrado nas tabelas a seguir. Se não houver uma coluna de limite máximo, o recurso não terá limites ajustáveis.
>
> As [assinaturas de avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p) não estão qualificadas para aumentos de cota ou limite. Se tiver um [subscrição de avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p), pode atualizar para uma [pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) subscrição. Para obter mais informações, consulte [atualizar sua assinatura de avaliação gratuita do Azure para uma assinatura paga conforme o uso](../../billing/billing-upgrade-azure-subscription.md) e as [perguntas frequentes sobre assinatura de avaliação gratuita](https://azure.microsoft.com/free/free-account-faq).
>

## <a name="limits-and-azure-resource-manager"></a>Limites e Azure Resource Manager

Você pode combinar vários recursos Azure em um único grupo de recursos Azure. Quando você usa grupos de recursos, os limites que eram globais se tornam gerenciados em um nível regional com Azure Resource Manager. Para obter mais informações sobre grupos de recursos do Azure, consulte [visão geral de Azure Resource Manager](overview.md).

Na lista de limites a seguir, uma nova tabela reflete as diferenças em limites quando você usa Azure Resource Manager. Por exemplo, há uma tabela de **limites de assinatura** e um **limite de assinatura-Azure Resource Manager** tabela. Quando um limite se aplica a ambos os cenários, ele só é mostrado na primeira tabela. Salvo indicação em contrário, os limites são globais em todas as regiões.

> [!NOTE]
> As cotas para recursos em grupos de recursos do Azure são acessíveis por região por meio de sua assinatura, não por assinatura, já que as cotas de gerenciamento de serviço são. Vamos usar as cotas do vCPU como exemplo. Para solicitar um aumento de cota com suporte para vCPUs, você deve decidir quantas vCPUs deseja usar em quais regiões. Em seguida, você faz uma solicitação específica para as cotas de vCPU do grupo de recursos do Azure para os valores e as regiões desejadas. Se você precisar usar 30 vCPUs em Europa Ocidental para executar seu aplicativo lá, solicite especificamente 30 vCPUs em Europa Ocidental. Sua cota de vCPU não é aumentada em nenhuma outra região-somente Europa Ocidental tem a cota de 30 vCPU.
> <!-- -->
> Como resultado, decida quais suas cotas do grupo de recursos do Azure devem ser para sua carga de trabalho em qualquer região. Em seguida, solicite esse valor em cada região na qual você deseja implantar. Para obter ajuda sobre como determinar suas cotas atuais para regiões específicas, consulte [solucionar problemas de implantação](../templates/common-deployment-errors.md).
>
>

## <a name="service-specific-limits"></a>Limites específicos do serviço

* [Active Directory](#active-directory-limits)
* [Gestão de API](#api-management-limits)
* [Serviço de Aplicações](#app-service-limits)
* [Gateway de Aplicação](#application-gateway-limits)
* [Automatização](#automation-limits)
* [Cache do Azure para Redis](#azure-cache-for-redis-limits)
* [Serviços Cloud do Azure](#azure-cloud-services-limits)
* [Pesquisa Cognitiva do Azure](#azure-cognitive-search-limits)
* [Serviços Cognitivos do Azure](#azure-cognitive-services-limits)
* [BD do Cosmos para o Azure](#azure-cosmos-db-limits)
* [Data Explorer do Azure](#azure-data-explorer-limits)
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
* [Serviço de Signaler do Azure](#azure-signalr-service-limits)
* [Cópia de segurança](#backup-limits)
* [Batch](#batch-limits)
* [Serviços BizTalk](#biztalk-services-limits)
* [Container Instances](#container-instances-limits)
* [Container Registry](#container-registry-limits)
* [Rede de Entrega de Conteúdos](#content-delivery-network-limits)
* [Data Factory](#data-factory-limits)
* [Data Lake Analytics](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [Database Migration Service](#database-migration-service-limits)
* [Event Grid](#event-grid-limits)
* [Hubs de Eventos](#event-hubs-limits)
* [Serviço de porta frontal](#azure-front-door-service-limits)
* [Gerenciador de identidades](#identity-manager-limits)
* [Centro de IoT](#iot-central-limits)
* [Hub IoT](#iot-hub-limits)
* [Serviço de Aprovisionamento de Dispositivos no Hub IoT](#iot-hub-device-provisioning-service-limits)
* [Cofre de Chaves](#key-vault-limits)
* [Serviços de Multimédia](#media-services-limits)
* [Serviços móveis](#mobile-services-limits)
* [Multi-Factor Authentication](#multi-factor-authentication-limits)
* [Redes](#networking-limits)
  * [Gateway de Aplicação](#application-gateway-limits)
  * [Bastiões do Azure](#azure-bastion-limits)
  * [DNS do Azure](#azure-dns-limits)
  * [Azure Front Door Service](#azure-front-door-service-limits)
  * [Azure Firewall](#azure-firewall-limits)
  * [ExpressRoute](#expressroute-limits)
  * [Balanceador de Carga](#load-balancer)
  * [Observador de rede](#network-watcher-limits)
  * [Endereço IP público](#publicip-address)
  * [Ligação Privada](#private-link-limits)
  * [Gestor de Tráfego](#traffic-manager-limits)
  * [Rede Virtual](#networking-limits)
  * [WAN virtual](#virtual-wan-limits)
* [Hubs de Notificação](#notification-hubs-limits)
* [Grupo de recursos](#resource-group-limits)
* [Controlo de acesso baseado em funções](#role-based-access-control-limits)
* [Scheduler](#scheduler-limits)
* [Service Bus](#service-bus-limits)
* [Site Recovery](#site-recovery-limits)
* [Base de Dados SQL](#sql-database-limits)
* [SQL Data Warehouse](#sql-data-warehouse-limits)
* [Armazenamento](#storage-limits)
* [Sistema StorSimple](#storsimple-system-limits)
* [Stream Analytics](#stream-analytics-limits)
* [Subscrição](#subscription-limits)
* [Máquinas Virtuais](#virtual-machines-limits)
* [Conjuntos de dimensionamento de máquinas virtuais](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Limites da subscrição

#### <a name="subscription-limits---azure-service-management-classic-deployment-model"></a>Limites de assinatura-gerenciamento de serviços do Azure (modelo de implantação clássico)

[!INCLUDE [azure-subscription-limits](../../../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Limites de assinatura-Azure Resource Manager

Os limites a seguir se aplicam quando você usa Azure Resource Manager e grupos de recursos do Azure. Os limites que não foram alterados com Azure Resource Manager não estão listados. Consulte a tabela anterior para esses limites.

Para obter informações sobre os limites de leitura e gravação da API do Resource Manager, consulte [limitação de solicitações do Resource Manager](request-limits-and-throttling.md).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Limites de grupo de recursos

[!INCLUDE [azure-resource-groups-limits](../../../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Limites de máquinas virtuais

#### <a name="virtual-machines-limits"></a>Limites de máquinas virtuais

[!INCLUDE [azure-virtual-machines-limits](../../../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Limites de máquinas virtuais-Azure Resource Manager

Os limites a seguir se aplicam quando você usa Azure Resource Manager e grupos de recursos do Azure.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

#### <a name="shared-image-gallery-limits"></a>Limites da Galeria de imagens compartilhadas

Há limites, por assinatura, para implantar recursos usando galerias de imagens compartilhadas:

- 100 galerias de imagens compartilhadas, por assinatura, por região
- 1\.000 definições de imagem, por assinatura, por região
- 10.000 versões de imagem, por assinatura, por região

### <a name="virtual-machine-scale-sets-limits"></a>Limites dos conjuntos de dimensionamento de máquinas virtuais

[!INCLUDE [virtual-machine-scale-sets-limits](../../../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Limites de instâncias de contêiner

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Limites de registro de contêiner

A tabela a seguir fornece detalhes dos recursos e limites das [camadas de serviço](../../container-registry/container-registry-skus.md)Basic, Standard e Premium.

[!INCLUDE [container-registry-limits](../../../includes/container-registry-limits.md)]

### <a name="azure-kubernetes-service-limits"></a>Limites do serviço kubernetes do Azure

[!INCLUDE [container-service-limits](../../../includes/container-service-limits.md)]

### <a name="azure-machine-learning-limits"></a>Limites de Azure Machine Learning

Os valores mais recentes para Azure Machine Learning cotas de computação podem ser encontrados na [página de cota de Azure Machine Learning](../../machine-learning/how-to-manage-quotas.md)

### <a name="networking-limits"></a>Limites de rede

[!INCLUDE [azure-virtual-network-limits](../../../includes/azure-virtual-network-limits.md)]

#### <a name="expressroute-limits"></a>Limites de ExpressRoute

[!INCLUDE [expressroute-limits](../../../includes/expressroute-limits.md)]

#### <a name="virtual-wan-limits"></a>Limites de WAN virtual

[!INCLUDE [virtual-wan-limits](../../../includes/virtual-wan-limits.md)]

#### <a name="application-gateway-limits"></a>Limites do gateway de aplicativo

A tabela a seguir aplica-se aos SKUs v1, v2, Standard e WAF, salvo indicação em contrário.
[!INCLUDE [application-gateway-limits](../../../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Limites do observador de rede

[!INCLUDE [network-watcher-limits](../../../includes/network-watcher-limits.md)]

#### <a name="private-link-limits"></a>Limites de link privado

[!INCLUDE [private-link-limits](../../../includes/private-link-limits.md)]

#### <a name="traffic-manager-limits"></a>Limites do Gerenciador de tráfego

[!INCLUDE [traffic-manager-limits](../../../includes/traffic-manager-limits.md)]

#### <a name="azure-bastion-limits"></a>Limites de bastiões do Azure

[!INCLUDE [Azure Bastion limits](../../../includes/bastion-limits.md)]

#### <a name="azure-dns-limits"></a>Limites de DNS do Azure

[!INCLUDE [dns-limits](../../../includes/dns-limits.md)]

#### <a name="azure-firewall-limits"></a>Limites de firewall do Azure

[!INCLUDE [azure-firewall-limits](../../../includes/firewall-limits.md)]

#### <a name="azure-front-door-service-limits"></a>Limites de serviço de porta frontal do Azure

[!INCLUDE [azure-front-door-service-limits](../../../includes/front-door-limits.md)]

### <a name="storage-limits"></a>Limites de armazenamento

<!--like # storage accts -->
[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

Para obter mais informações sobre os limites para contas de armazenamento padrão, consulte [metas de escalabilidade para contas de armazenamento padrão](../../storage/common/scalability-targets-standard-account.md).

#### <a name="storage-resource-provider-limits"></a>Limites do provedor de recursos de armazenamento

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Limites de armazenamento de BLOBs do Azure

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Limites de arquivos do Azure

Para obter mais informações sobre os limites de arquivos do Azure, consulte [metas de desempenho e escalabilidade de arquivos do Azure](../../storage/files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

#### <a name="azure-file-sync-limits"></a>Limites de Sincronização de Arquivos do Azure

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

#### <a name="azure-queue-storage-limits"></a>Limites de armazenamento de filas do Azure

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

#### <a name="azure-table-storage-limits"></a>Limites de armazenamento de tabelas do Azure

[!INCLUDE [storage-tables-scale-targets](../../../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>Limites de disco de máquina virtual

[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

Para obter mais informações, consulte [tamanhos de máquina virtual](../../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

#### <a name="managed-virtual-machine-disks"></a>Discos de máquina virtual gerenciados

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>Discos de máquina virtual não gerenciados

[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="azure-cloud-services-limits"></a>Limites dos serviços de nuvem do Azure

[!INCLUDE [azure-cloud-services-limits](../../../includes/azure-cloud-services-limits.md)]

### <a name="azure-cognitive-services-limits"></a>Limites de serviços cognitivas do Azure

[!INCLUDE [azure-cloud-services-limits](../../../includes/azure-cognitive-services-limits.md)]

### <a name="app-service-limits"></a>Limites do serviço de aplicativo

Os limites do serviço de aplicativo a seguir incluem limites para aplicativos Web, aplicativos móveis e aplicativos de API.

[!INCLUDE [azure-websites-limits](../../../includes/azure-websites-limits.md)]

### <a name="functions-limits"></a>Limites de funções

[!INCLUDE [functions-limits](../../../includes/functions-limits.md)]

### <a name="scheduler-limits"></a>Limites do Agendador

[!INCLUDE [scheduler-limits-table](../../../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Limites de lote

[!INCLUDE [azure-batch-limits](../../../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>Limites dos serviços BizTalk

A tabela a seguir mostra os limites para os serviços BizTalk do Azure.

[!INCLUDE [biztalk-services-service-limits](../../../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Limites de Azure Cosmos DB

Para limites de Azure Cosmos DB, consulte [limites em Azure Cosmos DB](../../cosmos-db/concepts-limits.md).

### <a name="azure-data-explorer-limits"></a>Limites de Data Explorer do Azure

[!INCLUDE [azure-data-explorer-limits](../../../includes/data-explorer-limits.md)]

### <a name="azure-database-for-mysql"></a>Base de Dados do Azure para MySQL

Para limites do banco de dados do Azure para MySQL, consulte [limitações no banco de dados do Azure para MySQL](../../mysql/concepts-limits.md).

### <a name="azure-database-for-postgresql"></a>Base de Dados do Azure para PostgreSQL

Para limites do banco de dados do Azure para PostgreSQL, consulte [limitações no banco de dados do Azure para PostgreSQL](../../postgresql/concepts-limits.md).

### <a name="azure-cognitive-search-limits"></a>Limites de Pesquisa Cognitiva do Azure

Os tipos de preço determinam a capacidade e os limites de seu serviço de pesquisa. As camadas incluem:

* O serviço multilocatário **gratuito** , compartilhado com outros assinantes do Azure, destina-se a avaliação e projetos de desenvolvimento pequenos.
* O **básico** fornece recursos de computação dedicados para cargas de trabalho de produção em uma escala menor, com até três réplicas para cargas de trabalho de consulta altamente disponíveis.
* O **padrão**, que inclui a alta densidade S1, S2, S3 e S3, é para cargas de trabalho de produção maiores. Existem vários níveis na camada Standard para que você possa escolher uma configuração de recurso que melhor corresponda ao perfil de carga de trabalho.

**Limites por assinatura**

[!INCLUDE [azure-search-limits-per-subscription](../../../includes/azure-search-limits-per-subscription.md)]

**Limites por serviço de pesquisa**

[!INCLUDE [azure-search-limits-per-service](../../../includes/azure-search-limits-per-service.md)]

Para saber mais sobre os limites em um nível mais granular, como tamanho do documento, consultas por segundo, chaves, solicitações e respostas, confira [limites de serviço no Azure pesquisa cognitiva](../../search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Limites dos serviços de mídia

[!INCLUDE [azure-mediaservices-limits](../../../includes/azure-mediaservices-limits.md)]

### <a name="content-delivery-network-limits"></a>Limites de rede de distribuição de conteúdo

[!INCLUDE [cdn-limits](../../../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Limites de serviços móveis

[!INCLUDE [mobile-services-limits](../../../includes/mobile-services-limits.md)]

### <a name="azure-monitor-limits"></a>Limites de Azure Monitor

#### <a name="alerts"></a>Alertas

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-alerts.md)]

#### <a name="action-groups"></a>Grupos de ação

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-action-groups.md)]

#### <a name="log-queries-and-language"></a>Consultas de log e idioma

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-log-queries.md)]

#### <a name="log-analytics-workspaces"></a>Áreas de trabalho do Log Analytics

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-workspaces.md)]

#### <a name="application-insights"></a>Estatísticas das Aplicações

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-app-insights.md)]




### <a name="notification-hubs-limits"></a>Limites de hubs de notificação

[!INCLUDE [notification-hub-limits](../../../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Limites de hubs de eventos

[!INCLUDE [azure-servicebus-limits](../../../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Limites do barramento de serviço

[!INCLUDE [azure-servicebus-limits](../../../includes/service-bus-quotas-table.md)]

### <a name="iot-central-limits"></a>Limites de IoT Central
[!INCLUDE [iot-central-limits](../../../includes/iot-central-limits.md)]

### <a name="iot-hub-limits"></a>Limites do Hub IoT

[!INCLUDE [azure-iothub-limits](../../../includes/iot-hub-limits.md)]

### <a name="iot-hub-device-provisioning-service-limits"></a>Limites do serviço de provisionamento de dispositivos no Hub IoT

[!INCLUDE [azure-iotdps-limits](../../../includes/iot-dps-limits.md)]

### <a name="data-factory-limits"></a>Limites de Data Factory

[!INCLUDE [azure-data-factory-limits](../../../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Limites de Data Lake Analytics

[!INCLUDE [azure-data-lake-analytics-limits](../../../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Limites de Data Lake Store

[!INCLUDE [azure-data-lake-store-limits](../../../includes/azure-data-lake-store-limits.md)]

### <a name="database-migration-service-limits"></a>Limites do serviço de migração de banco de dados

[!INCLUDE [database-migration-service-limits](../../../includes/database-migration-service-limits.md)]

### <a name="stream-analytics-limits"></a>Limites de Stream Analytics

[!INCLUDE [stream-analytics-limits-table](../../../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Limites de Active Directory

[!INCLUDE [AAD-service-limits](../../../includes/active-directory-service-limits-include.md)]

### <a name="event-grid-limits"></a>Limites de grade de eventos

[!INCLUDE [event-grid-limits](../../../includes/event-grid-limits.md)]

### <a name="azure-maps-limits"></a>Limites do Azure Maps

[!INCLUDE [maps-limits](../../../includes/maps-limits.md)]

### <a name="azure-policy-limits"></a>Limites de Azure Policy

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

### <a name="storsimple-system-limits"></a>Limites do sistema StorSimple

[!INCLUDE [storsimple-limits-table](../../../includes/storsimple-limits-table.md)]

### <a name="backup-limits"></a>Limites de backup

[!INCLUDE [azure-backup-limits](../../../includes/azure-backup-limits.md)]

### <a name="azure-signalr-service-limits"></a>Limites do serviço de Signaler do Azure

[!INCLUDE [signalr-service-limits](../../../includes/signalr-service-limits.md)]

### <a name="site-recovery-limits"></a>Limites do Site Recovery

[!INCLUDE [site-recovery-limits](../../../includes/site-recovery-limits.md)]

### <a name="api-management-limits"></a>Limites de gerenciamento de API

[!INCLUDE [api-management-service-limits](../../../includes/api-management-service-limits.md)]

### <a name="azure-cache-for-redis-limits"></a>Cache do Azure para limites de Redis

[!INCLUDE [redis-cache-service-limits](../../../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Limites de Key Vault

[!INCLUDE [key-vault-limits](../../../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication-limits"></a>Limites da autenticação multifator

[!INCLUDE [azure-mfa-service-limits](../../../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Limites de automação

[!INCLUDE [automation-limits](../../../includes/azure-automation-service-limits.md)]

### <a name="identity-manager-limits"></a>Limites do Identity Manager

[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

### <a name="role-based-access-control-limits"></a>Limites de controle de acesso baseado em função

[!INCLUDE [role-based-access-control-limits](../../../includes/role-based-access-control-limits.md)]

### <a name="sql-database-limits"></a>Limites do banco de dados SQL

Para os limites do banco de dados SQL, consulte [limites de recursos do banco de dados SQL para bancos únicos](../../sql-database/sql-database-vcore-resource-limits-single-databases.md), [limites de recursos do banco de dados SQL para pools elásticos e bancos](../../sql-database/sql-database-vcore-resource-limits-elastic-pools.md)de dados em pool e [limites de recursos do banco de dados SQL para instâncias gerenciadas](../../sql-database/sql-database-managed-instance-resource-limits.md).

### <a name="sql-data-warehouse-limits"></a>Limites de SQL Data Warehouse

Para limites de SQL Data Warehouse, confira [SQL data warehouse limites de recursos](../../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="see-also"></a>Ver também

* [Entender os limites e as aumentos do Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
* [Tamanhos de máquina virtual e serviço de nuvem para o Azure](../../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Tamanhos dos serviços de nuvem do Azure](../../cloud-services/cloud-services-sizes-specs.md)
* [Regras e restrições de nomeação para os recursos do Azure](resource-name-rules.md)

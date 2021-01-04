---
title: Serviços Azure que suportam Zonas de Disponibilidade
description: Para criar aplicações altamente disponíveis e resilientes no Azure, as Zonas de Disponibilidade fornecem locais fisicamente separados que pode utilizar para executar os seus recursos.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 12/17/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: 0365a60317538ba31f39928cd30a57e2c969c832
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/22/2020
ms.locfileid: "97723048"
---
# <a name="azure-services-that-support-availability-zones"></a>Serviços Azure que suportam Zonas de Disponibilidade

As Zonas de Disponibilidade são uma oferta de alta disponibilidade que protege as suas aplicações e dados contra falhas no datacenter. Para a lista das regiões existentes e futuras que suportam Zonas de Disponibilidade, consulte [Regiões e Zonas de Disponibilidade em Azure.](az-overview.md)  

Esta secção lista os serviços Azure que suportam Zonas de Disponibilidade. 

Os serviços disponíveis em cada região, juntamente com o roteiro de disponibilidade que se avizinha, podem ser encontrados em [Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/)

Todos os serviços de gestão da Azure são projetados para serem resistentes a falhas a nível regional. No espectro de falhas, uma ou mais falhas da Zona de Disponibilidade dentro de uma região têm um raio de falha menor em comparação com uma falha da região inteira. O Azure pode recuperar de uma falha ao nível da zona de serviços de gestão dentro de uma região. O Azure realiza uma manutenção crítica uma zona de cada vez dentro de uma região, para evitar falhas que impactem os recursos dos clientes implantados através das Zonas de Disponibilidade dentro de uma região.


![vista conceptual de uma região do Azure com 3 zonas](./media/az-region/azure-region-availability-zones.png)


Os serviços de apoio às Zonas de Disponibilidade enquadram-se em três categorias: **zonal,** **zona redundante** e serviços **não regionais.** As cargas de trabalho dos clientes podem ser categorizadas para utilizar qualquer um destes cenários de arquitetura para atender ao desempenho e durabilidade da aplicação.

- **Serviços zonais** – Um recurso pode ser implantado numa zona de disponibilidade específica e auto-seleccionada para alcançar requisitos mais rigorosos de latência ou desempenho.  A resiliência é auto-arquiteada replicando aplicações e dados para uma ou mais zonas da região.  Os recursos podem ser fixados a uma zona específica. Por exemplo, máquinas virtuais, discos geridos ou endereços IP padrão podem ser fixados a uma zona específica, o que permite uma maior resiliência por ter um ou mais casos de recursos espalhados por zonas.

- **Serviços redundantes de zona** – A plataforma Azure replica o recurso e os dados em todas as zonas.  A Microsoft gere a entrega de alta disponibilidade uma vez que o Azure replica e distribui automaticamente casos dentro da região.  O ZRS, por exemplo, replica os dados em três zonas para que uma falha de zona não tenha impacto no HA dos dados. 

- **Serviços não regionais** – Serviços que não têm dependência de uma região específica do Azure, tornando-os resilientes a paragens em toda a zona, bem como paragens em toda a região.


Para alcançar uma ampla continuidade de negócios em Azure, construa a sua arquitetura de aplicações utilizando a combinação de Zonas de Disponibilidade com pares da região de Azure. Pode replicar sincronizadamente as suas aplicações e dados utilizando Zonas de Disponibilidade dentro de uma região de Azure para uma alta disponibilidade e replicação assíncrona em todas as regiões de Azure para proteção de recuperação de desastres. Para saber mais, leia [soluções de construção para uma elevada disponibilidade utilizando Zonas de Disponibilidade.](https://docs.microsoft.com/azure/architecture/high-availability/building-solutions-for-high-availability) 


### <a name="azure-services-supporting-availability-zones"></a>Serviços Azure que apoiam zonas de disponibilidade

- As máquinas virtuais de geração mais velha não estão listadas abaixo. Para obter mais informações, consulte [gerações anteriores de tamanhos de máquinas virtuais.](../virtual-machines/sizes-previous-gen.md)

- Alguns serviços não são regionais, ver [Regiões e Zonas de Disponibilidade em Azure](az-overview.md) para mais informações. Estes serviços não têm dependência de uma região específica de Azure, tornando-os resistentes a interrupções em toda a zona e paragens em toda a região.  A lista de serviços não regionais pode ser consultada em [Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/)



## <a name="americas"></a>Américas

| **Produtos** | **E.U.A. Central** | **E.U.A. Leste** | **E.U.A. Leste 2** | **E.U.A. Oeste 2** | **Canadá Central** |
|--|--|--|--|--|--|
| **Computação** |  |  |  |  |  |
| [Ambientes de Serviço de Aplicações (ILB)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Conjuntos de escala de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Máquinas Virtuais](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 
| **Contentores** |  |  |  |
| [Azure Kubernetes Service (AKS)](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Container Registry](../container-registry/zone-redundancy.md) |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  | 
| **Armazenamento** |  |  |  |  |  |
| [Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?tabs=azure-portal) (Armazenamento do Azure Data Lake Gen2)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Armazenamento de ficheiros premium](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Armazenamento blob](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Managed Disks](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Rede** |  |  |  |  |  |
| [Gateway de aplicação V2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Rota Azure Express](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Firewall](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Endereço IP padrão](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Balanceador de Carga](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Rede Virtual](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Rede Virtual NAT](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [WAN Virtual](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Gateway de VPN](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Bases de dados** |  |  |  |  |  |
| [Cache do Azure para Redis](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [BD do Cosmos para o Azure](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Base de Dados Azure para MySQL - Servidor Flexível](../mysql/flexible-server/concepts-high-availability.md) | :x: | :x: | :heavy_check_mark: | :heavy_check_mark: | :x: |
| [Base de Dados Azure para PostgreSQL - Servidor Flexível](../postgresql/flexible-server/overview.md) | :x: | :x: | :heavy_check_mark: | :heavy_check_mark: | :x: |
| [Base de Dados Azure SQL (Nível de Finalidade Geral)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | :x: | :heavy_check_mark:(Pré-visualização) | :heavy_check_mark:(Pré-visualização) | :heavy_check_mark:(Pré-visualização) | :x: |
| [Base de Dados Azure SQL (Premium & Business Critical Tiers)](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Análise** |  |  |  |  |  |
| [Hubs de Eventos](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integração** |  |  |  |  |  |
| [Event Grid](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Bus](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Gestão e Governação** |  |  |  |  |  |
| [Observador de Rede](../network-watcher/frequently-asked-questions.md) | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: |
| **Segurança** |  |  |  |  |  |
| [Azure Active Directory Domain Services](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="europe"></a>Europa

| **Produtos** | **França Central** | **Europa do Norte** | **Sul do Reino Unido** | **Europa Ocidental** |
|--|--|--|--|--|
| **Computação** |  |  |  |  |
| [Ambientes de Serviço de Aplicações (ILB)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Kubernetes Service (AKS)](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Conjuntos de escala de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Máquinas Virtuais](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Armazenamento** |  |  |  |  |
| [Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?tabs=azure-portal) (Armazenamento do Azure Data Lake Gen2)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Armazenamento de ficheiros premium](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Armazenamento blob](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Managed Disks](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Rede** |  |  |  |  |
| [Gateway de aplicação V2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Rota Azure Express](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Firewall](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Endereço IP padrão](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Balanceador de Carga](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Rede Virtual](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Rede Virtual NAT](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [WAN Virtual](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Gateway de VPN](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Bases de dados** |  |  |  |  |
| [Cache do Azure para Redis](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [BD do Cosmos para o Azure](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Base de Dados Azure para MySQL - Servidor Flexível](../mysql/flexible-server/concepts-high-availability.md) | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Base de Dados Azure para PostgreSQL - Servidor Flexível](../postgresql/flexible-server/overview.md) | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Base de Dados Azure SQL (Nível de Finalidade Geral)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | :x: | :heavy_check_mark:(Pré-visualização) | :x: | :heavy_check_mark:(Pré-visualização) |
| [Base de Dados Azure SQL (Premium & Business Critical Tiers)](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Análise** |  |  |  |  |
| [Hubs de Eventos](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integração** |  |  |  |  |
| [Event Grid](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Bus](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Gestão e Governação** |  |  |  |  |
| [Observador de Rede](../network-watcher/frequently-asked-questions.md) | :heavy_check_mark: | :heavy_check_mark: | :x: | :heavy_check_mark: |
| **Segurança** |  |  |  |  |
| [Azure Active Directory Domain Services](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="asia-pacific"></a>Ásia-Pacífico



| **Produtos** | **Leste do Japão** | **Sudeste Asiático** | **Leste da Austrália** |
|--|--|--|--|
| **Computação** |  |  |  |
| [Ambientes de Serviço de Aplicações (ILB)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Kubernetes Service (AKS)](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Conjuntos de escala de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Máquinas Virtuais](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Armazenamento** |  |  |  |
| [Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?tabs=azure-portal) (Armazenamento do Azure Data Lake Gen2)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Armazenamento de ficheiros premium](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: | :heavy_check_mark: |
| [Armazenamento blob](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Managed Disks](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Rede** |  |  |  |
| [Gateway de aplicação V2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Rota Azure Express](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Firewall](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Endereço IP padrão](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Balanceador de Carga](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Rede Virtual](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Rede Virtual NAT](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [WAN Virtual](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Gateway de VPN](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Bases de dados** |  |  |  |
| [Cache do Azure para Redis](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [BD do Cosmos para o Azure](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Base de Dados Azure para MySQL - Servidor Flexível](../mysql/flexible-server/concepts-high-availability.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Base de Dados Azure para PostgreSQL - Servidor Flexível](../postgresql/flexible-server/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Base de Dados Azure SQL (Nível de Finalidade Geral)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | :x: | :heavy_check_mark:(Pré-visualização) | :heavy_check_mark:(Pré-visualização) |
| [Base de Dados Azure SQL (Premium & Business Critical Tiers)](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Análise** |  |  |  |
| [Hubs de Eventos](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integração** |  |  |  |
| [Event Grid](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Bus](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Gestão e Governação** |  |  |  |
| [Observador de Rede](../network-watcher/frequently-asked-questions.md) | :heavy_check_mark: | :x: | :x: |
| **Segurança** |  |  |  |
| [Azure Active Directory Domain Services](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: |  |


## <a name="upcoming-availability-zones"></a>Próximas Zonas de Disponibilidade 

A Azure oferece apoio às Zonas de Disponibilidade nas seguintes regiões:
- US Gov - Virginia
- Norte da África do Sul
- E.U.A. Centro-Sul
- Alemanha Centro-Oeste

A lista das regiões existentes e futuras que suportam Zonas de Disponibilidade pode ser consultada [aqui.](https://azure.microsoft.com/global-infrastructure/geographies/)    

Para saber mais sobre o suporte de Zonas de Disponibilidade nestas regiões, contacte o seu representante de vendas da Microsoft ou do cliente.


## <a name="pricing-for-vms-in-availability-zones"></a>Preços para VMs em Zonas de Disponibilidade

Não há custo adicional para máquinas virtuais implantadas numa Zona de Disponibilidade. Para mais informações, reveja [a página de preços da largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="get-started-with-availability-zones"></a>Começar com Zonas de Disponibilidade

- [Criar uma máquina virtual](../virtual-machines/windows/create-portal-availability-zone.md)
- [Adicione um disco gerido usando PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Criar um conjunto de escala de máquina virtual redundante de zona](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Balançar VMs em zonas usando um Balanceador de Carga Padrão com uma linha frontal redundante de zona](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-cli?tabs=option-1-create-load-balancer-standard)
- [Balançar VMs de equilíbrio dentro de uma zona usando um Balanceador de Carga Padrão com um frontend zonal](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-cli?tabs=option-1-create-load-balancer-standard)
- [Armazenamento com redundância entre zonas](../storage/common/storage-redundancy.md)
- [SqL Database nível de propósito geral](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Recuperação após desastre geográfica dos Hubs de Eventos](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Recuperação após desastre geográfica do Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Criar um gateway de rede virtual com redundância entre zonas](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Adicionar zona de região redundante para Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Começar cache Azure para zonas de disponibilidade de redis](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Criar uma instância do Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)
- [Criar um cluster de serviço Azure Kubernetes (AKS) que utiliza Zonas de Disponibilidade](../aks/availability-zones.md)


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Regiões e Zonas de Disponibilidade no Azure](az-overview.md)

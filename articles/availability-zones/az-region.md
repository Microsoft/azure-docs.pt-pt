---
title: Serviços do Azure que suportam as Zonas de Disponibilidade
description: Para criar aplicações altamente disponíveis e resilientes no Azure, as Zonas de Disponibilidade fornecem locais fisicamente separados que pode utilizar para executar os seus recursos.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: b59062395c0b05f36dd0f5bd00fbd1acb3ccb2a9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529803"
---
# <a name="azure-services-that-support-availability-zones"></a>Serviços do Azure que suportam as Zonas de Disponibilidade

A infraestrutura global da Microsoft Azure é projetada e construída em todas as camadas para entregar os mais altos níveis de redundância e resiliência aos seus clientes. A infraestrutura Azure é composta por geografias, regiões e Zonas de Disponibilidade, que limitam o raio de explosão de uma falha e, portanto, limitam o impacto potencial nas aplicações e dados do cliente. A construção Azure Availability Zones foi desenvolvida para fornecer uma solução de software e networking para proteger contra falhas do datacenter e fornecer uma maior disponibilidade (HA) aos nossos clientes.

As Zonas de Disponibilidade são localizações físicas exclusivas numa região do Azure. Cada zona é composta por um ou mais datacenters com potência, arrefecimento e networking independentes. A separação física das Zonas de Disponibilidade dentro de uma região limita o impacto para aplicações e dados de falhas de zona, tais como inundações em larga escala, grandes tempestades e supertempestades, e outros eventos que poderiam perturbar o acesso ao local, passagem segura, aumento de serviços públicos alargados e disponibilidade de recursos. As Zonas de Disponibilidade e os seus centros de dados associados são projetados de forma a que, se uma zona estiver comprometida, os serviços, capacidade e disponibilidade sejam suportados pelas outras Zonas de Disponibilidade da região.

Todos os serviços de gestão da Azure são projetados para serem resistentes a falhas a nível regional. No espectro de falhas, uma ou mais falhas da Zona de Disponibilidade dentro de uma região têm um raio de falha menor em comparação com uma falha da região inteira. O Azure pode recuperar de uma falha ao nível da zona de serviços de gestão dentro de uma região. O Azure realiza uma manutenção crítica uma zona de cada vez dentro de uma região, para evitar falhas que impactem os recursos dos clientes implantados através das Zonas de Disponibilidade dentro de uma região.


![vista conceptual de uma região do Azure com 3 zonas](./media/az-region/azure-region-availability-zones.png)


Os serviços de apoio às Zonas de Disponibilidade enquadram-se em três categorias: **zonal,** **zona redundante** e serviços **não regionais.** As cargas de trabalho dos clientes podem ser categorizadas para utilizar qualquer um destes cenários de arquitetura para atender ao desempenho e durabilidade da aplicação.

- **Serviços zonais** – Um recurso pode ser implantado numa zona de disponibilidade específica e auto-seleccionada para alcançar requisitos mais rigorosos de latência ou desempenho.  A resiliência é auto-arquiteada replicando aplicações e dados para uma ou mais zonas da região.  Os recursos podem ser fixados a uma zona específica. Por exemplo, máquinas virtuais, discos geridos ou endereços IP padrão podem ser fixados a uma zona específica, o que permite uma maior resiliência por ter um ou mais casos de recursos espalhados por zonas.

- **Serviços redundantes de zona** – A plataforma Azure replica o recurso e os dados em todas as zonas.  A Microsoft gere a entrega de alta disponibilidade uma vez que o Azure replica e distribui automaticamente casos dentro da região.  O ZRS, por exemplo, replica os dados em três zonas para que uma falha de zona não tenha impacto no HA dos dados. 

- **Serviços não regionais** – Os serviços estão sempre disponíveis a partir de geografias Azure e são resistentes a paragens em toda a zona, bem como paragens em toda a região. 


Para alcançar uma ampla continuidade de negócios em Azure, construa a sua arquitetura de aplicações utilizando a combinação de Zonas de Disponibilidade com pares da região de Azure. Pode replicar sincronizadamente as suas aplicações e dados utilizando Zonas de Disponibilidade dentro de uma região de Azure para uma alta disponibilidade e replicação assíncrona em todas as regiões de Azure para proteção de recuperação de desastres. Para saber mais, leia [soluções de construção para uma elevada disponibilidade utilizando Zonas de Disponibilidade.](/azure/architecture/high-availability/building-solutions-for-high-availability) 

## <a name="azure-services-supporting-availability-zones"></a>Serviços Azure de apoio a Zonas de Disponibilidade

 - As máquinas virtuais de geração mais velha não estão listadas. Para obter mais informações, consulte [gerações anteriores de tamanhos de máquinas virtuais.](../virtual-machines/sizes-previous-gen.md)
 - Como referido nas [Regiões e Zonas de Disponibilidade em Azure, alguns](az-overview.md)serviços não são regionais. Estes serviços não têm dependência de uma região específica de Azure, como tal são resistentes a interrupções em toda a zona, bem como a interrupções em toda a região.  A lista de serviços não regionais pode ser consultada em [Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/)


## <a name="azure-regions-with-availability-zones"></a>Regiões azure com Zonas de Disponibilidade
 

| América           | Europa               | África              | Ásia-Pacífico   |
|--------------------|----------------------|---------------------|----------------|
|                    |                      |                     |                |
| Sul do Brasil       | França Central       | África do Sul Norte* | Leste da Austrália |
| Canadá Central     | Alemanha Centro-Oeste |                     | Centro da Índia* |
| E.U.A. Central         | Europa do Norte         |                     | Leste do Japão     |
| E.U.A. Leste            | Sul do Reino Unido             |                     | Coreia Central* |
| E.U.A. Leste 2          | Europa Ocidental          |                     | Sudeste Asiático |
| Centro-Sul dos EUA |                      |                     |                |
| US Gov - Virginia    |                      |                     |                |
| Oeste DOS EUA 2        |                      |                     |                |
| Oeste DOS EUA 3*       |                      |                     |                |

\* Para saber mais sobre As Zonas de Disponibilidade e o suporte de serviços disponíveis nestas regiões, contacte o seu representante de vendas da Microsoft ou do cliente. Para as próximas regiões que apoiarão As Zonas de Disponibilidade, consulte [geografias Azure.](https://azure.microsoft.com/en-us/global-infrastructure/geographies/)


## <a name="azure-services-supporting-availability-zones"></a>Serviços Azure que apoiam zonas de disponibilidade

- As máquinas virtuais de geração mais velha não estão listadas abaixo. Para obter mais informações, consulte [gerações anteriores de tamanhos de máquinas virtuais.](../virtual-machines/sizes-previous-gen.md)

- Alguns serviços não são regionais, ver [Regiões e Zonas de Disponibilidade em Azure](az-overview.md) para mais informações. Estes serviços não têm dependência de uma região específica de Azure, tornando-os resistentes a interrupções em toda a zona e paragens em toda a região.  A lista de serviços não regionais pode ser consultada em [Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/)


### <a name="zone-resilient-services"></a>Serviços Resilientes de Zona 

:globe_with_meridians: Serviços Não Regionais - Os serviços estão sempre disponíveis a partir de geografias Azure e são resistentes a paragens em toda a zona, bem como paragens em toda a região.

:large_blue_diamond: Resiliente às paragens em toda a zona 

**Serviços Fundacionais**

|     Produtos                                                    | Resiliência             |
|-----------------------------------------------------------------|:----------------------------:|
|     [Gateway de aplicação (V2)](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)                                  | :large_blue_diamond:  |
|     [Azure Backup](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy)                                                | :large_blue_diamond:  |
|     [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability#availability-zone-support)                                           | :large_blue_diamond:  |
|     [Azure Data Lake Storage Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)                             | :large_blue_diamond:  |
|     [Rota Azure Express](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute)                                       | :large_blue_diamond:  |
|     [IP Público de Azure](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses)                                           | :large_blue_diamond:  |
|     Base de Dados Azure SQL[(Nível de Finalidade Geral)](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla)                 | :large_blue_diamond:  |
|     Azure SQL Database ([Premium & Business Critical Tier)](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla)     | :large_blue_diamond:  |
|     [Armazenamento de Discos](https://docs.microsoft.com/azure/storage/common/storage-redundancy)                                                | :large_blue_diamond:  |
|     [Hubs de Eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones)                                                  | :large_blue_diamond:  |
|     [Cofre de Chaves](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)                                                   | :large_blue_diamond:  |
|     [Balanceador de Carga](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)                                               | :large_blue_diamond:  |
|     [Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-geo-dr#availability-zones)                                                 | :large_blue_diamond:  |
|     [Tecido de serviço](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications)                                            | :large_blue_diamond:  |
|     [Conta de Armazenamento](https://docs.microsoft.com/azure/storage/common/storage-redundancy)                                           | :large_blue_diamond:  |
|     Armazenamento:   [Níveis de armazenamento de blob quente/fresco](https://docs.microsoft.com/azure/storage/common/storage-redundancy)                      | :large_blue_diamond:  |
|     Armazenamento:   [Discos geridos](https://docs.microsoft.com/azure/virtual-machines/managed-disks-overview)                                    | :large_blue_diamond:  |
|     [Conjuntos de escala de máquinas virtuais](https://docs.microsoft.com/azure/virtual-machine-scale-sets/scripts/cli-sample-zone-redundant-scale-set)                               | :large_blue_diamond:  |
|     [Máquinas Virtuais](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                          | :large_blue_diamond:  |
|     Máquinas Virtuais: [Série Av2](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Máquinas Virtuais: [Série B](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Máquinas Virtuais: [Série DSv2](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                             | :large_blue_diamond:  |
|     Máquinas Virtuais: [Série DSv3](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                            | :large_blue_diamond:  |
|     Máquinas Virtuais: [Série Dv2](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                             | :large_blue_diamond:  |
|     Máquinas Virtuais: [Série Dv3](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Máquinas Virtuais: [Série ESv3](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                             | :large_blue_diamond:  |
|     Máquinas Virtuais: [Série Ev3](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Máquinas Virtuais: [Série F](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                | :large_blue_diamond:  |
|     Máquinas Virtuais: [Série F](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Máquinas Virtuais: [Galeria de Imagens Partilhadas](https://docs.microsoft.com/azure/virtual-machines/shared-image-galleries#make-your-images-highly-available) | :large_blue_diamond:  |
|     [Rede Virtual](https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway)                                         | :large_blue_diamond:  |
|     [Gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways)                                             | :large_blue_diamond:  |


**Serviços mainstream**


|     Produtos                                                    | Resiliência             |
|-----------------------------------------------------------------|:----------------------------:|
|     [Ambientes do App Service](https://docs.microsoft.com/azure/app-service/environment/zone-redundancy)                                    | :large_blue_diamond:  |
|     [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview)                      | :large_blue_diamond:  |
|     [API Management do Azure](https://docs.microsoft.com/azure/api-management/zone-redundancy)                      | :large_blue_diamond:  |
|     [Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview)                                               | :large_blue_diamond:  |
|     [Cache do Azure para Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-high-availability)                              | :large_blue_diamond:  |
|     [Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-performance-optimization#availability-zones)               | :large_blue_diamond:  |
|     Serviços Cognitivos Azure: [Análise de Texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)                    | :large_blue_diamond:  |
|     [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal)                               | :large_blue_diamond:  |
|     Base de Dados Azure para MySQL – [Servidor Flexível](https://docs.microsoft.com/azure/mysql/flexible-server/concepts-high-availability)                  | :large_blue_diamond:  |
|     Base de Dados Azure para PostgreSQL – [Servidor Flexível](https://docs.microsoft.com/azure/postgresql/flexible-server/overview)             | :large_blue_diamond:  |
|     [Azure DDoS Protection](https://docs.microsoft.com/azure/ddos-protection/ddos-faq)                                       | :large_blue_diamond:  |
|     [Azure Disk Encryption](https://docs.microsoft.com/azure/virtual-machines/disks-redundancy)                                       | :large_blue_diamond:  |
|     [Azure Firewall](https://docs.microsoft.com/azure/firewall/deploy-availability-zone-powershell#:~:text=For%20more%20information%20about%20Azure%20Firewall%20Availability%20Zones%2C,This%20creates%20a%20zone-redundant%20IP%20address%20by%20default)                                              | :large_blue_diamond:  |
|     [Azure Firewall Manager](https://docs.microsoft.com/azure/firewall-manager/quick-firewall-policy)                                      | :large_blue_diamond:  |
|     [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/availability-zones)                              | :large_blue_diamond:  |
|     [Azure Private Link](https://docs.microsoft.com/azure/private-link/private-link-overview)                                          | :large_blue_diamond:  |
|     [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery)                                         | :large_blue_diamond:  |
|     Azure SQL: [Máquina Virtual](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla)                                  | :large_blue_diamond:  |
|     [Firewall de Aplicações Web do Azure](https://docs.microsoft.com/azure/firewall/deploy-availability-zone-powershell#:~:text=For%20more%20information%20about%20Azure%20Firewall%20Availability%20Zones%2C,This%20creates%20a%20zone-redundant%20IP%20address%20by%20default)                              | :large_blue_diamond:  |
|     [Container Registry](https://docs.microsoft.com/azure/container-registry/zone-redundancy)                                          | :large_blue_diamond:  |
|     [Event Grid](https://docs.microsoft.com/azure/event-grid/overview)                                                  | :large_blue_diamond:  |
|     [Observador de Rede](https://docs.microsoft.com/azure/network-watcher/frequently-asked-questions#service-availability-and-redundancy)                                             | :large_blue_diamond:  |
|     Observador de Rede: [Análise de Tráfego](https://docs.microsoft.com/azure/network-watcher/frequently-asked-questions#service-availability-and-redundancy)                          | :large_blue_diamond:  |
|     [Power BI Embedded](https://docs.microsoft.com/power-bi/admin/service-admin-failover#what-does-high-availability)                                           | :large_blue_diamond:  |
|     [Armazenamento Premium Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blob-performance-tiers#:~:text=Table%201%20%20%20%20Area%20%20,%20%20Currently%20supports%20only%20locally-redundan%20...%20)                                        | :large_blue_diamond:  |
|     Armazenamento: [Ficheiros Azure Premium](https://docs.microsoft.com/azure/storage/files/storage-files-planning)                                | :large_blue_diamond:  |
|     Máquinas Virtuais: [Azure Anfitrião Dedicado](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                     | :large_blue_diamond:  |
|     Máquinas Virtuais: [Série Ddsv4](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Máquinas Virtuais: [Série Ddv4](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Máquinas Virtuais: [Série Dsv4](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Máquinas Virtuais: [Série Dv4](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                | :large_blue_diamond:  |
|     Máquinas Virtuais: [Série Edsv4](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Máquinas Virtuais: [Série Edv4](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Máquinas Virtuais: [Série Esv4](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Máquinas Virtuais: [Série Ev4](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                | :large_blue_diamond:  |
|     Máquinas Virtuais: [Série Fsv2](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Máquinas Virtuais: [Série M](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                  | :large_blue_diamond:  |
|     [WAN Virtual](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)                                                 | :large_blue_diamond:  |
|     Virtual WAN: [ExpressRoute](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)                                   | :large_blue_diamond:  |
|     WAN Virtual: [Gateway VPN ponto-a-local](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways)                      | :large_blue_diamond:  |
|     VIRTUAL WAN: [Portal VPN site-to-site](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways)                       | :large_blue_diamond:  |


**Serviços Especializados**

|     Produtos                                                    | Resiliência             |
|-----------------------------------------------------------------|:----------------------------:|
|     Azure Red Hat OpenShift                                     | :large_blue_diamond:  |
|     Serviços Cognitivos: Detetor de Anomalias                        | :large_blue_diamond:  |
|     Serviços Cognitivos: Reconhecimento de Formulários                         | :large_blue_diamond:  |
|     Armazenamento: Disco Ultra                                         | :large_blue_diamond:  |


**Não regional**

|     Produtos                                                    | Resiliência             |
|-----------------------------------------------------------------|:----------------------------:|
|     DNS do Azure                                                   | :globe_with_meridians: |
|     Diretório Ativo Azure                                    | :globe_with_meridians: |
|     Proteção Avançada Contra Ameaças do Azure                            | :globe_with_meridians: |
|     Assistente do Azure                                               | :globe_with_meridians: |
|     Azure Blueprints                                            | :globe_with_meridians: |
|     Azure Bot Services                                          | :globe_with_meridians: |
|     Azure Front Door                                            | :globe_with_meridians: |
|     Azure Defender para IoT                                    | :globe_with_meridians: |
|     Azure Front Door                                            | :globe_with_meridians: |
|     Proteção de Informação Azure                              | :globe_with_meridians: |
|     Farol de Azure                                          | :globe_with_meridians: |
|     Aplicações geridas azure                                | :globe_with_meridians: |
|     Azure Maps                                                  | :globe_with_meridians: |
|     Diagnóstico de Desempenho Azure                               | :globe_with_meridians: |
|     Azure Policy                                                | :globe_with_meridians: |
|     Gráfico de recursos Azure                                      | :globe_with_meridians: |
|     Azure Sentinel                                              | :globe_with_meridians: |
|     Azure Stack                                                 | :globe_with_meridians: |
|     Borda da pilha de Azure                                          | :globe_with_meridians: |
|     Cloud Shell                                                 | :globe_with_meridians: |
|     Rede de Entrega de Conteúdos                                    | :globe_with_meridians: |
|     Cost Management                                             | :globe_with_meridians: |
|     Lockbox do cliente para Microsoft Azure                      | :globe_with_meridians: |
|     Intune                                                      | :globe_with_meridians: |
|     Microsoft Azure Peering Service                           | :globe_with_meridians: |
|     Portal Microsoft Azure                                    | :globe_with_meridians: |
|     Microsoft Cloud App Security                                | :globe_with_meridians: |
|     Microsoft Graph                                             | :globe_with_meridians: |
|     Centro de Segurança                                           | :globe_with_meridians: |
|     Gestor de Tráfego                                           | :globe_with_meridians: |


## <a name="pricing-for-vms-in-availability-zones"></a>Preços para VMs em Zonas de Disponibilidade

As Zonas de Disponibilidade Azure estão disponíveis com a sua subscrição Azure. Saiba mais aqui - [Página de preços da largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="get-started-with-availability-zones"></a>Começar com Zonas de Disponibilidade

- [Criar uma máquina virtual](../virtual-machines/windows/create-portal-availability-zone.md)
- [Adicione um disco gerido usando PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Criar um conjunto de escala de máquina virtual redundante de zona](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Balançar VMs em zonas usando um Balanceador de Carga Padrão com uma linha frontal redundante de zona](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Balançar VMs de equilíbrio dentro de uma zona usando um Balanceador de Carga Padrão com um frontend zonal](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Armazenamento com redundância entre zonas](../storage/common/storage-redundancy.md)
- [SqL Database nível de propósito geral](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Recuperação após desastre geográfica dos Hubs de Eventos](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Recuperação após desastre geográfica do Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Criar um gateway de rede virtual com redundância entre zonas](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Adicionar zona de região redundante para Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Começar cache Azure para zonas de disponibilidade de redis](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Criar uma instância do Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md)
- [Criar um cluster de serviço Azure Kubernetes (AKS) que utiliza Zonas de Disponibilidade](../aks/availability-zones.md)


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Regiões e Zonas de Disponibilidade no Azure](az-overview.md)

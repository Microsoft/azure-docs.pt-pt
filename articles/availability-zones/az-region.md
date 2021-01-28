---
title: Serviços do Azure que suportam as Zonas de Disponibilidade
description: Para criar aplicações altamente disponíveis e resilientes no Azure, as Zonas de Disponibilidade fornecem locais fisicamente separados que pode utilizar para executar os seus recursos.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 01/26/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: e8e62d92afb70ec9d4df40f677de05583ef472b0
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955788"
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


| América           | Europa         | Alemanha              | África              | Ásia-Pacífico   |
|--------------------|----------------|----------------------|---------------------|----------------|
|                    |                |                      |                     |                |
| Canadá Central     | França Central | Alemanha Centro-Oeste | África do Sul Norte* | Leste do Japão     |
| E.U.A. Central         | Europa do Norte   |                      |                     | Sudeste Asiático |
| E.U.A. Leste            | Sul do Reino Unido       |                      |                     | Leste da Austrália |
| E.U.A. Leste 2          | Europa Ocidental    |                      |                     |                |
| Centro-Sul dos EUA |                |                      |                     |                |
| Eua Gov Virginia* |                |                      |                     |                |
| Oeste DOS EUA 2        |                |                      |                     |                |


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
|     Conta de Armazenamento                                           | :large_blue_diamond:  |
|     Gateway de aplicação (V2)                                  | :large_blue_diamond:  |
|     Azure Backup                                                | :large_blue_diamond:  |
|     Azure Cosmos DB                                           | :large_blue_diamond:  |
|     Azure Data Lake Storage Gen 2                             | :large_blue_diamond:  |
|     Rota Azure Express                                       | :large_blue_diamond:  |
|     IP Público de Azure                                           | :large_blue_diamond:  |
|     Base de Dados Azure SQL (Nível de Finalidade Geral)                 | :large_blue_diamond:  |
|     Base de Dados Azure SQL (Premium & Business Critical Tier)     | :large_blue_diamond:  |
|     Armazenamento de Discos                                                | :large_blue_diamond:  |
|     Hubs de Eventos                                                  | :large_blue_diamond:  |
|     Key Vault                                                   | :large_blue_diamond:  |
|     Balanceador de Carga                                               | :large_blue_diamond:  |
|     Service Bus                                                 | :large_blue_diamond:  |
|     Tecido de serviço                                            | :large_blue_diamond:  |
|     Armazenamento: Níveis de armazenamento de blob quente/fresco                      | :large_blue_diamond:  |
|     Armazenamento: Discos geridos                                    | :large_blue_diamond:  |
|     Conjuntos de escala de máquinas virtuais                               | :large_blue_diamond:  |
|     Máquinas Virtuais                                          | :large_blue_diamond:  |
|     Máquinas Virtuais: Av2-Series                              | :large_blue_diamond:  |
|     Máquinas Virtuais: Bs-Series                               | :large_blue_diamond:  |
|     Máquinas Virtuais: DSv2-Series                             | :large_blue_diamond:  |
|     Máquinas Virtuais: DSv3-Series                             | :large_blue_diamond:  |
|     Máquinas Virtuais: Dv2-Series                              | :large_blue_diamond:  |
|     Máquinas Virtuais: Dv3-Series                              | :large_blue_diamond:  |
|     Máquinas Virtuais: ESv3-Series                             | :large_blue_diamond:  |
|     Máquinas Virtuais: Ev3-Series                              | :large_blue_diamond:  |
|     Rede Virtual                                           | :large_blue_diamond:  |
|     Gateway de VPN                                                 | :large_blue_diamond:  |


**Serviços mainstream**

| Produtos                                        | Resiliência |
|-------------------------------------------------|:------------:|
| Ambientes do App Service                        |      :large_blue_diamond:  |
| Azure Active Directory Domain Services          |      :large_blue_diamond:  |
| Azure Bastion                                   |      :large_blue_diamond:  |
| Cache do Azure para Redis                           |      :large_blue_diamond:  |
| Serviços Cognitivos do Azure: Análise de Texto        |      :large_blue_diamond:  |
| Azure Data Explorer                             |      :large_blue_diamond:  |
| Base de Dados Azure para MySQL – Servidor Flexível      |      :large_blue_diamond:  |
| Base de Dados Azure para PostgreSQL – Servidor Flexível |      :large_blue_diamond:  |
| Azure DDoS Protection                           |      :large_blue_diamond:  |
| Azure Firewall                                  |      :large_blue_diamond:  |
| Azure Firewall Manager                          |      :large_blue_diamond:  |
| Azure Kubernetes Service (AKS)                  |      :large_blue_diamond:  |
| Azure Private Link                              |      :large_blue_diamond:  |
| Azure Red Hat OpenShift                         |      :large_blue_diamond:  |
| Azure Site Recovery                             |      :large_blue_diamond:  |
| Container Registry                              |      :large_blue_diamond:  |
| Event Grid                                      |      :large_blue_diamond:  |
| Observador de Rede                                 |      :large_blue_diamond:  |
| Power BI Embedded                               |      :large_blue_diamond:  |
| Armazenamento Premium Blob                            |      :large_blue_diamond:  |
| Máquinas Virtuais: Ddsv4-Series                  |      :large_blue_diamond:  |
| Máquinas Virtuais: Ddv4-Series                   |      :large_blue_diamond:  |
| Máquinas Virtuais: Dsv4-Series                   |      :large_blue_diamond:  |
| Máquinas Virtuais: Dv4-Series                    |      :large_blue_diamond:  |
| Máquinas Virtuais: Edsv4-Series                  |      :large_blue_diamond:  |
| Máquinas Virtuais: Edv4-Series                   |      :large_blue_diamond:  |
| Máquinas Virtuais: Esv4-Series                   |      :large_blue_diamond:  |
| Máquinas Virtuais: Ev4-Series                    |      :large_blue_diamond:  |
| Máquinas Virtuais: Fsv2-Series                   |      :large_blue_diamond:  |
| Máquinas Virtuais: Série M                      |      :large_blue_diamond:  |
| WAN Virtual                                     |      :large_blue_diamond:  |


**Não regional**

|     Produtos                                  |     Resiliência    |
|-----------------------------------------------|:-------------------:|
|     DNS do Azure                                 |     :globe_with_meridians:             |
|     Diretório Ativo Azure                  |     :globe_with_meridians:             |
|     Assistente do Azure                             |     :globe_with_meridians:             |
|     Azure Bot Services                        |     :globe_with_meridians:             |
|     Azure Defender para IoT                  |     :globe_with_meridians:             |
|     Proteção de Informação Azure            |     :globe_with_meridians:             |
|     Farol de Azure                        |     :globe_with_meridians:             |
|     Aplicações geridas azure              |     :globe_with_meridians:             |
|     Azure Maps                                |     :globe_with_meridians:             |
|     Azure Policy                              |     :globe_with_meridians:             |
|     Gráfico de recursos Azure                    |     :globe_with_meridians:             |
|     Azure Stack                               |     :globe_with_meridians:             |
|     Borda da pilha de Azure                        |     :globe_with_meridians:             |
|     Cloud Shell                               |     :globe_with_meridians:             |
|     Lockbox do cliente para Microsoft Azure    |     :globe_with_meridians:             |
|     Microsoft Azure Peering Service         |     :globe_with_meridians:             |
|     Portal Microsoft Azure                  |     :globe_with_meridians:             |
|     Centro de Segurança                         |     :globe_with_meridians:             |
|     Gestor de Tráfego                         |     :globe_with_meridians:             |


## <a name="pricing-for-vms-in-availability-zones"></a>Preços para VMs em Zonas de Disponibilidade

Não há custo adicional para máquinas virtuais implantadas numa Zona de Disponibilidade. Para mais informações, reveja [a página de preços da largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).


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

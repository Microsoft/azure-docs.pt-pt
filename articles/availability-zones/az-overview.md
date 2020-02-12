---
title: O que são Zonas de Disponibilidade do Azure?
description: Para criar aplicativos altamente disponíveis e resilientes no Azure, Zonas de Disponibilidade fornecer locais fisicamente separados que você pode usar para executar seus recursos.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 10/17/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 4b1af742182d9c953846e723b6d48809818565b6
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271289"
---
# <a name="what-are-availability-zones-in-azure"></a>O que são Zonas de Disponibilidade no Azure?
Zonas de Disponibilidade é uma oferta de alta disponibilidade que protege seus aplicativos e dados de falhas do datacenter. As Zonas de Disponibilidade são localizações físicas exclusivas numa região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas. A separação física de Zonas de Disponibilidade dentro de uma região protege aplicativos e dados de falhas do datacenter. Os serviços com redundância de zona replicam seus aplicativos e dados em Zonas de Disponibilidade para proteger contra pontos únicos de falha. Com o Zonas de Disponibilidade, o Azure oferece um SLA de tempo de atividade de VM melhor do setor 99,99%. O [SLA do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) completo explica a disponibilidade garantida do Azure em termos globais.

Uma zona de disponibilidade em uma região do Azure é uma combinação de um domínio de falha e um domínio de atualização. Por exemplo, se você criar três ou mais VMs em três zonas em uma região do Azure, suas VMs serão efetivamente distribuídas entre três domínios de falha e três domínios de atualização. A plataforma Azure reconhece essa distribuição entre domínios de atualização para garantir que as VMs em diferentes zonas não sejam atualizadas ao mesmo tempo.

Crie alta disponibilidade em sua arquitetura de aplicativos Colocalizando seus recursos de computação, armazenamento, rede e dados em uma zona e replicando em outras zonas. Os serviços do Azure que suportam as Zonas de Disponibilidade estão divididos em duas categorias:

- **Serviços zonais** – você fixa o recurso em uma zona específica (por exemplo, máquinas virtuais, discos gerenciados, endereços IP padrão) ou
- **Serviços com redundância entre zonas** – a plataforma é replicada automaticamente nas zonas (por exemplo, armazenamento com redundância entre zonas, Base de Dados SQL).

Para obter uma continuidade de negócios abrangente no Azure, crie sua arquitetura de aplicativo usando a combinação de Zonas de Disponibilidade com pares de regiões do Azure. Você pode replicar de forma síncrona seus aplicativos e dados usando Zonas de Disponibilidade em uma região do Azure para alta disponibilidade e replicação assíncrona em regiões do Azure para proteção de recuperação de desastres.
 
![exibição conceitual de uma zona ficando inativa em uma região](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> Os identificadores de zona de disponibilidade (os números 1, 2 e 3 na imagem acima) são mapeados logicamente para as zonas físicas reais para cada assinatura de forma independente. Isso significa que a disponibilidade Zona 1 em uma determinada assinatura pode se referir a uma zona física diferente da Zona 1 de disponibilidade em uma assinatura diferente. Como consequência, é recomendável não confiar nas IDs de zona de disponibilidade em assinaturas diferentes para o posicionamento da máquina virtual.

## <a name="services-support-by-region"></a>Suporte a serviços por região

As combinações de serviços e regiões do Azure que oferecem suporte a Zonas de Disponibilidade são:


|                                 |Américas |              |           |           | Europa |              |          |              | Ásia-Pacífico |                 |
|----------------------------|----------|----------|---------|---------|--------------|------------|--------|----------|----------|-------------|
|          |E.U.A. Central|E.U.A. Leste|E.U.A. Leste 2|E.U.A. Oeste 2|França Central|Europa do Norte|Sul do Reino Unido|Europa Ocidental|Leste do Japão|Ásia Sudeste|
| **Computação**                         |            |              |           |           |                |              |          |             |            |                |
| Máquinas Virtuais do Linux          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Máquinas Virtuais do Windows        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Conjuntos de Dimensionamento de Máquinas Virtuais      | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Serviço Kubernetes do Azure        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Armazenamento**   |            |              |           |           |                |              |          |             |            |                |
| Managed Disks                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Armazenamento com redundância de zona          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Redes**                     |            |              |           |           |                |              |          |             |            |                |
| Endereço IP padrão        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Balanceador de Carga Standard     | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Gateway de VPN            | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| Gateway do ExpressRoute   | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| Gateway de aplicativo (v2)    | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| Azure Firewall           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |
| **Bases de dados**                     |            |              |           |           |                |              |          |             |            |                |
| Explorador de Dados do Azure                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;        | &#10003;       |
| SQL Database                    | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;Apresentação      | &#10003;       |
| Cache do Azure para Redis           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |
| Azure Cosmos DB                    | &#10003;   |  &#10003;  |  &#10003; | &#10003; |       |     | &#10003; |  &#10003;   |            | &#10003;       |
| **Análise**                       |            |              |           |           |                |              |          |             |            |                |
| Event Hubs                      | &#10003;   |   &#10003; | &#10003;  | &#10003;  | &#10003; | &#10003; | &#10003; | &#10003; | &#10003; | &#10003;       |
| **Integração**                     |            |              |           |           |                |              |          |             |            |                |
| Barramento de serviço (somente camada Premium) | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |
| Event Grid | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |
| **Identidade**                     |            |              |           |           |                |              |          |             |            |                |
| Serviços de Domínio do Azure AD | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |

## <a name="services-resiliency"></a>Resiliência de serviços
Todos os serviços de gerenciamento do Azure são arquitetados para serem resilientes de falhas no nível de região. No espectro de falhas, uma ou mais falhas de zona de disponibilidade em uma região têm um raio de falha menor em comparação a uma falha de região inteira. O Azure pode se recuperar de uma falha no nível de zona dos serviços de gerenciamento dentro da região ou de outra região do Azure. O Azure realiza uma manutenção crítica em uma zona por vez dentro de uma região, para evitar falhas que afetem os recursos do cliente implantados em Zonas de Disponibilidade dentro de uma região.

## <a name="pricing"></a>Preços
Não há nenhum custo adicional para máquinas virtuais implantadas em uma zona de disponibilidade. 99,99% de SLA de tempo de atividade de VM é oferecido quando duas ou mais VMs são implantadas em duas ou mais Zonas de Disponibilidade em uma região do Azure. Haverá encargos adicionais de transferência de dados da VM para a VM na zona de disponibilidade. Para obter mais informações, consulte a página de [preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/) .


## <a name="get-started-with-availability-zones"></a>Introdução ao Zonas de Disponibilidade
- [Criar uma máquina virtual](../virtual-machines/windows/create-portal-availability-zone.md)
- [Adicionar um disco gerenciado usando o PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Criar um conjunto de dimensionamento de máquinas virtuais com redundância de zona](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [VMs de balanceamento de carga entre zonas usando um Standard Load Balancer com um front-end com redundância de zona](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [VMs de balanceamento de carga em uma zona usando um Standard Load Balancer com um front-end zonal](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Armazenamento com redundância entre zonas](../storage/common/storage-redundancy-zrs.md)
- [Base de Dados SQL](../sql-database/sql-database-high-availability.md#zone-redundant-configuration)
- [Recuperação após desastre geográfica dos Hubs de Eventos](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Recuperação após desastre geográfica do Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Criar um gateway de rede virtual com redundância entre zonas](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Adicionar região com redundância de zona para Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Introdução o cache do Azure para Redis Zonas de Disponibilidade](https://aka.ms/redis/az/getstarted)
- [Criar uma instância de Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md)
- [Criar um cluster AKS (serviço de kubernetes do Azure) que usa Zonas de Disponibilidade](../aks/availability-zones.md)

## <a name="next-steps"></a>Passos seguintes
- [Modelos de início rápido](https://aka.ms/azqs)

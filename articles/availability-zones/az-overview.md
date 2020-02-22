---
title: O que são As Zonas de Disponibilidade do Azure?
description: Para criar aplicações altamente disponíveis e resilientes em Azure, as Zonas de Disponibilidade fornecem locais fisicamente separados que pode usar para executar os seus recursos.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 10/17/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: c9f4a418ac05b2618b4641c857e182e73c35d34c
ms.sourcegitcommit: 78f367310e243380b591ff10f2500feca93f5d0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77544254"
---
# <a name="what-are-availability-zones-in-azure"></a>O que são Zonas de Disponibilidade em Azure?
As Zonas de Disponibilidade são uma oferta de alta disponibilidade que protege as suas aplicações e dados de falhas no datacenter. As Zonas de Disponibilidade são localizações físicas exclusivas numa região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas. A separação física das Zonas de Disponibilidade dentro de uma região protege aplicações e dados de falhas no datacenter. Os serviços redundantes em zonas replicam as suas aplicações e dados através das Zonas de Disponibilidade para proteger contra pontos únicos de falha. Com Zonas de Disponibilidade, o Azure oferece à indústria o melhor tempo de uptime De SLA de 99,99% vM. O [SLA do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) completo explica a disponibilidade garantida do Azure em termos globais.

Uma Zona de Disponibilidade numa região do Azure é uma combinação de um domínio de falha e um domínio de atualização. Por exemplo, se criar três ou mais VMs em três zonas de uma região do Azure, os seus VMs são efetivamente distribuídos por três domínios de falha e três domínios de atualização. A plataforma Azure reconhece esta distribuição através de domínios de atualização para garantir que os VMs em diferentes zonas não são atualizados ao mesmo tempo.

Construa alta disponibilidade na sua arquitetura de aplicação co-localizando a sua computação, armazenamento, networking e recursos de dados dentro de uma zona e replicando-se noutras zonas. Os serviços Azure que suportam zonas de disponibilidade enquadram-se em duas categorias:

- **Serviços zonais** – você prende o recurso a uma zona específica (por exemplo, máquinas virtuais, discos geridos, endereços IP standard), ou
- **Serviços de redução de zonas** – a plataforma replica-se automaticamente em zonas (por exemplo, armazenamento redundante em zona, Base de Dados SQL).

Para alcançar uma continuidade abrangente de negócios no Azure, construa a sua arquitetura de aplicação utilizando a combinação de Zonas de Disponibilidade com pares da região de Azure. Pode replicar sincronizadamente as suas aplicações e dados utilizando zonas de disponibilidade dentro de uma região de Azure para alta disponibilidade e replicação assincronicamente em todas as regiões de Azure para proteção de recuperação de desastres.
 
![visão conceptual de uma zona descendo em uma região](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> Os identificadores da Zona de Disponibilidade (os números 1, 2 e 3 na imagem acima) estão logicamente mapeados para as zonas físicas reais para cada subscrição de forma independente. Isto significa que a Zona de Disponibilidade 1 numa determinada subscrição pode referir-se a uma zona física diferente da Zona de Disponibilidade 1 numa subscrição diferente. Como consequência, recomenda-se não confiar em IDs da Zona de Disponibilidade em diferentes subscrições para colocação de máquinas virtuais.

## <a name="services-support-by-region"></a>Apoio de serviços por região

As combinações de serviços Azure e regiões que suportam zonas de disponibilidade são:


|                                 |Américas |              |           |           | Europa |              |          |              | Ásia-Pacífico |                 |
|----------------------------|----------|----------|---------|---------|--------------|------------|--------|----------|----------|-------------|
|          |E.U.A. Central|E.U.A. Leste|E.U.A. Leste 2|E.U.A.Oeste 2|França Central|Europa do Norte|Sul do Reino Unido|Europa Ocidental|Leste do Japão|Ásia Sudeste|
| **Computação**                         |            |              |           |           |                |              |          |             |            |                |
| Máquinas Virtuais do Linux          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Máquinas Virtuais do Windows        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Conjuntos de Dimensionamento de Máquinas Virtuais      | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Ambientes de Serviço de Aplicações Azure ILB | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Azure Kubernetes Service        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Armazenamento**   |            |              |           |           |                |              |          |             |            |                |
| Managed Disks                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Armazenamento redundante de zona          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Redes**                     |            |              |           |           |                |              |          |             |            |                |
| Endereço IP padrão        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Balanceador de Carga Padrão     | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Gateway de VPN            | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| ExpressRoute Gateway   | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| Gateway de aplicação (V2)    | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| Azure Firewall           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |
| **Bases de dados**                     |            |              |           |           |                |              |          |             |            |                |
| Azure Data Explorer                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;        | &#10003;       |
| SQL Database                    | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;(Pré-visualização)      | &#10003;       |
| Cache do Azure para Redis           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |
| Azure Cosmos DB                    | &#10003;   |  &#10003;  |  &#10003; | &#10003; |       |     | &#10003; |  &#10003;   |            | &#10003;       |
| **Análise**                       |            |              |           |           |                |              |          |             |            |                |
| Hubs de Eventos                      | &#10003;   |   &#10003; | &#10003;  | &#10003;  | &#10003; | &#10003; | &#10003; | &#10003; | &#10003; | &#10003;       |
| **Integração**                     |            |              |           |           |                |              |          |             |            |                |
| Ônibus de serviço (apenas premium tier) | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |
| Event Grid | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |
| **Identidade**                     |            |              |           |           |                |              |          |             |            |                |
| Azure AD Domain Services | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |

## <a name="services-resiliency"></a>Resiliência dos serviços
Todos os serviços de gestão azure são arquitetos para serem resilientes a falhas a nível regional. No espectro de falhas, uma ou mais falhas da Zona de Disponibilidade numa região têm um raio de avaria menor em comparação com uma falha total da região. O Azure pode recuperar de uma falha de serviços de gestão ao nível da zona dentro da região ou de outra região do Azure. O Azure realiza uma zona de manutenção crítica numa zona de cada vez dentro de uma região, para evitar falhas que afetem os recursos dos clientes implantados em zonas de disponibilidade dentro de uma região.

## <a name="pricing"></a>Preços
Não existe um custo adicional para máquinas virtuais implantadas numa Zona de Disponibilidade. 99,99% VM uptime SLA é oferecido quando dois ou mais VMs são implantados em duas ou mais Zonas de Disponibilidade dentro de uma região de Azure. Haverá taxas adicionais de transferência de dados VM-to-VM da Zona Inter-Disponibilidade. Para mais informações, reveja a página de [preços da Largura de Banda.](https://azure.microsoft.com/pricing/details/bandwidth/)


## <a name="get-started-with-availability-zones"></a>Começar com Zonas de Disponibilidade
- [Criar uma máquina virtual](../virtual-machines/windows/create-portal-availability-zone.md)
- [Adicione um disco gerido utilizando powerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Criar um conjunto de escala de máquina virtual redundante de zona](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Peso de vMs em zonas usando um Balancer de Carga Padrão com uma extremidade frontal redundante de zona](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [Peso de saldo VMs dentro de uma zona usando um Balancer de carga padrão com uma extremidade frontal zonal](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Armazenamento com redundância entre zonas](../storage/common/storage-redundancy-zrs.md)
- [Base de Dados SQL](../sql-database/sql-database-high-availability.md#zone-redundant-configuration)
- [Recuperação após desastre geográfica dos Hubs de Eventos](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Recuperação após desastre geográfica do Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Criar um gateway de rede virtual com redundância entre zonas](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Adicione zona redundante região para Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Começar azure cache para redis zonas de disponibilidade](https://aka.ms/redis/az/getstarted)
- [Criar uma instância de Serviços de Domínio de Diretório Ativo Azure](../active-directory-domain-services/tutorial-create-instance.md)
- [Crie um cluster azure Kubernetes Service (AKS) que utilize zonas de disponibilidade](../aks/availability-zones.md)

## <a name="next-steps"></a>Passos seguintes
- [Modelos de início rápido](https://aka.ms/azqs)

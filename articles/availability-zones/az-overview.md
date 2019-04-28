---
title: Quais são as zonas de disponibilidade do Azure? | Microsoft Docs
description: Para criar aplicações de elevada disponibilidade e resilientes no Azure, as zonas de disponibilidade fornecer localizações fisicamente separadas, pode usar para executar seus recursos.
services: ''
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/18/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 0c5a57ab6d84e1eeda62ab149a9aa7eb3ca71a7a
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763773"
---
# <a name="what-are-availability-zones-in-azure"></a>Quais são as zonas de disponibilidade no Azure?
As zonas de disponibilidade é uma oferta que protege os seus aplicativos e dados de falhas de datacenter de elevada disponibilidade. As zonas de disponibilidade são localizações físicas únicas dentro de uma região do Azure. Cada zona é constituída por um ou mais datacenters equipados com energia, refrigeração e redes. Para garantir a resiliência, existe um mínimo de três zonas separadas em todas as regiões ativadas. A separação física das zonas de disponibilidade numa região protege as aplicações e dados de falhas de datacenter. Serviços com redundância de zona replicar os seus dados e aplicações em zonas de disponibilidade para proteger contra único pontos de falha. Com as zonas de disponibilidade, o Azure oferece o SLA de tempo de atividade VM do setor melhor 99,99%. O [SLA do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) completo explica a disponibilidade garantida do Azure em termos globais.

Uma zona de disponibilidade na região do Azure é uma combinação de um domínio de falha e um domínio de atualização. Por exemplo, se criar três ou mais VMs por zonas de três numa região do Azure, as suas VMs com eficiência são distribuídas entre três domínios de falha e três domínios de atualização. A plataforma do Azure reconhece essa distribuição em vários domínios de atualização para se certificar de que as VMs em diferentes horários não são atualizadas ao mesmo tempo.

Crie elevada disponibilidade em sua arquitetura de aplicativos ao colocalizar os recursos de computação, armazenamento, rede e dados dentro de uma zona e replicar noutras zonas. Serviços do Azure que suportam zonas de disponibilidade enquadram-se em duas categorias:

- **Serviços zonais** – afixar o recurso a uma zona específica (por exemplo, máquinas virtuais, discos geridos, endereços IP), ou
- **Serviços com redundância de zona** – plataforma replica automaticamente os entre zonas (por exemplo, com redundância de zona de armazenamento, base de dados SQL).

Para alcançar a continuidade do negócio abrangente no Azure, crie a sua arquitetura de aplicativos usando a combinação das zonas de disponibilidade com pares de região do Azure. Pode replicar de modo síncrono suas aplicações e dados com as zonas de disponibilidade numa região do Azure para elevada disponibilidade e replicar de forma assíncrona entre regiões do Azure para a proteção de recuperação após desastre.
 
![Vista concetual da zona de uma direção para baixo numa região](./media/az-overview/az-graphic-two.png)

## <a name="services-support-by-region"></a>Suporte de serviços por região

As combinações de serviços do Azure e regiões que suportam as zonas de disponibilidade são:


|                                 |Américas |              |           |           | Europa |              |          |              | Ásia-Pacífico |                 |
|----------------------------|----------|----------|---------|---------|--------------|------------|--------|----------|----------|-------------|
|          |EUA Central|EUA Leste|EUA Leste 2|EUA Oeste 2|França Central|Europa do Norte|Reino Unido Sul|Europa Ocidental|Leste do Japão|Sudeste Asiático|
| **Computação**                         |            |              |           |           |                |              |          |             |            |                |
| Máquinas Virtuais do Linux          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Máquinas Virtuais do Windows        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Conjuntos de Dimensionamento de Máquinas Virtuais      | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Armazenamento**   |            |              |           |           |                |              |          |             |            |                |
| Managed Disks                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Armazenamento com redundância de zona          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Redes**                     |            |              |           |           |                |              |          |             |            |                |
| Endereço IP padrão        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; &#42;| &#10003;    | &#10003;   | &#10003;       |
| Balanceador de Carga Standard     | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; &#42;| &#10003;    | &#10003;   | &#10003;       |
| Gateway de VPN                     | &#10003;   |              | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    |            | &#10003;       |
| ExpressRoute                    | &#10003;   |              | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    |            | &#10003;       |
| Gateway de aplicação (pré-visualização)   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    |            | &#10003;       |
| **Bases de dados**                     |            |              |           |           |                |              |          |             |            |                |
| SQL Database                    | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |            | &#10003;       |
| **Análise**                       |            |              |           |           |                |              |          |             |            |                |
| Hubs de Eventos                      | &#10003;   |              | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    |            | &#10003;       |
| **Integração**                     |            |              |           |           |                |              |          |             |            |                |
| O Service Bus (apenas no escalão Premium) | &#10003;   |              | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    |            | &#10003;       |


&#42;Recursos criados no sul do Reino Unido, antes de 25 de Março de 2019 em breve será convertido para ser com redundância de zona. Recursos criados após a 25 de Março de 2019 será com redundância de zona imediatamente.

## <a name="services-resiliency"></a>Resiliência de serviços
Todos os serviços de gestão do Azure têm uma arquitetura para ser resiliente de falhas de nível de região. O espectro de falhas, uma ou mais falhas de zona de disponibilidade numa região tem um raio de falha mais pequeno em comparação com uma falha de região inteira. Azure pode recuperar de uma falha de nível de zona dos serviços de gestão dentro da região ou em outra região do Azure. O Azure efetua a zona de uma manutenção crítica por vez numa região, para evitar quaisquer falhas que afetam o recursos de cliente implementados em zonas de disponibilidade numa região.

## <a name="pricing"></a>Preços
Não existe nenhum custo adicional para máquinas virtuais implementadas numa zona de disponibilidade. Tempo de atividade VM de 99,99% são oferecidos SLAS quando duas ou mais VMs são implementadas em duas ou mais zonas de disponibilidade numa região do Azure. Haverá adicionais custos de transferência de dados a VM para VM da zona de disponibilidade entre. Para obter mais informações, reveja os [preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/) página.


## <a name="get-started-with-availability-zones"></a>Introdução às zonas de disponibilidade
- [Criar uma máquina virtual](../virtual-machines/windows/create-portal-availability-zone.md)
- [Adicionar um disco gerido com o PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Criar um conjunto de dimensionamento de máquinas virtuais com redundância de zona](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Balancear carga de VMs por zonas com um balanceador de carga Standard com um front-end com redundância de zona](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [Balancear carga de VMs dentro de uma zona com um balanceador de carga Standard com um front-end zonal](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Armazenamento com redundância entre zonas](../storage/common/storage-redundancy-zrs.md)
- [Base de Dados SQL](../sql-database/sql-database-high-availability.md#zone-redundant-configuration)
- [Recuperação após desastre geográfica dos Hubs de Eventos](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Recuperação após desastre geográfica do Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Criar um gateway de rede virtual com redundância entre zonas](../vpn-gateway/create-zone-redundant-vnet-gateway.md)


## <a name="next-steps"></a>Passos Seguintes
- [Modelos de início rápido](https://aka.ms/azqs)

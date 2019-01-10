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
ms.date: 08/31/2018
ms.author: cynthn
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.openlocfilehash: 360f5cca3da5e9987b5e057449fb9c2cbb910552
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157841"
---
# <a name="what-are-availability-zones-in-azure"></a>Quais são as zonas de disponibilidade no Azure?
As zonas de disponibilidade é uma oferta que protege os seus aplicativos e dados de falhas de datacenter de elevada disponibilidade. As zonas de disponibilidade são localizações físicas únicas dentro de uma região do Azure. Cada zona é constituída por um ou mais datacenters equipados com energia, refrigeração e redes. Para garantir a resiliência, existe um mínimo de três zonas separadas em todas as regiões ativadas. A separação física das zonas de disponibilidade numa região protege as aplicações e dados de falhas de datacenter. Serviços com redundância de zona replicar os seus dados e aplicações em zonas de disponibilidade para proteger contra único pontos de falha. Com as zonas de disponibilidade, o Azure oferece o SLA de tempo de atividade VM do setor melhor 99,99%. O [SLA do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) completo explica a disponibilidade garantida do Azure em termos globais.

Uma zona de disponibilidade na região do Azure é uma combinação de um domínio de falha e um domínio de atualização. Por exemplo, se criar três ou mais VMs por zonas de três numa região do Azure, as suas VMs com eficiência são distribuídas entre três domínios de falha e três domínios de atualização. A plataforma do Azure reconhece essa distribuição em vários domínios de atualização para se certificar de que as VMs em diferentes horários não são atualizadas ao mesmo tempo.

Crie elevada disponibilidade em sua arquitetura de aplicativos ao colocalizar os recursos de computação, armazenamento, rede e dados dentro de uma zona e replicar noutras zonas. Serviços do Azure que suportam zonas de disponibilidade enquadram-se em duas categorias:

- **Serviços zonais** – afixar o recurso a uma zona específica (por exemplo, máquinas virtuais, discos geridos, endereços IP), ou
- **Serviços com redundância de zona** – plataforma replica automaticamente os entre zonas (por exemplo, com redundância de zona de armazenamento, base de dados SQL).

Para alcançar a continuidade do negócio abrangente no Azure, crie a sua arquitetura de aplicativos usando a combinação das zonas de disponibilidade com pares de região do Azure. Pode replicar de modo síncrono suas aplicações e dados com as zonas de disponibilidade numa região do Azure para elevada disponibilidade e replicar de forma assíncrona entre regiões do Azure para a proteção de recuperação após desastre.
 
![Vista concetual da zona de uma direção para baixo numa região](./media/az-overview/az-graphic-two.png)

## <a name="regions-that-support-availability-zones"></a>Regiões que suportam as zonas de disponibilidade

- EUA Central
- E.U.A. Leste 2 (pré-visualização)
- França Central
- Europa do Norte
- Sudeste Asiático 
- Europa Ocidental
- EUA Oeste 2



## <a name="services-that-support-availability-zones"></a>Serviços que suportam as zonas de disponibilidade
Os serviços do Azure que suportam as zonas de disponibilidade são:

- Máquinas Virtuais do Linux
- Máquinas Virtuais do Windows
- Conjuntos de Dimensionamento de Máquinas Virtuais
- Managed Disks
- Load balancer
- Endereço IP público
- Armazenamento com redundância entre zonas

- SQL Database
- Hubs de Eventos
- O Service Bus (apenas no escalão Premium)
- Gateway de VPN
- ExpressRoute
- Gateway de aplicação (pré-visualização)


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
- [Recuperação após desastre geográfica dos Hubs de Eventos](../event-hubs/event-hubs-geo-dr.md#availability-zones-preview)
- [Recuperação após desastre geográfica do Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones-preview)
- [Criar um gateway de rede virtual com redundância entre zonas](../vpn-gateway/create-zone-redundant-vnet-gateway.md)


## <a name="next-steps"></a>Passos Seguintes
- [Modelos de início rápido](https://aka.ms/azqs)

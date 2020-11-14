---
title: Opções de disponibilidade
description: Conheça as funcionalidades de disponibilidade para executar máquinas virtuais em Azure
author: cynthn
ms.author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: a0ea9a0871dd57088473999b28553258ff210038
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94628769"
---
# <a name="availability-options-for-virtual-machines-in-azure"></a>Opções de disponibilidade das máquinas virtuais no Azure

Este artigo fornece-lhe uma visão geral das funcionalidades de disponibilidade das máquinas virtuais Azure (VMs).

## <a name="high-availability"></a>Elevada disponibilidade

As cargas de trabalho são normalmente distribuídas por diferentes máquinas virtuais para obter alta produção, desempenho e para criar redundância no caso de um VM ser impactado devido a uma atualização ou outro evento. 

Existem poucas opções que o Azure fornece para alcançar a Alta Disponibilidade. Primeiro vamos falar de construções básicas. 

### <a name="availability-zones"></a>Zonas de disponibilidade

[As zonas de disponibilidade](../availability-zones/az-overview.md) expandem o nível de controlo que tem para manter a disponibilidade das aplicações e dados nos seus VMs. Uma Zona de Disponibilidade é uma zona fisicamente separada, dentro de uma região de Azure. Existem três Zonas de Disponibilidade por região de Azure suportada. 

Cada Zona de Disponibilidade tem uma fonte de energia, uma rede e um sistema de refrigeração distintos. Ao arquiteto as suas soluções para usar VMs replicados em zonas, pode proteger as suas aplicações e dados da perda de um datacenter. Se uma zona estiver comprometida, então aplicações replicadas e dados estão instantaneamente disponíveis noutra zona. 

![Zonas de disponibilidade](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Saiba mais sobre a implementação de um [Windows](./windows/create-powershell-availability-zone.md) ou [Linux](./linux/create-cli-availability-zone.md) VM numa Zona de Disponibilidade.


### <a name="fault-domains"></a>Domínios de falha

Um domínio de falha é um grupo lógico de hardware subjacente que partilha a mesma origem de energia e o mesmo comutador de rede física, semelhante a uma estrutura dentro de um datacenter no local. 

### <a name="update-domains"></a>Domínios de atualização

Um domínio de atualização é um grupo lógico de hardware subjacente que pode entrar em manutenção ou ser reiniciado ao mesmo tempo. 

Esta abordagem garante que, pelo menos, uma instância da aplicação permanece sempre em execução quando a plataforma Azure entra em manutenção periódica. A ordem dos domínios de atualização a ser reiniciada pode não prosseguir sequencialmente durante a manutenção, mas apenas um domínio de atualização é reiniciado de cada vez.


## <a name="virtual-machines-scale-sets"></a>Conjuntos de escala de máquinas virtuais 

Conjuntos de escala de máquina virtual Azure permitem criar e gerir um grupo de VMs equilibrados de carga. O número de instâncias de VM pode aumentar ou diminuir automaticamente como resposta à procura ou horário definido. Os conjuntos de escala proporcionam uma elevada disponibilidade para as suas aplicações e permitem-lhe gerir, configurar e atualizar muitos VMs. Recomendamos que sejam criados dois ou mais VMs dentro de uma escala definida para fornecer uma aplicação altamente disponível e para atender o [Azure SLA de 99,95%.](https://azure.microsoft.com/support/legal/sla/virtual-machines/) Não há qualquer custo para a escala em si, você só paga por cada instância VM que você cria. Quando um único VM está a utilizar [SSDs premium Azure,](./disks-types.md#premium-ssd)o Azure SLA aplica-se a eventos de manutenção não planeados. Máquinas virtuais num conjunto de escala podem ser implantadas em vários domínios de atualização e domínios de avarias para maximizar a disponibilidade e a resiliência a falhas devido a interrupções no centro de dados e eventos de manutenção planeados ou não planeados. Máquinas virtuais em um conjunto de escala também podem ser implantadas numa única zona de Disponibilidade, ou regionalmente. As opções de implantação da zona de disponibilidade podem diferir com base no modo de orquestração.

**Domínios de avaria e domínios de atualização**

Os conjuntos de escala de máquina virtual simplificam o design para uma alta disponibilidade, alinhando domínios de falhas e domínios de atualização. Só terá de definir a contagem de domínios de avaria para o conjunto de escalas. O número de domínios de avaria disponíveis para os conjuntos de escala pode variar por região. Ver [Gerir a disponibilidade de máquinas virtuais em Azure](./manage-availability.md).


## <a name="availability-sets"></a>Conjuntos de disponibilidade
Um conjunto de disponibilidade é um agrupamento lógico de VMs que permite ao Azure compreender a forma como a aplicação é criada, para fornecer redundância e disponibilidade. Recomendamos que sejam criados dois ou mais VMs dentro de um conjunto de disponibilidade para fornecer uma aplicação altamente disponível e para atender o [Azure SLA de 99,95%.](https://azure.microsoft.com/support/legal/sla/virtual-machines/) Não há qualquer custo para o próprio Conjunto de Disponibilidade, só paga por cada instância VM que cria. Quando um único VM está a utilizar [SSDs premium Azure,](./disks-types.md#premium-ssd)o Azure SLA aplica-se a eventos de manutenção não planeados.

Num conjunto de disponibilidade, os VMs são automaticamente distribuídos por estes domínios de avaria. Esta abordagem limita o impacto de potenciais falhas de hardware físico, indisponibilidade de rede ou falhas de energia.

Para as VMs que utilizam os [Managed Disks do Azure](./faq-for-disks.md), as VMs são alinhadas com domínios de falha de discos geridos ao utilizar um conjunto de disponibilidade gerido. Este alinhamento garante que todos os discos geridos ligados a uma VM estão dentro do mesmo domínio de falha do disco gerido. 

Apenas as VMs com discos geridos podem ser criadas num conjunto de disponibilidade gerido. O número de domínios de falha do disco gerido varia por região - dois ou três domínios de falha do disco gerido por região. Pode ler mais sobre estes domínios de falha de disco geridos para [VMs Linux](./manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) ou [VMs do Windows](./manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

![Conjunto de disponibilidade gerido](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


Os VMs dentro de um conjunto de disponibilidade também são distribuídos automaticamente por domínios de atualização. 

![Conjuntos de disponibilidade](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>Passos seguintes
Pode agora começar a utilizar estas funcionalidades de redundância e disponibilidade para criar o seu ambiente do Azure. Para informações relativas a melhores práticas, veja [Melhores Práticas de Disponibilidade do Azure](/azure/architecture/checklist/resiliency-per-service).

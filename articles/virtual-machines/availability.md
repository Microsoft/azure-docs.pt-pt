---
title: Opções de disponibilidade para Máquinas Virtuais Azure
description: Saiba mais sobre as opções de disponibilidade para executar máquinas virtuais em Azure
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 03/08/2021
ms.reviewer: cynthn
ms.openlocfilehash: 821c50b5cb48bddb586c3589326a7f31c7bafded
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604537"
---
# <a name="availability-options-for-azure-virtual-machines"></a>Opções de disponibilidade para Máquinas Virtuais Azure
Este artigo fornece uma visão geral das opções de disponibilidade para máquinas virtuais Azure (VMs).

## <a name="availability-zones"></a>Zonas de disponibilidade

[As zonas de disponibilidade](../availability-zones/az-overview.md?context=/azure/virtual-machines/context/context) expandem o nível de controlo que tem para manter a disponibilidade das aplicações e dados nos seus VMs. Uma Zona de Disponibilidade é uma zona fisicamente separada, dentro de uma região de Azure. Existem três Zonas de Disponibilidade por região de Azure suportada. 

Cada Zona de Disponibilidade tem uma fonte de energia, uma rede e um sistema de refrigeração distintos. Ao conceber as suas soluções para utilizar VMs replicados em zonas, pode proteger as suas aplicações e dados da perda de um centro de dados. Se uma zona estiver comprometida, então aplicações replicadas e dados estão instantaneamente disponíveis noutra zona. 

## <a name="availability-sets"></a>Conjuntos de disponibilidade
Um [conjunto de disponibilidade](availability-set-overview.md) é um agrupamento lógico de VMs que permite ao Azure entender como a sua aplicação é construída para garantir redundância e disponibilidade. Recomendamos que sejam criados dois ou mais VMs dentro de um conjunto de disponibilidade para fornecer uma aplicação altamente disponível e para atender o [Azure SLA de 99,95%.](https://azure.microsoft.com/support/legal/sla/virtual-machines/) Não há qualquer custo para o próprio Conjunto de Disponibilidade, só paga por cada instância VM que cria.


## <a name="virtual-machines-scale-sets"></a>Conjuntos de escala de máquinas virtuais 

[Conjuntos de escala de máquina virtual Azure](../virtual-machine-scale-sets/overview.md?context=/azure/virtual-machines/context/context) permitem criar e gerir um grupo de VMs equilibrados de carga. O número de instâncias de VM pode aumentar ou diminuir automaticamente como resposta à procura ou horário definido. Os conjuntos de escala proporcionam uma elevada disponibilidade para as suas aplicações e permitem-lhe gerir, configurar e atualizar muitos VMs. Recomendamos que sejam criados dois ou mais VMs dentro de uma escala definida para fornecer uma aplicação altamente disponível e para atender o [Azure SLA de 99,95%.](https://azure.microsoft.com/support/legal/sla/virtual-machines/) Não há qualquer custo para a escala em si, você só paga por cada instância VM que você cria.

Máquinas virtuais em um conjunto de escala também podem ser implantadas numa única zona de Disponibilidade, ou regionalmente. As opções de implantação da zona de disponibilidade podem diferir com base no [modo de orquestração](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md?context=/azure/virtual-machines/context/context).

## <a name="load-balancer"></a>Balanceador de carga
Combine o [Balançador de Carga Azure](../load-balancer/load-balancer-overview.md) com uma zona de disponibilidade ou disponibilidade definida para obter a maior resiliência da aplicação. O Balanceador de Carga do Azure distribui tráfego entre várias máquinas virtuais. Para as nossas máquinas virtuais de camada Standard, o Balanceador de Carga do Azure está incluído. Nem todas as camadas de máquina virtual incluem o Balanceador de Carga do Azure. Para obter mais informações sobre a carga que equilibra as suas máquinas virtuais, consulte **máquinas virtuais de equilíbrio de carga** para [Linux](linux/tutorial-load-balancer.md) ou [Windows](windows/tutorial-load-balancer.md).


## <a name="azure-storage-redundancy"></a>Redundância do Armazenamento do Azure
O Azure Storage armazena sempre várias cópias dos seus dados para que esteja protegido contra eventos planeados e não planeados, incluindo falhas de hardware transitórios, falhas de rede ou de energia e desastres naturais maciços. A redundância garante que a sua conta de armazenamento cumpre os seus objetivos de disponibilidade e durabilidade mesmo face a falhas.

Ao decidir qual a opção de despedimento melhor para o seu cenário, considere as trocas entre custos mais baixos e maior disponibilidade. Os fatores que ajudam a determinar qual a opção de redundância que deve escolher incluem:
- Como os seus dados são replicados na região primária
- Se os seus dados são replicados para uma segunda região que está geograficamente distante da região primária, para proteger contra desastres regionais
- Se a sua aplicação requer acesso lido aos dados replicados na região secundária se a região primária ficar indisponível por qualquer motivo

Para mais informações, consulte [a redundância do Azure Storage](../storage/common/storage-redundancy.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery
Como organização, é necessário adotar uma estratégia de continuidade de negócios e recuperação de desastres (BCDR) que mantenha os seus dados seguros, bem como as suas aplicações e cargas de trabalho online, quando ocorrem interrupções planeadas e não planeadas.

[A Azure Site Recovery](../site-recovery/site-recovery-overview.md) ajuda a garantir a continuidade do negócio mantendo aplicações de negócio e cargas de trabalho em execução durante as interrupções. O Site Recovery replica cargas de trabalho em execução em máquinas virtuais (VMs) e físicas a partir de um site primário para uma localização secundária. Quando ocorre uma falha no seu site primário, pode realizar a ativação pós-falha para a localização secundária e aceder às aplicações a partir daí. Depois de executar novamente a localização primária, pode fazer a reativação pós-falha.

O Site Recovery pode gerir a replicação de:
- VMs do Azure a replicarem entre regiões do Azure.
- VMs no local, VMs Azure Stack e servidores físicos.

## <a name="next-steps"></a>Passos seguintes
- [Criar uma máquina virtual em uma zona de disponibilidade](/azure/virtual-machines/linux/create-cli-availability-zone)
- [Criar uma máquina virtual num conjunto de disponibilidade](/azure/virtual-machines/linux/tutorial-availability-sets)
- [Criar um conjunto de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/quick-create-portal.md)
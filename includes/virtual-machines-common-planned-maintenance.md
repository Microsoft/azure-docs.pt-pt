---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 12/14/2018
ms.author: shants
ms.custom: include file
ms.openlocfilehash: c26c037455b6d14a906894ec39bf46630826950b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60301721"
---
Azure atualiza periodicamente uma plataforma para melhorar a fiabilidade, desempenho e segurança da infraestrutura do anfitrião para máquinas virtuais. Estes variam de atualizações de componentes de software no ambiente de alojamento, atualizando os componentes de rede, a desativação de hardware de aplicação de patches. A maioria destas atualizações não tiver nenhum impacto para as máquinas virtuais alojadas. No entanto, existem casos em que as atualizações têm um impacto e Azure escolhe o método com menos impacto de atualizações:

- Se uma atualização no rebootful não for possível, que a VM está em pausa enquanto o anfitrião é atualizado ou encontra-se em direto migrado para um anfitrião já atualizado.

- Se a manutenção requer uma reinicialização, receberá um aviso de quando a manutenção está prevista. Azure também dá uma janela de tempo em que pode iniciar a manutenção por conta própria, ao mesmo tempo que funcione para si. O Azure investe em tecnologias para reduzir os casos, quando as VMs têm de ser reiniciado para manutenção planeada de plataforma. 

Esta página descreve a forma como o Azure executa ambos os tipos de manutenção. Para obter mais informações sobre eventos não planeados (falhas), consulte Gerir a disponibilidade das máquinas virtuais para [Windows](../articles/virtual-machines/windows/manage-availability.md) ou [Linux](../articles/virtual-machines/linux/manage-availability.md).

Pode receber notificação de dentro da VM sobre manutenção futura, utilizando os eventos agendados para [Windows](../articles/virtual-machines/windows/scheduled-events.md) ou [Linux](../articles/virtual-machines/linux/scheduled-events.md).

Para obter informações "procedimentos" sobre o gerenciamento de manutenção planeada, consulte "Manipulação planeado notificações de manutenção" para [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) ou [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="maintenance-not-requiring-a-reboot"></a>Manutenção não exigir um reinício

O objetivo de manutenção a maioria dos que não requer uma reinicialização é inferior a 10 segundos colocar em pausa para a VM. Em determinadas memória casos preservação da manutenção mecanismos são utilizados, que interrompe a VM para até 30 segundos e preserva a memória RAM. A máquina virtual é retomada, em seguida, e o relógio da máquina virtual é sincronizado automaticamente. O Azure é cada vez mais usando tecnologias de migração em direto e melhorando a memória, preservando o mecanismo de manutenção para reduzir a duração de pausa.

Essas operações de manutenção não rebootful são o domínio de falha aplicada por domínio de falha e o progresso é interrompido se qualquer sinais de estado de funcionamento de aviso são recebidas. 

Alguns aplicativos podem ser afetados por estes tipos de atualizações. No caso da VM estiver em direto migrado para outro anfitrião, algumas cargas de trabalho confidenciais podem observar uma degradação do desempenho de pequenas em alguns minutos que levou à pausa VM. Tais aplicativos podem se beneficiar usando eventos agendados para [Windows](../articles/virtual-machines/windows/scheduled-events.md) ou [Linux](../articles/virtual-machines/linux/scheduled-events.md) para se prepararem para a manutenção da VM e não tiver nenhum impacto durante a manutenção do Azure. Azure também está trabalhando em recursos de controle de manutenção para essas aplicações Ultra confidenciais. 


## <a name="maintenance-requiring-a-reboot"></a>Necessidade de um reinício de manutenção

Nos raros casos quando as VMs têm de ser reiniciado para manutenção planeada, será notificado com antecedência. Manutenção planeada tem duas fases: a janela de self-service e uma janela de manutenção agendada.

O **janela de self-service** permite-lhe iniciar a manutenção nas suas VMs. Durante este período, pode consultar cada VM para ver o respetivo estado e verificar o resultado de sua última solicitação de manutenção.

Quando começa a manutenção self-service, a VM é implementada novamente para um nó já atualizado. Uma vez que a VM reiniciar, o disco temporário é perdido e endereços IP dinâmicos associados à interface de rede virtual são atualizados.

Se começa a manutenção self-service e existe um erro durante o processo, a operação está parada, a VM não é atualizada e terá a opção para repetir a manutenção self-service. 

Quando a janela de self-service tiver passado, o **janela de manutenção agendada** começa. Durante este período de tempo, ainda pode consultar a janela de manutenção, mas não é possível iniciar a manutenção por conta própria.

Para informações sobre como gerenciar a necessidade de um reinício de manutenção, consulte "Manipulação planeado notificações de manutenção" para [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) ou [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Considerações de disponibilidade durante a manutenção agendada 

Se optar por esperar até a janela de manutenção agendada, existem alguns aspetos a considerar para manter a maior disponibilidade das suas VMs. 

#### <a name="paired-regions"></a>Regiões emparelhadas

Cada região do Azure é emparelhada com outra região na mesma geografia e juntos eles tornam a um par regional. Na fase de manutenção agendada, o Azure irá atualizar apenas as VMs numa única região de um par de região. Por exemplo, ao atualizar a VM nos e.u.a. Centro-Norte, o Azure não atualizar qualquer VM no centro-sul ao mesmo tempo. No entanto, outras regiões, como a Europa do Norte, podem estar sob manutenção em simultâneo como os E.U.A. Leste. Noções básicas sobre como funcionam os pares de região pode ajudá-lo melhor distribuir as VMs em várias regiões. Para obter mais informações, consulte [pares de região do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Conjuntos de disponibilidade e conjuntos de dimensionamento

Ao implementar uma carga de trabalho em VMs do Azure, pode criar as VMs dentro de um conjunto de disponibilidade para proporcionar elevada disponibilidade para a sua aplicação. Isto assegura que durante um período de indisponibilidade ou eventos de manutenção rebootful, pelo menos uma VM está disponível.

Dentro de um conjunto de disponibilidade, VMs individuais são distribuídas por até 20 domínios de atualização (UDs). Durante a manutenção agendada, apenas um domínio com uma única atualização é atualizado em qualquer momento. A ordem dos domínios de atualização que está a ser atualizado necessariamente não acontece em seqüência. 

Os conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure que permite-lhe implementar e gerir um conjunto de VMs idênticas como um único recurso. O conjunto de dimensionamento será automaticamente implantado em vários domínios de atualização, como VMs num conjunto de disponibilidade. Assim como com os conjuntos de disponibilidade, com conjuntos de dimensionamento apenas um domínio com uma única atualização é atualizado em qualquer momento durante a manutenção agendada.

Para obter mais informações sobre como configurar as VMs de elevada disponibilidade, consulte Gerir a disponibilidade das suas máquinas virtuais para [Windows](../articles/virtual-machines/windows/manage-availability.md) ou [Linux](../articles/virtual-machines/linux/manage-availability.md).

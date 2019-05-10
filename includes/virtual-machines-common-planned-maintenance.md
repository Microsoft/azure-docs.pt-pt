---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 4/30/2019
ms.author: shants
ms.custom: include file
ms.openlocfilehash: adf99b941a775f105d8c65da3ac6c11dc7257120
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65416346"
---
Azure atualiza periodicamente uma plataforma para melhorar a fiabilidade, desempenho e segurança da infraestrutura do anfitrião para máquinas virtuais. Estes variam de atualizações de componentes de software no ambiente de alojamento, atualizando os componentes de rede, a desativação de hardware de aplicação de patches. A maioria destas atualizações não tiver nenhum impacto para as máquinas virtuais alojadas. No entanto, existem casos em que as atualizações têm um impacto e Azure escolhe o método com menos impacto de atualizações:

- Se uma atualização no rebootful não for possível, que a VM está em pausa enquanto o anfitrião é atualizado ou encontra-se em direto migrado para um anfitrião já atualizado.

- Se a manutenção requer uma reinicialização, receberá um aviso de quando a manutenção está prevista. Azure também dá uma janela de tempo em que pode iniciar a manutenção por conta própria, ao mesmo tempo que funcione para si. Janela de tempo de manutenção automática, normalmente, é 30 dias, a menos que seja urgente para efetuar a manutenção. Azure também está a investir em tecnologias para reduzir os casos, quando as VMs têm de ser reiniciado para manutenção planeada de plataforma. 

Esta página descreve a forma como o Azure executa ambos os tipos de manutenção. Para obter mais informações sobre eventos não planeados (falhas), consulte Gerir a disponibilidade das máquinas virtuais para [Windows](../articles/virtual-machines/windows/manage-availability.md) ou [Linux](../articles/virtual-machines/linux/manage-availability.md).

Pode receber notificação de dentro da VM sobre manutenção futura, utilizando os eventos agendados para [Windows](../articles/virtual-machines/windows/scheduled-events.md) ou [Linux](../articles/virtual-machines/linux/scheduled-events.md).

Para obter informações "procedimentos" sobre o gerenciamento de manutenção planeada, consulte "Manipulação planeado notificações de manutenção" para [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) ou [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="maintenance-not-requiring-a-reboot"></a>Manutenção não exigir um reinício

Conforme afirmamos acima, a maior parte das atualizações de plataforma são feitas com impacto zero na VMs de cliente. Quando a atualização de impacto zero não é possível Azure escolhe o mecanismo de atualização que é o mínimo impacto para VMs de cliente. A maioria da manutenção impacto diferente de zero faz com que menos de 10 segundos colocar em pausa para a VM. Em certos casos, são utilizados mecanismos de manutenção preservando de memória, que interrompe a VM para até 30 segundos e preserva a memória RAM. A VM é retomada, em seguida, e seu relógio é sincronizado automaticamente. Preservação da manutenção de memória funciona, para mais de 90%, as VMs do Azure, exceto a série G, M, N e H. O Azure é cada vez mais usando tecnologias de migração em direto e melhorando a memória, preservando o mecanismo de manutenção para reduzir a duração de pausa.  

Essas operações de manutenção não rebootful são o domínio de falha aplicada por domínio de falha e o progresso é interrompido se qualquer sinais de estado de funcionamento de aviso são recebidas. 

Alguns aplicativos podem ser afetados por estes tipos de atualizações. No caso da VM estiver em direto migrado para outro anfitrião, algumas cargas de trabalho confidenciais podem observar uma degradação do desempenho de pequenas em alguns minutos que levou à pausa VM. Tais aplicativos podem se beneficiar usando eventos agendados para [Windows](../articles/virtual-machines/windows/scheduled-events.md) ou [Linux](../articles/virtual-machines/linux/scheduled-events.md) para se prepararem para a manutenção da VM e não tiver nenhum impacto durante a manutenção do Azure. Azure também está trabalhando em recursos de controle de manutenção para essas aplicações Ultra confidenciais. 

### <a name="live-migration"></a>Migração em direto

Migração em direto é uma operação de não-rebootful que preserva a memória para a VM e resulta numa limitada colocar em pausa ou congelar, duração, normalmente, não mais de 5 segundos. Hoje em dia, toda a infraestrutura como um máquinas de virtuais de serviço (IaaS), além da série G, M, N e H, são elegíveis para migração em direto. Tal equivale a mais de 90% das VMs IaaS implementado à frota do Azure. 

Migração em direto é iniciada por recursos de infraestrutura do Azure nos seguintes cenários:
- Manutenção Planeada
- Falha de hardware
- Otimizações de alocação

Migração em direto é aproveitada em alguns cenários de manutenção planeada e eventos agendados podem ser utilizados para saber com antecedência Live quando início de operações de migração.

Migração em direto também é usada para mover máquinas virtuais do hardware com uma iminente, prevista falha quando detectado por nossos algoritmos de Machine Learning e otimizar as alocações de Máquina Virtual. Para saber mais acerca da nossa modelação preditiva que Deteta instâncias do hardware degradado, consulte nossa mensagem de blogue intitulado [resiliência de aprimoramento da Máquina Virtual do Azure com preditiva ML e a migração em direto](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure). Os clientes recebem sempre um aviso de migração em direto no portal do Azure no Monitor / registos de estado de funcionamento do serviço, bem como, por meio de eventos agendados, se estes estão a ser utilizados.

## <a name="maintenance-requiring-a-reboot"></a>Necessidade de um reinício de manutenção

Nos raros casos quando as VMs têm de ser reiniciado para manutenção planeada, será notificado com antecedência. Manutenção planeada tem duas fases: a janela de self-service e uma janela de manutenção agendada.

O **janela de self-service** permite-lhe iniciar a manutenção nas suas VMs. Durante este período é, normalmente, quatro semanas, pode consultar cada VM para ver o respetivo estado e verificar o resultado de sua última solicitação de manutenção.

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

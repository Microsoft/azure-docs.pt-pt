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
ms.openlocfilehash: c2931fa410cf92755a5df5b7129dcf93de900930
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155916"
---
Azure atualiza periodicamente sua plataforma para melhorar a fiabilidade, desempenho e segurança da infraestrutura do anfitrião para máquinas virtuais. A finalidade destes intervalos de atualizações da aplicação de patches de componentes de software no ambiente de alojamento para atualizar a componentes de rede ou a desativação de hardware. 

Atualizações raramente afetam as VMs alojadas. Quando as atualizações têm um efeito, o Azure escolhe o método com menos impacto de atualizações:

- Se a atualização não requer um reinício, a VM está em pausa enquanto o anfitrião é atualizado, ou a VM está migradas ao vivo para um anfitrião já atualizado.

- Se a manutenção requer uma reinicialização, receberá uma notificação a manutenção planeada. O Azure também fornece uma janela de tempo em que pode iniciar a manutenção por conta própria, ao mesmo tempo que funcione para si. A janela de manutenção automática, normalmente, é 30 dias, a menos que a manutenção é urgente. O Azure investe em tecnologias para reduzir o número de casos em que a manutenção planeada plataforma requer as VMs de ser reiniciado. 

Esta página descreve a forma como o Azure executa ambos os tipos de manutenção. Para obter mais informações sobre eventos não planeados (falhas), consulte [gerir a disponibilidade de VMs para Windows](../articles/virtual-machines/windows/manage-availability.md) ou o artigo correspondente para [Linux](../articles/virtual-machines/linux/manage-availability.md).

Dentro de uma VM, pode obter notificações sobre manutenção futura pelo [usando eventos agendados para Windows](../articles/virtual-machines/windows/scheduled-events.md) ou para [Linux](../articles/virtual-machines/linux/scheduled-events.md).

Para obter instruções sobre como gerir a manutenção planeada, consulte [planejado de manipulação de notificações de manutenção para Linux](../articles/virtual-machines/linux/maintenance-notifications.md) ou o artigo correspondente para [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="maintenance-that-doesnt-require-a-reboot"></a>Manutenção não exige um reinício

Conforme declarado anteriormente, a maior parte das atualizações de plataforma não afetam VMs de cliente. Quando uma atualização sem impacto não for possível, o Azure escolhe o mecanismo de atualização que é o mínimo impacto para VMs de cliente. 

A maioria dos manutenção de impacto diferente de zero coloca em pausa a VM por menos de 10 segundos. Em certos casos, o Azure utiliza mecanismos de manutenção de preservação de memória. Estes mecanismos colocar em pausa a VM para até 30 segundos e preservar a memória RAM. A VM é retomada, em seguida, e seu relógio é sincronizado automaticamente. 

Manutenção de preservação de memória funciona para mais de 90 por cento de VMs do Azure. Ele não funciona para a série G, M, N e H. Cada vez mais, o Azure utiliza tecnologias de migração em direto e melhora a preservação de memória mecanismos de manutenção para reduzir as durações de colocar em pausa.  

Estas operações de manutenção que não requerem um reinício são de domínio de falha aplicadas cada vez. Param se receberem qualquer sinais de estado de funcionamento de aviso. 

Esses tipos de atualizações podem afetar alguns aplicativos. Quando a VM estiver migradas ao vivo para outro anfitrião, algumas cargas de trabalho confidenciais poderão mostrar uma degradação do desempenho de pequenas em alguns minutos que levou à pausa VM. Para preparar para a manutenção da VM e reduzir o impacto durante a manutenção do Azure, experimente [usando eventos agendados para Windows](../articles/virtual-machines/windows/scheduled-events.md) ou [Linux](../articles/virtual-machines/linux/scheduled-events.md) para essas aplicações. Azure está a trabalhar nos recursos de controle de manutenção para estas aplicações confidenciais. 

### <a name="live-migration"></a>Migração em direto

Migração em direto é uma operação que não requer uma reinicialização e que preserva a memória para a VM. Ele faz com que uma pausa ou um congelamento, duração, normalmente, não mais de 5 segundos. Exceto para G, série H, M e N, toda a infraestrutura como um serviço (IaaS) de VMs, é elegível para migração em direto. As VMs elegíveis representam mais de 90 por cento das VMs IaaS que são implementadas para a Azure frota. 

A plataforma Azure inicia a migração em direto nos seguintes cenários:
- Manutenção planeada
- Falha de hardware
- Otimizações de alocação

Alguns cenários de manutenção planeada utilizem migração em direto, e pode usar eventos agendados para saber com antecedência ao live começarão a operações de migração.

Também pode ser utilizada a migração em direto para mover as VMs quando algoritmos do Azure Machine Learning prever uma iminente falha de hardware ou quando quiser otimizar as alocações de VM. Para obter mais informações sobre a modelação preditiva que Deteta instâncias do hardware degradado, consulte [resiliência da VM do Azure de melhorar com preditiva de machine learning e de migração em direto](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure). Notificações de migração em direto são apresentados no portal do Azure nos Monitor e o estado de funcionamento do serviço de registos, bem como em eventos agendados, se utilizar estes serviços.

## <a name="maintenance-that-requires-a-reboot"></a>Manutenção que requer uma reinicialização

Nos raros casos em que as VMs têm de ser reiniciado para manutenção planeada, será notificado com antecedência. Manutenção planeada tem duas fases: a fase de self-service e uma fase de manutenção agendada.

Durante a *self-service fase*, que, normalmente, dura quatro semanas, iniciar a manutenção nas suas VMs. Como parte do Self-Service, pode consultar cada VM para ver o estado e o resultado da sua última solicitação de manutenção.

Quando começa a manutenção self-service, a VM é implementada novamente para um nó já atualizado. Uma vez que a VM reiniciar, o disco temporário é perdido e endereços IP dinâmicos associados com a interface de rede virtual são atualizados.

Caso seja de um erro durante a manutenção self-service, o pára de operação, a VM não está atualizada e terá a opção para repetir a manutenção self-service. 

Quando termina a fase de self-service, o *fase de manutenção agendada* começa. Durante esta fase, ainda pode consultar para a fase de manutenção, mas não é possível iniciar a manutenção por conta própria.

Para obter mais informações sobre o gerenciamento de manutenção que requer uma reinicialização, consulte [planejado de manipulação de notificações de manutenção para Linux](../articles/virtual-machines/linux/maintenance-notifications.md) ou o artigo correspondente para [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Considerações de disponibilidade durante a manutenção agendada 

Se optar por esperar até a fase de manutenção agendada, existem algumas coisas que deve considerar para manter a maior disponibilidade das suas VMs. 

#### <a name="paired-regions"></a>Regiões emparelhadas

Cada região do Azure é emparelhada com outra região dentro da mesma região geográfica. Juntos, eles tornam a um par de região. Durante a fase de manutenção agendada, o Azure atualiza apenas as VMs numa única região de um par de região. Por exemplo, ao atualizar a VM nos e.u.a. Centro-Norte, o Azure não atualiza qualquer VM no centro-sul ao mesmo tempo. No entanto, outras regiões, como a Europa do Norte, podem estar sob manutenção em simultâneo como os E.U.A. Leste. Noções básicas sobre como funcionam os pares de região pode ajudá-lo melhor distribuir as VMs em várias regiões. Para obter mais informações, consulte [pares de região do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Conjuntos de disponibilidade e conjuntos de dimensionamento

Ao implementar uma carga de trabalho em VMs do Azure, pode criar as VMs dentro de um *conjunto de disponibilidade* para proporcionar elevada disponibilidade para a sua aplicação. Utilizar conjuntos de disponibilidade, é possível garantir que durante eventos de uma falha ou manutenção que requerem um reinício, pelo menos uma VM está disponível.

Dentro de um conjunto de disponibilidade, VMs individuais são distribuídas por até 20 domínios de atualização (UDs). Durante a manutenção agendada, apenas um UD é atualizada em qualquer momento. Domínios de atualização não são necessariamente ser atualizados sequencialmente. 

Máquina virtual *conjuntos de dimensionamento* são o recurso que pode utilizar para implementar e gerir um conjunto de VMs idênticas como um único recurso de computação do Azure. O conjunto de dimensionamento é implementado automaticamente entre domínios de atualização, como VMs num conjunto de disponibilidade. Como com conjuntos de disponibilidade, ao utilizar conjuntos de dimensionamento, apenas um UD é atualizada em qualquer momento durante a manutenção agendada.

Para obter mais informações sobre como configurar as VMs de elevada disponibilidade, consulte [gerir a disponibilidade das suas VMs para Windows](../articles/virtual-machines/windows/manage-availability.md) ou o artigo correspondente para [Linux](../articles/virtual-machines/linux/manage-availability.md).

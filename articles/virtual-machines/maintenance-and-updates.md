---
title: Manutenção e atualizações para VMs no Azure | Microsoft Docs
description: Visão geral de manutenção e atualizações para máquinas virtuais em execução no Azure.
services: virtual-machines
documentationcenter: ''
author: shants123
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/18/2019
ms.author: shants
ms.openlocfilehash: 8e522b49c53dd40a4438755520481383984c1e01
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535889"
---
# <a name="maintenance-for-virtual-machines-in-azure"></a>Manutenção para máquinas virtuais no Azure

O Azure atualiza periodicamente sua plataforma para melhorar a confiabilidade, o desempenho e a segurança da infraestrutura de host para máquinas virtuais. O objetivo destas atualizações vai da correção de componentes de software no ambiente anfitrião à atualização de componentes de rede ou desativação de hardware. 

As atualizações raramente afetam as VMs hospedadas. Quando as atualizações têm um efeito, o Azure escolhe o método de impacto mínimo para atualizações:

- Se a atualização não exigir uma reinicialização, a VM será pausada enquanto o host for atualizado ou a VM será migrada ao vivo para um host já atualizado. 
- Se a manutenção exigir uma reinicialização, você será notificado sobre a manutenção planejada. O Azure também fornece uma janela de tempo na qual você pode iniciar a manutenção por conta própria, ao mesmo tempo que funciona para você. A janela de automanutenção normalmente é de 30 dias, a menos que a manutenção seja urgente. O Azure está investindo em tecnologias para reduzir o número de casos em que a manutenção de plataforma planejada exige que as VMs sejam reinicializadas. Para obter instruções sobre como gerenciar a manutenção planejada, consulte tratamento de notificações de manutenção planejada usando a [CLI](maintenance-notifications-cli.md)do Azure, o [PowerShell](maintenance-notifications-powershell.md) ou o [portal](maintenance-notifications-portal.md).

Esta página descreve como o Azure executa os dois tipos de manutenção. Para obter mais informações sobre eventos não planejados (interrupções), consulte [gerenciar a disponibilidade de VMs para Windows](./windows/manage-availability.md) ou o artigo correspondente para [Linux](./linux/manage-availability.md).

Em uma VM, você pode obter notificações sobre a manutenção futura [usando eventos agendados para Windows](/windows/scheduled-events.md) ou para [Linux](/linux/scheduled-events.md).



## <a name="maintenance-that-doesnt-require-a-reboot"></a>Manutenção que não exige uma reinicialização

A maioria das atualizações de plataforma não afeta as VMs do cliente. Quando uma atualização sem impacto não é possível, o Azure escolhe o mecanismo de atualização que é menos impactante para as VMs dos clientes. 

A manutenção de impacto mais diferente de zero pausa a VM por menos de 10 segundos. Em determinados casos, o Azure usa mecanismos de manutenção de preservação de memória. Esses mecanismos pausam a VM por até 30 segundos e preservam a memória na RAM. Em seguida, a VM é retomada e seu relógio é sincronizado automaticamente. 

A manutenção de preservação de memória funciona por mais de 90% das VMs do Azure. Ele não funciona para as séries G, M, N e H. O Azure usa cada vez mais as tecnologias de migração ao vivo e aprimora os mecanismos de manutenção de preservação de memória para reduzir as durações de pausa.  

Essas operações de manutenção que não exigem uma reinicialização são aplicadas um domínio de falha por vez. Eles serão interrompidos se receberem sinais de integridade de aviso. 

Esses tipos de atualizações podem afetar alguns aplicativos. Quando a VM é migrada ao vivo para um host diferente, algumas cargas de trabalho confidenciais podem mostrar uma pequena degradação de desempenho em alguns minutos, levando a uma pausa na VM. Para se preparar para a manutenção da VM e reduzir o impacto durante a manutenção do Azure, tente [usar eventos agendados para Windows](/windows/scheduled-events.md) ou [Linux](/linux/scheduled-events.md) para tais aplicativos. 

Há também um recurso, controle de manutenção, em visualização pública que pode ajudar a gerenciar a manutenção que não exige uma reinicialização. Você deve estar usando [hosts dedicados do Azure](./linux/dedicated-hosts.md) ou uma [VM isolada](../security/fundamentals/isolation-choices.md). O controle de manutenção oferece a opção de ignorar as atualizações de plataforma e aplicar as atualizações em sua escolha de tempo dentro de uma janela sem interrupção de 35 dias. Para obter mais informações, consulte [controlar atualizações com o controle de manutenção e o CLI do Azure](maintenance-control-cli.md).


### <a name="live-migration"></a>Migração ao vivo

A migração ao vivo é uma operação que não exige uma reinicialização e preserva a memória da VM. Isso causa uma pausa ou congelamento, normalmente com duração de mais de 5 segundos. Exceto para as séries G, M, N e H, todas as VMs de IaaS (infraestrutura como serviço) são elegíveis para a migração ao vivo. As VMs qualificadas representam mais de 90% das VMs IaaS implantadas no Azure frota. 

A plataforma Azure inicia a migração dinâmica nos seguintes cenários:
- Manutenção planeada
- Falha de hardware
- Otimizações de alocação

Alguns cenários de manutenção planejada usam a migração dinâmica e você pode usar Eventos Agendados para saber com antecedência quando as operações de migração ao vivo serão iniciadas.

A migração ao vivo também pode ser usada para mover VMs quando os algoritmos de Azure Machine Learning preveem uma falha iminente de hardware ou quando você deseja otimizar as alocações de VM. Para obter mais informações sobre modelagem preditiva que detecta instâncias de hardware degradado, consulte [melhorando a resiliência de VM do Azure com o aprendizado de máquina preditiva e migração dinâmica](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure). As notificações de migração ao vivo aparecem na portal do Azure nos logs de monitoramento e de integridade do serviço, bem como em Eventos Agendados se você usar esses serviços.

## <a name="maintenance-that-requires-a-reboot"></a>Manutenção que requer uma reinicialização

No caso raro em que as VMs precisam ser reinicializadas para manutenção planejada, você será notificado com antecedência. A manutenção planejada tem duas fases: a fase de autoatendimento e uma fase de manutenção agendada.

Durante a *fase de autoatendimento*, que geralmente dura quatro semanas, você inicia a manutenção em suas VMs. Como parte do autoatendimento, você pode consultar cada VM para ver seu status e o resultado da última solicitação de manutenção.

Quando você inicia a manutenção de autoatendimento, sua VM é reimplantada em um nó já atualizado. Como a VM é reiniciada, o disco temporário é perdido e os endereços IP dinâmicos associados à interface de rede virtual são atualizados.

Se ocorrer um erro durante a manutenção de autoatendimento, a operação será interrompida, a VM não será atualizada e você terá a opção de tentar novamente a manutenção de autoatendimento. 

Quando a fase de autoatendimento termina, a *fase de manutenção agendada* começa. Durante essa fase, você ainda pode consultar a fase de manutenção, mas não pode iniciar a manutenção por conta própria.

Para obter mais informações sobre como gerenciar a manutenção que exige uma reinicialização, consulte **lidando com notificações de manutenção planejada** usando a [CLI](maintenance-notifications-cli.md)do Azure, o [PowerShell](maintenance-notifications-powershell.md) ou o [portal](maintenance-notifications-portal.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Considerações de disponibilidade durante a manutenção agendada 

Se você decidir esperar até a fase de manutenção agendada, há algumas coisas que você deve considerar para manter a maior disponibilidade de suas VMs. 

#### <a name="paired-regions"></a>Regiões emparelhadas

Cada região do Azure é emparelhada com outra região dentro das mesmas proximidades geográficas. Juntos, eles fazem um par de regiões. Durante a fase de manutenção agendada, o Azure atualiza somente as VMs em uma única região de um par de regiões. Por exemplo, ao atualizar a VM no norte EUA Central, o Azure não atualiza nenhuma VM no Sul EUA Central ao mesmo tempo. No entanto, outras regiões, como a Europa do Norte, podem estar sob manutenção em simultâneo como os E.U.A. Leste. Entender como os pares de região funcionam pode ajudá-lo a distribuir melhor suas VMs entre regiões. Para obter mais informações, consulte [pares de regiões do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Conjuntos de disponibilidade e conjuntos de dimensionamento

Ao implantar uma carga de trabalho em VMs do Azure, você pode criar as VMs em um *conjunto de disponibilidade* para fornecer alta disponibilidade ao seu aplicativo. Usando conjuntos de disponibilidade, você pode garantir que, durante uma interrupção ou eventos de manutenção que exijam uma reinicialização, pelo menos uma VM esteja disponível.

Em um conjunto de disponibilidade, as VMs individuais são distribuídas em até 20 domínios de atualização. Durante a manutenção agendada, apenas um domínio de atualização é atualizado em um determinado momento. Os domínios de atualização não são necessariamente atualizados em sequência. 

Os *conjuntos de dimensionamento* de máquinas virtuais são um recurso de computação do Azure que você pode usar para implantar e gerenciar um conjunto de VMs idênticas como um único recurso. O conjunto de dimensionamento é implantado automaticamente em UDs, como VMs em um conjunto de disponibilidade. Assim como acontece com os conjuntos de disponibilidade, quando você usa conjuntos de dimensionamento, apenas um UD é atualizado em um determinado momento durante a manutenção agendada.

Para obter mais informações sobre como configurar suas VMs para alta disponibilidade, consulte [gerenciar a disponibilidade de suas VMs para Windows](./windows/manage-availability.md) ou o artigo correspondente para [Linux](./linux/manage-availability.md).

#### <a name="availability-zones"></a>Zonas de disponibilidade

As zonas de disponibilidade são locais físicos exclusivos em uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas. 

Uma zona de disponibilidade é uma combinação de um domínio de falha e um domínio de atualização. Se você criar três ou mais VMs em três zonas em uma região do Azure, suas VMs serão efetivamente distribuídas entre três domínios de falha e três domínios de atualização. A plataforma Azure reconhece essa distribuição entre domínios de atualização para garantir que as VMs em diferentes zonas não sejam atualizadas ao mesmo tempo.

Cada atualização de infraestrutura distribui a zona por zona, dentro de uma única região. No entanto, você pode fazer com que a implantação seja ativada em Zona 1, e a implantação diferente entrará em Zona 2, ao mesmo tempo. Implantações não são todas serializadas. Mas, uma única implantação apenas distribui uma zona por vez para reduzir o risco.

## <a name="next-steps"></a>Passos seguintes 

Você pode usar o [CLI do Azure](maintenance-notifications-cli.md), [Azure PowerShell](maintenance-notifications-powershell.md) ou o [portal](maintenance-notifications-portal.md) para gerenciar a manutenção planejada. 
---
title: Manutenção e atualizações
description: Visão geral da manutenção e atualizações para máquinas virtuais em funcionamento em Azure.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/18/2019
ms.author: shants
ms.openlocfilehash: eaf7616b3bd69828829342b4dca9247c009d3475
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121858"
---
# <a name="maintenance-for-virtual-machines-in-azure"></a>Manutenção para máquinas virtuais em Azure

O Azure atualiza periodicamente a sua plataforma para melhorar a fiabilidade, desempenho e segurança da infraestrutura hospedeira para máquinas virtuais. O objetivo destas atualizações vai da correção de componentes de software no ambiente anfitrião à atualização de componentes de rede ou desativação de hardware. 

As atualizações raramente afetam os VMs hospedados. Quando as atualizações têm um efeito, o Azure escolhe o método menos impactante para atualizações:

- Se a atualização não necessitar de um reboot, o VM é interrompido enquanto o anfitrião é atualizado, ou o VM é migrado em direto para um hospedeiro já atualizado. 
- Se a manutenção necessitar de um reboot, é notificado da manutenção planeada. O Azure também fornece uma janela de tempo na qual você pode iniciar a manutenção por si mesmo, numa hora que funciona para si. A janela de auto-manutenção é normalmente de 30 dias, a menos que a manutenção seja urgente. O Azure está a investir em tecnologias para reduzir o número de casos em que a manutenção planeada da plataforma exige que os VMs sejam reiniciados. Para obter instruções sobre a gestão da manutenção planeada, consulte o manuseamento de notificações de manutenção planeadas utilizando o Azure [CLI,](maintenance-notifications-cli.md) [PowerShell](maintenance-notifications-powershell.md) ou [portal](maintenance-notifications-portal.md).

Esta página descreve como o Azure executa ambos os tipos de manutenção. Para obter mais informações sobre eventos não planeados (interrupções), consulte [Gerir a disponibilidade de VMs para Windows](./windows/manage-availability.md) ou o artigo correspondente para [linux](./linux/manage-availability.md).

Dentro de um VM, pode receber notificações sobre a manutenção futura [utilizando eventos agendados para Windows](./windows/scheduled-events.md) ou para [Linux](./linux/scheduled-events.md).



## <a name="maintenance-that-doesnt-require-a-reboot"></a>Manutenção que não requer um reboot

A maioria das atualizações da plataforma não afeta os VMs dos clientes. Quando uma atualização sem impacto não é possível, o Azure escolhe o mecanismo de atualização que é menos impactante para os VMs dos clientes. 

A maioria da manutenção de impacto não zero interrompe o VM por menos de 10 segundos. Em certos casos, o Azure utiliza mecanismos de manutenção que preservam a memória. Estes mecanismos pausam o VM durante 30 segundos e preservam a memória na RAM. O VM é então retomado e o seu relógio é automaticamente sincronizado. 

A manutenção de conservação de memória funciona para mais de 90% dos VMs Azure. Não funciona para as séries G, M, N e H. O Azure utiliza cada vez mais tecnologias de migração ao vivo e melhora os mecanismos de manutenção de conservação da memória para reduzir as durações da pausa.  

Estas operações de manutenção que não requerem um reboot são aplicadas um domínio de falha de cada vez. Param se receberem sinais de saúde. 

Este tipo de atualizações pode afetar algumas aplicações. Quando o VM é migrado ao vivo para um hospedeiro diferente, algumas cargas de trabalho sensíveis podem mostrar uma ligeira degradação de desempenho nos poucos minutos que antecedem a pausa vm. Para se preparar para a manutenção vm e reduzir o impacto durante a manutenção do Azure, tente [utilizar eventos agendados para Windows](./windows/scheduled-events.md) ou [Linux](./linux/scheduled-events.md) para tais aplicações. 

Existe também uma funcionalidade, controlo de manutenção, na pré-visualização pública que pode ajudar a gerir a manutenção que não requer um reboot. Deve utilizar [anfitriões dedicados azure](./linux/dedicated-hosts.md) ou um [VM isolado.](../security/fundamentals/isolation-choices.md) O controlo de manutenção dá-lhe a opção de ignorar as atualizações da plataforma e aplicar as atualizações à sua escolha de tempo dentro de uma janela de rolamento de 35 dias. Para mais informações, consulte as atualizações de Controlo com controlo de [manutenção e o Azure CLI](maintenance-control-cli.md).


### <a name="live-migration"></a>Migração ao vivo

A migração ao vivo é uma operação que não requer um reboot e que preserva a memória para o VM. Causa uma pausa ou congelamento, normalmente não dura mais de 5 segundos. Com exceção das séries G, M, N e H, todas as infraestruturas como VMs de serviço (IaaS), são elegíveis para migração ao vivo. Os VMelegíveis representam mais de 90% dos VMs IaaS que são implantados na frota Azure. 

A plataforma Azure inicia a migração ao vivo nos seguintes cenários:
- Manutenção planeada
- Falha de hardware
- Otimizações de atribuição

Alguns cenários de manutenção planeada usam migração ao vivo, e você pode usar Eventos Agendados para saber antecipadamente quando as operações de migração ao vivo começarão.

A migração ao vivo também pode ser usada para mover VMs quando os algoritmos de Aprendizagem automática do Azure preveem uma falha de hardware iminente ou quando você quer otimizar as alocações vm. Para obter mais informações sobre modelação preditiva que detete casos de hardware degradado, consulte [a resiliência do Azure VM com aprendizagem automática preditiva e migração ao vivo.](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure) As notificações de migração ao vivo aparecem no portal Azure nos registos monitor e de saúde de serviço, bem como em Eventos Agendados se utilizar estes serviços.

## <a name="maintenance-that-requires-a-reboot"></a>Manutenção que requer um reboot

No caso raro em que os VMs precisam de ser reiniciados para manutenção planeada, será notificado com antecedência. A manutenção prevista tem duas fases: a fase de autosserviço e uma fase de manutenção programada.

Durante a *fase de self-service*, que normalmente dura quatro semanas, inicia a manutenção dos seus VMs. Como parte do self-service, você pode consultar cada VM para ver o seu estado e o resultado do seu último pedido de manutenção.

Quando iniciar a manutenção do self-service, o seu VM é reimplantado num nó já atualizado. Como o VM reinicia, o disco temporário perde-se e os endereços IP dinâmicos associados à interface de rede virtual são atualizados.

Se surgir um erro durante a manutenção do self-service, a operação para, o VM não é atualizado e obtém a opção de voltar a tentar a manutenção do self-service. 

Quando a fase de autosserviço termina, inicia-se a *fase de manutenção programada.* Durante esta fase, ainda pode consultar a fase de manutenção, mas não pode iniciar a manutenção por si mesmo.

Para obter mais informações sobre a gestão da manutenção que exija um reboot, consulte o Manuseamento de notificações de **manutenção planeadas** utilizando o Azure [CLI,](maintenance-notifications-cli.md) [PowerShell](maintenance-notifications-powershell.md) ou [portal](maintenance-notifications-portal.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Considerações de disponibilidade durante a manutenção programada 

Se decidir esperar até à fase de manutenção programada, há algumas coisas que deve considerar para manter a maior disponibilidade dos seus VMs. 

#### <a name="paired-regions"></a>Regiões emparelhadas

Cada região de Azure é emparelhada com outra região dentro das mesmas proximidades geográficas. Juntos, fazem um par de regiões. Durante a fase de manutenção programada, o Azure atualiza apenas os VMs numa única região de um par de região. Por exemplo, ao atualizar o VM no Centro-Norte dos EUA, o Azure não atualiza nenhum VM no Centro-Sul dos EUA ao mesmo tempo. No entanto, outras regiões, como a Europa do Norte, podem estar sob manutenção em simultâneo como os E.U.A. Leste. Compreender como os pares de região funcionam pode ajudá-lo a distribuir melhor os seus VMs por regiões. Para mais informações, consulte os pares da região de [Azure.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)

#### <a name="availability-sets-and-scale-sets"></a>Conjuntos de disponibilidade e conjuntos de escala

Ao implementar uma carga de trabalho em VMs Azure, pode criar os VMs dentro de um *conjunto de disponibilidade* para fornecer alta disponibilidade à sua aplicação. Utilizando conjuntos de disponibilidade, pode garantir que durante uma paragem ou eventos de manutenção que exijam um reboot, pelo menos um VM está disponível.

Dentro de um conjunto de disponibilidade, os VMs individuais estão espalhados por até 20 domínios de atualização. Durante a manutenção programada, apenas um domínio de atualização é atualizado a qualquer momento. Os domínios de atualização não são necessariamente atualizados sequencialmente. 

Os *conjuntos* de escala de máquina virtual são um recurso de computação Azure que pode usar para implantar e gerir um conjunto de VMs idênticos como um único recurso. O conjunto de escala é automaticamente implantado em UDs, como VMs em um conjunto de disponibilidade. Tal como acontece com os conjuntos de disponibilidade, quando utiliza conjuntos de escala, apenas um UD é atualizado a qualquer momento durante a manutenção programada.

Para obter mais informações sobre a configuração dos seus VMs para uma elevada disponibilidade, consulte [Gerir a disponibilidade dos seus VMs para Windows](./windows/manage-availability.md) ou o artigo correspondente para o [Linux](./linux/manage-availability.md).

#### <a name="availability-zones"></a>Zonas de disponibilidade

As zonas de disponibilidade são locais físicos únicos dentro de uma região de Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas. 

Uma zona de disponibilidade é uma combinação de um domínio de falha e um domínio de atualização. Se criar três ou mais VMs em três zonas de uma região do Azure, os seus VMs são efetivamente distribuídos por três domínios de falha e três domínios de atualização. A plataforma Azure reconhece esta distribuição através de domínios de atualização para garantir que os VMs em diferentes zonas não são atualizados ao mesmo tempo.

Cada atualização de infraestrutura sai zona por zona, dentro de uma única região. Mas, pode ter implantação em curso na Zona 1, e diferente implantação na Zona 2, ao mesmo tempo. As missões não são todas serializadas. Mas, uma única implantação só sai de uma zona de cada vez para reduzir o risco.

## <a name="next-steps"></a>Passos seguintes 

Pode utilizar o [Azure CLI,](maintenance-notifications-cli.md) [O Azure PowerShell](maintenance-notifications-powershell.md) ou o [portal](maintenance-notifications-portal.md) para gerir a manutenção planeada. 
---
title: Manutenção e atualizações
description: Visão geral da manutenção e atualizações para máquinas virtuais em execução em Azure.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/22/2020
ms.author: shants
ms.openlocfilehash: 3cf126caaaa0c518574418aca194ebd82cc4d6b9
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91972072"
---
# <a name="maintenance-for-virtual-machines-in-azure"></a>Manutenção de máquinas virtuais no Azure

O Azure atualiza periodicamente a sua plataforma para melhorar a fiabilidade, desempenho e segurança da infraestrutura de acolhimento para máquinas virtuais. O objetivo destas atualizações vai da correção de componentes de software no ambiente anfitrião à atualização de componentes de rede ou desativação de hardware. 

As atualizações raramente afetam os VMs hospedados. Quando as atualizações têm um efeito, o Azure escolhe o método menos impactante para atualizações:

- Se a atualização não necessitar de um reboot, o VM é interrompido enquanto o hospedeiro é atualizado, ou o VM é migrado ao vivo para um hospedeiro já atualizado. 
- Se a manutenção necessitar de um reboot, é notificado da manutenção planeada. O Azure também fornece uma janela de tempo na qual você pode iniciar a manutenção por si mesmo, numa altura que funciona para si. A janela de auto-manutenção é normalmente de 35 dias, a menos que a manutenção seja urgente. O Azure está a investir em tecnologias para reduzir o número de casos em que a manutenção planeada da plataforma exige que os VM sejam reiniciados. Para obter instruções sobre a gestão da manutenção planeada, consulte as notificações de manutenção planeadas utilizando o Azure [CLI,](maintenance-notifications-cli.md) [PowerShell](maintenance-notifications-powershell.md) ou [portal](maintenance-notifications-portal.md).

Esta página descreve como a Azure executa ambos os tipos de manutenção. Para obter mais informações sobre eventos não planeados (interrupções), consulte [Gerir a disponibilidade de VMs para Windows](./manage-availability.md) ou o artigo correspondente para o [Linux.](./manage-availability.md)

Dentro de um VM, você pode obter notificações sobre a próxima manutenção [usando Eventos Agendados para Windows](./windows/scheduled-events.md) ou para [Linux](./linux/scheduled-events.md).



## <a name="maintenance-that-doesnt-require-a-reboot"></a>Manutenção que não requer um reboot

A maioria das atualizações da plataforma não afetam os VMs dos clientes. Quando uma atualização sem impacto não é possível, o Azure escolhe o mecanismo de atualização que é menos impactante para os VM do cliente. 

A maior parte da manutenção não-impacto faz uma pausa no VM por menos de 10 segundos. Em certos casos, a Azure utiliza mecanismos de manutenção de preservação da memória. Estes mecanismos fazem uma pausa no VM durante 30 segundos e preservam a memória na RAM. O VM é então retomado e o seu relógio é automaticamente sincronizado. 

A manutenção da conservação da memória funciona para mais de 90% dos VMs Azure. Não funciona para as séries G, M, N e H. O Azure utiliza cada vez mais tecnologias de migração ao vivo e melhora os mecanismos de manutenção da memória para reduzir as durações da pausa.  

Estas operações de manutenção que não requerem um reboot são aplicadas um domínio de falha de cada vez. Param se receberem sinais de saúde de aviso das ferramentas de monitorização da plataforma. 

Este tipo de atualizações pode afetar algumas aplicações. Quando o VM é migrado ao vivo para um hospedeiro diferente, algumas cargas de trabalho sensíveis podem mostrar uma ligeira degradação do desempenho nos poucos minutos que antecedem a pausa VM. Para preparar a manutenção de VM e reduzir o impacto durante a manutenção do Azure, tente [utilizar Eventos Programados para Windows](./windows/scheduled-events.md) ou [Linux](./linux/scheduled-events.md) para tais aplicações. 

Para um maior controlo em todas as atividades de manutenção, incluindo atualizações de impacto zero e rebootless, pode utilizar a função de Controlo de Manutenção. Deve utilizar anfitriões [dedicados Azure](./dedicated-hosts.md) ou um [VM isolado.](../security/fundamentals/isolation-choices.md) O controlo de manutenção dá-lhe a opção de ignorar todas as atualizações da plataforma e aplicar as atualizações à sua escolha dentro de uma janela de 35 dias. Para obter mais informações, consulte [atualizações de Controlo com o Controlo de Manutenção e o CLI Azure](maintenance-control.md).


### <a name="live-migration"></a>Migração em direto

A migração ao vivo é uma operação que não requer um reboot e que preserva a memória para o VM. Provoca uma pausa ou congelamento, que normalmente não dura mais do que 5 segundos. Com exceção das séries G, M, N e H, todas as infraestruturas como VMs de serviço (IaaS), são elegíveis para migração ao vivo. Os VM elegíveis representam mais de 90% dos VMs da IaaS que são implantados na frota Azure. 

A plataforma Azure inicia a migração ao vivo nos seguintes cenários:
- Manutenção planeada
- Falha de hardware
- Otimizações de atribuição

Alguns cenários de manutenção planeada usam migração ao vivo, e você pode usar Eventos Agendados para saber com antecedência quando as operações de migração ao vivo vão começar.

A migração ao vivo também pode ser usada para mover VMs quando os algoritmos de Aprendizagem automática Azure prevêem uma falha de hardware iminente ou quando você quer otimizar alocações de VM. Para obter mais informações sobre modelação preditiva que detete casos de hardware degradado, consulte Melhorar a resiliência do VM do [Azure com aprendizagem automática preditiva e migração ao vivo.](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure) As notificações de migração em direto aparecem no portal Azure nos registos de Saúde monitor e de serviço, bem como em Eventos Agendados se utilizar estes serviços.

## <a name="maintenance-that-requires-a-reboot"></a>Manutenção que requer um reboot

No caso raro em que os VMs precisam de ser reiniciados para manutenção planeada, será notificado com antecedência. A manutenção planeada tem duas fases: a fase de self-service e uma fase de manutenção programada.

Durante a *fase de autosserviço,* que normalmente dura quatro semanas, inicia-se a manutenção dos seus VMs. Como parte do self-service, pode consultar cada VM para ver o seu estado e o resultado do seu último pedido de manutenção.

Quando inicia a manutenção de autosserviço, o seu VM é reenviado para um nó já atualizado. Como o VM reinicia, o disco temporário perde-se e os endereços IP dinâmicos associados à interface de rede virtual são atualizados.

Se ocorrer um erro durante a manutenção do autosserviço, a operação para, o VM não é atualizado e obtém-se a opção de voltar a tentar a manutenção do autosserviço. 

Quando a fase de autosserviço terminar, inicia-se a *fase de manutenção programada.* Durante esta fase, ainda pode consultar a fase de manutenção, mas não pode iniciar a manutenção sozinho.

Para obter mais informações sobre a gestão da manutenção que exija um reboot, consulte **handling as notificações de manutenção planeadas** utilizando o Azure [CLI,](maintenance-notifications-cli.md) [PowerShell](maintenance-notifications-powershell.md) ou [portal](maintenance-notifications-portal.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Considerações de disponibilidade durante a manutenção programada 

Se decidir esperar pela fase de manutenção programada, há algumas coisas que deve considerar para manter a maior disponibilidade dos seus VM. 

#### <a name="paired-regions"></a>Regiões emparelhadas

Cada região de Azure é emparelhada com outra região nas mesmas proximidades geográficas. Juntos, fazem um par de regiões. Durante a fase de manutenção programada, o Azure atualiza apenas os VMs numa única região de um par de regiões. Por exemplo, ao atualizar o VM nos EUA, o Azure não atualiza nenhum VM nos EUA do Centro Sul ao mesmo tempo. No entanto, outras regiões, como a Europa do Norte, podem estar sob manutenção em simultâneo como os E.U.A. Leste. Compreender como os pares de regiões funcionam pode ajudá-lo a distribuir melhor os seus VMs por regiões. Para mais informações, consulte [os pares da região de Azure.](../best-practices-availability-paired-regions.md)

#### <a name="availability-sets-and-scale-sets"></a>Conjuntos de disponibilidade e conjuntos de escala

Ao implementar uma carga de trabalho em VMs Azure, pode criar os VMs dentro de um *conjunto de disponibilidade* para fornecer alta disponibilidade à sua aplicação. Utilizando conjuntos de disponibilidade, pode garantir que durante uma paragem ou eventos de manutenção que exijam um reboot, pelo menos um VM está disponível.

Dentro de um conjunto de disponibilidade, os VM individuais são distribuídos por até 20 domínios de atualização. Durante a manutenção programada, apenas um domínio de atualização é atualizado a qualquer momento. Os domínios de atualização não são necessariamente atualizados sequencialmente. 

Os *conjuntos* de escala de máquina virtual são um recurso de computação Azure que pode usar para implementar e gerir um conjunto de VMs idênticos como um único recurso. O conjunto de escala é automaticamente implantado em UDs, como VMs em um conjunto de disponibilidade. Tal como acontece com os conjuntos de disponibilidade, quando utiliza conjuntos de escala, apenas um UD é atualizado a qualquer momento durante a manutenção programada.

Para obter mais informações sobre a configuração dos seus VMs para uma elevada disponibilidade, consulte [Gerir a disponibilidade dos seus VMs para Windows](./manage-availability.md) ou o artigo correspondente para o [Linux.](./manage-availability.md)

#### <a name="availability-zones"></a>Zonas de disponibilidade

As zonas de disponibilidade são localizações físicas únicas dentro de uma região de Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões ativadas. 

Uma zona de disponibilidade é uma combinação de um domínio de falha e um domínio de atualização. Se criar três ou mais VMs em três zonas numa região de Azure, os seus VMs são efetivamente distribuídos por três domínios de avaria e três domínios de atualização. A plataforma Azure reconhece esta distribuição através de domínios de atualização para garantir que os VMs em diferentes zonas não sejam atualizados ao mesmo tempo.

Cada atualização de infraestrutura sai de zona por zona, dentro de uma única região. Mas, você pode ter a implantação em curso na Zona 1, e diferente implementação indo na Zona 2, ao mesmo tempo. As implementações não são todas serializadas. Mas, uma única implantação só sai uma zona de cada vez para reduzir o risco.

## <a name="next-steps"></a>Passos seguintes 

Pode utilizar o [Azure CLI,](maintenance-notifications-cli.md) [a Azure PowerShell](maintenance-notifications-powershell.md) ou o [portal](maintenance-notifications-portal.md) para gerir a manutenção planeada.
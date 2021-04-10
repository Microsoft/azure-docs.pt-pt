---
title: Notificações de manutenção
description: Visão geral das notificações de manutenção para máquinas virtuais em execução em Azure.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 8/12/2020
ms.author: shants
ms.openlocfilehash: 92cb780a80f1010fd1c2f5d19fe616e0285de73b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564531"
---
# <a name="handling-planned-maintenance-notifications"></a>Tratamento de notificações de manutenção planeadas

O Azure realiza periodicamente atualizações para melhorar a fiabilidade, o desempenho e a segurança da infraestrutura de anfitrião para máquinas virtuais. As atualizações são alterações como corrigir o ambiente de hospedagem ou atualizar e desativar hardware. A maioria destas atualizações são concluídas sem qualquer impacto nas máquinas virtuais acolhidas. No entanto, há casos em que as atualizações têm impacto:

- Se a manutenção não necessitar de um reboot, o Azure faz uma pausa no VM durante alguns segundos enquanto o hospedeiro é atualizado. Estes tipos de operações de manutenção são aplicados domínio de avaria por domínio de avaria. Os progressos são interrompidos se forem recebidos sinais de saúde de aviso.

- Se a manutenção necessitar de um reboot, recebe um aviso de quando a manutenção está planeada. É-lhe dada uma janela de tempo de cerca de 35 dias onde pode iniciar a manutenção, quando funcionar para si.


A manutenção planeada que requer um reboot está programada em ondas. Cada onda tem âmbito diferente (regiões).

- Uma onda começa com uma notificação aos clientes. Por predefinição, a notificação é enviada para os administradores de subscrição e administradores. Pode adicionar mais destinatários e opções de mensagens como e-mail, SMS e webhooks, utilizando [Alertas de Registo de Atividades](../service-health/alerts-activity-log-service-notifications-portal.md).  
- Uma vez que uma notificação é feita, uma *janela de autosserviço* é disponibilizada. Durante esta janela, pode consultar quais das suas máquinas virtuais estão afetadas e iniciar a manutenção com base nas suas próprias necessidades de agendamento. A janela de autosserviço é normalmente de cerca de 35 dias.
- Após a janela de autosserviço, começa uma *janela de manutenção programada.* Em algum momento durante esta janela, a Azure programa e aplica a manutenção necessária à sua máquina virtual. 

O objetivo de ter duas janelas é dar-lhe tempo suficiente para iniciar a manutenção e reiniciar a sua máquina virtual enquanto sabe quando a Azure iniciará automaticamente a manutenção.


Pode utilizar o portal Azure, PowerShell, REST API e CLI para consultar as janelas de manutenção para os seus VMs e iniciar a manutenção de autosserviço.

 
## <a name="should-you-start-maintenance-using-during-the-self-service-window"></a>Deve começar a manutenção durante a janela de autosserviço?  

As seguintes diretrizes devem ajudá-lo a decidir se utiliza esta capacidade e iniciar a manutenção no seu próprio tempo. 

> [!NOTE] 
> A manutenção de autosserviço pode não estar disponível para todos os seus VMs. Para determinar se a recolocação proactiva está disponível para o seu VM, procure o **Arranque agora** no estado de manutenção. A manutenção de self-service não está atualmente disponível para serviços cloud (Web/Worker Role) e Service Fabric.


A manutenção de autosserviço não é recomendada para implementações utilizando **conjuntos de disponibilidade**. Os conjuntos de disponibilidade já são atualizados apenas um domínio de atualização de cada vez. 

- Deixe a Azure ativar a manutenção. Para a manutenção que requer o reboot, a manutenção será feita por domínio de atualização. Os domínios de atualização não recebem necessariamente a manutenção sequencialmente, e há uma pausa de 30 minutos entre domínios de atualização. 
- Se uma perda temporária de alguma capacidade (domínio de atualização de 1) for uma preocupação, pode adicionar instâncias durante o período de manutenção. 
- Para uma manutenção que não requer o reinício, as atualizações são aplicadas ao nível do domínio de avaria. 

**Não** utilize a manutenção de autosserviço nos seguintes cenários: 
- Se desligar frequentemente os seus VMs, quer manualmente, utilizando a DevTest Labs, utilizando o desligamento automático, ou seguindo um horário, poderá reverter o estado de manutenção e, portanto, causar tempo de inatividade adicional.
- Em VMs de curta duração que você sabe serão apagados antes do fim da onda de manutenção. 
- Para cargas de trabalho com um grande estado armazenado no disco local (efémero) que é desejado para ser mantido após atualização. 
- Para os casos em que redimensione frequentemente o seu VM, uma vez que pode reverter o estado de manutenção. 
- Se tiver adotado eventos programados que permitam o failover proactivo ou o encerramento gracioso da sua carga de trabalho, 15 minutos antes do início do encerramento da manutenção

**Utilize** a manutenção de autoassistência, se estiver a planear executar o seu VM ininterruptamente durante a fase de manutenção programada e nenhuma das contraindicações acima mencionadas for aplicável. 

É melhor utilizar a manutenção de autosserviço nos seguintes casos:
- Tem de comunicar uma janela de manutenção exata à sua administração ou ao seu cliente final. 
- Tem de completar a manutenção até uma data dada. 
- É necessário controlar a sequência de manutenção, por exemplo, aplicação multi-nível para garantir uma recuperação segura.
- São necessários mais de 30 minutos de tempo de recuperação de VM entre dois domínios de atualização (UDs). Para controlar o tempo entre os domínios de atualização, tem de acionar a manutenção dos seus VMs, um domínio de atualização (UD) de cada vez.


## <a name="faq"></a>FAQ


**P: Por que precisa de reiniciar as minhas máquinas virtuais agora?**

**A:** Embora a maioria das atualizações e atualizações para a plataforma Azure não tenham impacto na disponibilidade da máquina virtual, existem casos em que não podemos evitar reiniciar máquinas virtuais hospedadas no Azure. Acumulámos várias alterações que nos obrigam a reiniciar os nossos servidores que resultarão no reinício de máquinas virtuais.

**P: Se eu seguir as suas recomendações de Alta Disponibilidade utilizando um Conjunto de Disponibilidade, estou seguro?**

**A:** As máquinas virtuais implantadas num conjunto de disponibilidade ou conjuntos de escala de máquinas virtuais têm a noção de Domínios de Atualização (UD). Ao realizar a manutenção, o Azure honra a restrição de UD e não reiniciará máquinas virtuais de diferentes UD (dentro do mesmo conjunto de disponibilidade).  O Azure também espera pelo menos 30 minutos antes de se mudar para o próximo grupo de máquinas virtuais. 

Para obter mais informações sobre a elevada disponibilidade, consulte [Disponibilidade para máquinas virtuais em Azure.](availability.md)

**P: Como sou notificado sobre a manutenção planeada?**

**A:** Uma onda de manutenção planeada começa por definir um horário para uma ou mais regiões de Azure. Pouco tempo depois, é enviada uma notificação por e-mail para os administradores de subscrição, coadministradores, proprietários e contribuintes (um e-mail por subscrição). Canais e destinatários adicionais para esta notificação podem ser configurados usando Alertas de Registo de Atividade. Caso desloque uma máquina virtual para uma região onde a manutenção planeada já esteja programada, não receberá a notificação, mas precisará verificar o estado de manutenção do VM.

**P: Não vejo qualquer indicação de manutenção planeada no portal, PowerShell ou CLI. O que se passa?**

**A:** A informação relacionada com a manutenção planeada está disponível durante uma onda de manutenção planeada apenas para os VMs que vão ser impactados por ela. Por outras palavras, se não vir dados, pode ser que a onda de manutenção já tenha concluído (ou não iniciada) ou que a sua máquina virtual já esteja hospedada num servidor atualizado.

**P: Há alguma maneira de saber exatamente quando a minha máquina virtual será impactada?**

**A:** Ao definir o horário, definimos uma janela de tempo de vários dias. No entanto, desconhece-se a sequência exata dos servidores (e VMs) dentro desta janela. Os clientes que gostariam de saber a hora exata para os seus VMs podem usar [eventos e](./linux/scheduled-events.md) consultas programadas dentro da máquina virtual e receber uma notificação de 15 minutos antes de um reboot de VM.

**P: Quanto tempo vai demorar a reiniciar a minha máquina virtual?**

**A:**  Dependendo do tamanho do seu VM, o reboot pode demorar até alguns minutos durante a janela de manutenção de autosserviço. Durante o Azure iniciado reinicializações na janela de manutenção programada, o reboot normalmente levará cerca de 25 minutos. Note que no caso de utilizar serviços cloud (Função Web/Trabalhador), conjuntos de balanças de máquinas virtuais ou conjuntos de disponibilidade, receberá 30 minutos entre cada grupo de VMs (UD) durante a janela de manutenção programada.

**P: Qual é a experiência no caso dos conjuntos de escala de máquina virtual?**

**A:** A manutenção planeada está agora disponível para conjuntos de escala de máquina virtual. Para obter instruções sobre como iniciar a manutenção de autosserviço, consulte a manutenção planeada para o documento [de conjuntos de calcários virtuais.](../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md)

**P: Qual é a experiência no caso dos Serviços cloud (Web/Papel trabalhador) e tecido de serviço?**

**A:** Embora estas plataformas sejam impactadas pela manutenção planeada, os clientes que utilizam estas plataformas são considerados seguros, dado que apenas os VMs num único Domínio de Upgrade (UD) serão impactados a qualquer momento. A manutenção de self-service não está atualmente disponível para serviços cloud (Web/Worker Role) e Service Fabric.

**P: Não vejo nenhuma informação de manutenção nos meus VMs. O que é que correu mal?**

**A:** Existem várias razões pelas quais não está a ver nenhuma informação de manutenção nos seus VMs:
1.  Está a utilizar uma subscrição marcada como Interna da Microsoft.
2.  Os seus VMs não estão programados para manutenção. Pode ser que a onda de manutenção tenha terminado, cancelada ou modificada para que os seus VMs não sejam mais afetados por ela.
3. Fizeste negócios com a VM e depois começaste. Isto pode fazer com que a VM se mova para um local que não tenha a onda de manutenção planeada programada. Assim, o VM não mostrará mais informações de manutenção. 
4.  Não tem a coluna **manutenção** adicionada à sua visualização da lista VM. Embora tenhamos adicionado esta coluna à vista padrão, os clientes que configuraram para ver colunas não predefinidas devem adicionar manualmente a coluna **manutenção** à sua vista de lista VM.

**P: O meu VM está programado para manutenção pela segunda vez. Porquê?**

**A:** Existem vários casos de utilização em que verá o seu VM agendado para manutenção depois de já ter concluído a sua redistribuição de manutenção:
1.  Cancelamos a onda de manutenção e reiniciámo-la com uma carga diferente. Pode ser que detetemos uma carga útil defeituosa e precisamos simplesmente de implantar uma carga adicional.
2.  O seu VM foi *reparado* em outro nó devido a uma falha de hardware.
3.  Escolheu parar (deallocate) e reiniciar o VM.
4.  Tem **a paragem automática** ligada para o VM.



## <a name="next-steps"></a>Passos seguintes

Pode manusear a manutenção planeada utilizando o [Azure CLI,](maintenance-notifications-cli.md) [Azure PowerShell](maintenance-notifications-powershell.md) ou [portal](maintenance-notifications-portal.md).

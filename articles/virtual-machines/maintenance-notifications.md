---
title: Notificações de manutenção
description: Visão geral das notificações de manutenção para máquinas virtuais em funcionamento em Azure.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 68159577cb31145be5063bb19af6db71ca1727bd
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77115694"
---
# <a name="handling-planned-maintenance-notifications"></a>Manuseamento de notificações de manutenção planeadas

O Azure realiza periodicamente atualizações para melhorar a fiabilidade, o desempenho e a segurança da infraestrutura de anfitrião para máquinas virtuais. As atualizações são alterações como corrigir o ambiente de hospedagem ou atualizar e desativar hardware. A maioria destas atualizações são concluídas sem qualquer impacto nas máquinas virtuais hospedadas. No entanto, há casos em que as atualizações têm impacto:

- Se a manutenção não necessitar de um reboot, o Azure utiliza a migração no local para parar o VM enquanto o hospedeiro é atualizado. Estes tipos de operações de manutenção são aplicados por domínio de falha. Os progressos são interrompidos se forem recebidos sinais de saúde de alerta.

- Se a manutenção necessitar de um reboot, você recebe um aviso de quando a manutenção está planeada. É-lhe dada uma janela temporal de cerca de 35 dias onde pode iniciar a manutenção por si mesmo, quando funciona para si.


A manutenção planeada que requer um reboot está programada em ondas. Cada onda tem um âmbito diferente (regiões).

- Uma onda começa com uma notificação aos clientes. Por predefinição, a notificação é enviada ao Administrador de Serviço e aos Coadministradores. Pode adicionar mais destinatários e opções de mensagens como e-mail, SMS e webhooks, utilizando [alertas](../service-health/alerts-activity-log-service-notifications.md)de registo de atividade .  
- Uma vez que uma notificação sai, uma *janela de self-service* é disponibilizada. Durante esta janela, pode consultar quais das suas máquinas virtuais são afetadas e iniciar a manutenção com base nas suas próprias necessidades de agendamento. A janela de self-service é tipicamente de cerca de 35 dias.
- Depois da janela de self-service, começa uma janela de *manutenção programada.* Em algum momento durante esta janela, o Azure programa e aplica a manutenção necessária à sua máquina virtual. 

O objetivo em ter duas janelas é dar-lhe tempo suficiente para iniciar a manutenção e reiniciar a sua máquina virtual, sabendo quando o Azure iniciará automaticamente a manutenção.


Pode utilizar o portal Azure, PowerShell, REST API e CLI para consultar as janelas de manutenção dos seus VMs e iniciar a manutenção do self-service.

 
## <a name="should-you-start-maintenance-using-during-the-self-service-window"></a>Deve começar a manutenção utilizando durante a janela de self-service?  

As seguintes diretrizes devem ajudá-lo a decidir se utiliza esta capacidade e iniciar a manutenção no seu próprio momento. 

> [!NOTE] 
> A manutenção do self-service pode não estar disponível para todos os seus VMs. Para determinar se a reimplantação proactiva está disponível para o seu VM, procure o **Início agora** no estado de manutenção. A manutenção do self-service não está atualmente disponível para serviços em nuvem (Função Web/Trabalhador) e Tecido de Serviço.


A manutenção do self-service não é recomendada para implementações utilizando **conjuntos**de disponibilidade . Os conjuntos de disponibilidade já são atualizados apenas um domínio de atualização de cada vez. 

- Deixe o Azure acionar a manutenção. Para a manutenção que requer o reboot, a manutenção será feita por domínio de atualização. Os domínios de atualização não recebem necessariamente a manutenção sequencialmente, e que há uma pausa de 30 minutos entre os domínios de atualização. 
- Se uma perda temporária de alguma capacidade (1 domínio de atualização) for uma preocupação, pode adicionar instâncias durante o período de manutenção. 
- Para manutenção que não exija o reboot, as atualizações são aplicadas ao nível do domínio da falha. 

**Não** utilize a manutenção do self-service nos seguintes cenários: 
- Se desligar frequentemente os seus VMs, manualmente, utilizando o DevTest Labs, utilizando a paragem automática ou seguindo um horário, poderá reverter o estado de manutenção e, portanto, causar tempo de paragem adicional.
- Em VMs de curta duração que você sabe que serão eliminados antes do fim da onda de manutenção. 
- Para cargas de trabalho com um grande estado armazenado no disco local (efémero) que se pretende manter após a atualização. 
- Para casos em que redimensiona frequentemente o seu VM, pois pode reverter o estado de manutenção. 
- Se tiver adotado eventos programados que permitam o failover proactivo ou o encerramento gracioso da sua carga de trabalho, 15 minutos antes do início do encerramento da manutenção

**Utilize** a manutenção do self-service, se estiver a planear executar o seu VM ininterruptamente durante a fase de manutenção programada e nenhuma das contraindicações acima mencionadas é aplicável. 

É melhor utilizar a manutenção do self-service nos seguintes casos:
- Precisa comunicar uma janela de manutenção exata à sua gerência ou cliente final. 
- Precisa completar a manutenção até uma data. 
- É necessário controlar a sequência de manutenção, por exemplo, aplicação de vários níveis para garantir uma recuperação segura.
- São necessários mais de 30 minutos de tempo de recuperação vm entre dois domínios de atualização (UDs). Para controlar o tempo entre os domínios da atualização, deve acionar a manutenção dos seus VMs num domínio de atualização (UD) de cada vez.


## <a name="faq"></a>FAQ


**P: Por que precisa reiniciar as minhas máquinas virtuais agora?**

**A:** Embora a maioria das atualizações e atualizações para a plataforma Azure não afetem a disponibilidade da máquina virtual, existem casos em que não podemos evitar reiniciar máquinas virtuais hospedadas no Azure. Acumulámos várias alterações que nos obrigam a reiniciar os nossos servidores que resultarão no reboot de máquinas virtuais.

**P: Se eu seguir as suas recomendações para alta disponibilidade usando um Conjunto de Disponibilidade, estou seguro?**

**A:** As máquinas virtuais implantadas num conjunto de disponibilidade ou conjuntos de escala de máquinavirtual têm a noção de Domínios de Atualização (UD). Ao realizar a manutenção, o Azure honra a restrição de UD e não reiniciará máquinas virtuais de diferentes UD (dentro do mesmo conjunto de disponibilidade).  O Azure também aguarda pelo menos 30 minutos antes de se mudar para o próximo grupo de máquinas virtuais. 

Para mais informações sobre a elevada disponibilidade, consulte [Disponibilidade para máquinas virtuais em Azure](./linux/availability.md).

**P: Como posso ser notificado sobre a manutenção planeada?**

**A:** Uma onda de manutenção planeada começa por definir um horário para uma ou mais regiões azure. Logo após, uma notificação por email é enviada para os administradores de assinatura (um email por assinatura). Canais e destinatários adicionais para esta notificação poderiam ser configurados usando Alertas de Registo de Atividade. Caso desloque uma máquina virtual para uma região onde a manutenção planeada já está programada, não receberá a notificação, mas terá de verificar o estado de manutenção do VM.

**P: Não vejo qualquer indicação de manutenção planeada no portal, Powershell ou CLI. O que se passa?**

**A:** As informações relacionadas com a manutenção planeada estão disponíveis durante uma onda de manutenção planeada apenas para os VMs que serão impactados por ela. Por outras palavras, se não vir dados, pode ser que a onda de manutenção já tenha terminado (ou não tenha começado) ou que a sua máquina virtual já esteja hospedada num servidor atualizado.

**P: Existe uma maneira de saber exatamente quando a minha máquina virtual será impactada?**

**A:** Ao definir o horário, definimos uma janela de tempo de vários dias. No entanto, a sequência exata de servidores (e VMs) dentro desta janela é desconhecida. Os clientes que gostariam de saber a hora exata para os seus VMs podem usar [eventos e](./linux/scheduled-events.md) consulta programados a partir de dentro da máquina virtual e receber uma notificação de 15 minutos antes de um reboot vm.

**P: Quanto tempo vai demorar a reiniciar a minha máquina virtual?**

**A:**  Dependendo do tamanho do seu VM, o reboot pode demorar até vários minutos durante a janela de manutenção do self-service. Durante o Azure iniciado reboots na janela de manutenção programada, o reboot normalmente levará cerca de 25 minutos. Note que no caso de utilizar serviços de cloud (Função Web/Trabalhador), conjuntos de escala de máquina virtual ou conjuntos de disponibilidade, será dado 30 minutos entre cada grupo de VMs (UD) durante a janela de manutenção programada.

**P: Qual é a experiência no caso dos conjuntos de escala de máquinas virtuais?**

**A:** A manutenção planeada está agora disponível para conjuntos de escala de máquina virtual. Para obter instruções sobre como iniciar a manutenção do self-service, consulte a manutenção planeada para o documento de conjuntos de escala de [máquinas virtuais.](../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md)

**P: Qual é a experiência no caso dos Serviços de Nuvem (Função Web/Trabalhador) e do Tecido de Serviço?**

**A:** Embora estas plataformas sejam impactadas pela manutenção planeada, os clientes que utilizam estas plataformas são considerados seguros, dado que apenas os VMs num único Domínio de Upgrade (UD) serão impactados a qualquer momento. A manutenção do self-service não está atualmente disponível para serviços em nuvem (Função Web/Trabalhador) e Tecido de Serviço.

**P: Não vejo nenhuma informação de manutenção nos meus VMs. O que é que correu mal?**

**A:** Existem várias razões pelas quais não está a ver nenhuma informação de manutenção nos seus VMs:
1.  Está a utilizar uma subscrição marcada como interna da Microsoft.
2.  Os seus VMs não estão programados para manutenção. Pode ser que a onda de manutenção tenha terminado, cancelado ou modificado para que os seus VMs não sejam mais afetados por ela.
3.  Não tem a coluna de **manutenção** adicionada à sua visão da lista VM. Embora tenhamos adicionado esta coluna à vista padrão, os clientes que configuraram para ver colunas não predefinidas devem adicionar manualmente a coluna **de Manutenção** à sua visão de lista VM.

**P: O meu VM está agendado para manutenção pela segunda vez. Porquê?**

**A:** Existem vários casos de utilização em que verá o seu VM agendado para manutenção depois de já ter concluído a sua reutilização de manutenção:
1.  Cancelamos a onda de manutenção e reiniciámo-la com uma carga útil diferente. Pode ser que detetámos uma carga útil defeituosa e precisamos simplesmente de descarregar uma carga adicional.
2.  O seu VM foi *servido a* outro nó devido a uma falha de hardware.
3.  Selecionou parar (desalocar) e reiniciar o VM.
4.  Tens **uma paragem automática** ligada para o VM.



## <a name="next-steps"></a>Passos seguintes

Pode manusear a manutenção planeada utilizando o [Azure CLI,](maintenance-notifications-cli.md) [O Azure PowerShell](maintenance-notifications-powershell.md) ou [portal](maintenance-notifications-portal.md).


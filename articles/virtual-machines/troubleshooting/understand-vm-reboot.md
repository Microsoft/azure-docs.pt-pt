---
title: Compreenda um reboot de sistema para um VM Azure Microsoft Docs
description: Lista os eventos que podem fazer com que um VM reinicie
services: virtual-machines
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 4694b6ac829c42f20c6783810c248ee18d220433
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91965765"
---
# <a name="understand-a-system-reboot-for-azure-vm"></a>Compreenda um reboot de sistema para Azure VM

As máquinas virtuais Azure (VMs) podem por vezes reiniciar sem razão aparente, sem provas de que tenha iniciado a operação de reinicialização. Este artigo lista as ações e eventos que podem fazer com que os VMs reiniciem e fornece informações sobre como evitar problemas inesperados de reinicialização ou reduzir o impacto de tais problemas.

## <a name="configure-the-vms-for-high-availability"></a>Configure os VMs para uma elevada disponibilidade

A melhor maneira de proteger uma aplicação que está a decorrer no Azure contra reboots de VM e tempo de inatividade é configurar os VMs para uma alta disponibilidade.

Para fornecer este nível de redundância à sua aplicação, recomendamos que você agrupe dois ou mais VMs em um conjunto de disponibilidade. Esta configuração garante que durante um evento de manutenção planeado ou não planeado, pelo menos um VM está disponível e cumpre o [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_5/)de 99,95 por cento .

Para obter mais informações sobre os conjuntos de disponibilidade, consulte [Gerir a disponibilidade de VMs](../manage-availability.md)

## <a name="resource-health-information"></a>Informação sobre saúde de recursos

A Azure Resource Health é um serviço que expõe a saúde dos recursos individuais da Azure e fornece orientações acccionáveis para problemas de resolução de problemas. Num ambiente em nuvem onde não é possível aceder diretamente a servidores ou elementos de infraestrutura, o objetivo da Resource Health é reduzir o tempo que gasta na resolução de problemas. Em particular, o objetivo é reduzir o tempo que gasta para determinar se a raiz do problema está na aplicação ou num evento dentro da plataforma Azure. Para mais informações, consulte [Compreender e utilizar a Saúde dos Recursos.](../../service-health/resource-health-overview.md)

## <a name="actions-and-events-that-can-cause-the-vm-to-reboot"></a>Ações e eventos que podem fazer com que o VM reinicie

### <a name="planned-maintenance"></a>Manutenção planeada

O Microsoft Azure realiza periodicamente atualizações em todo o mundo para melhorar a fiabilidade, desempenho e segurança da infraestrutura anfitriã que está subjacente aos VMs. Muitas destas atualizações, incluindo atualizações de preservação da memória, são realizadas sem qualquer impacto nos seus VMs ou serviços na nuvem.

No entanto, algumas atualizações requerem um reboot. Nesses casos, os VMs são desligados enquanto remendamos a infraestrutura, e então os VMs são reiniciados.

Para entender o que é a manutenção planeada do Azure e como pode afetar a disponibilidade dos seus VMs Linux, consulte os artigos listados aqui. Os artigos fornecem informações sobre o processo de manutenção planeada do Azure e como agendar a manutenção planeada de modo a reduzir ainda mais o impacto.

- [Manutenção planeada para VMs no Azure](../maintenance-and-updates.md?bc=/azure/virtual-machines/windows/breadcrumb/toc.json&toc=/azure/virtual-machines/windows/toc.json)
- [Como agendar a manutenção planeada em VMs do Azure](../maintenance-and-updates.md?bc=/azure/virtual-machines/windows/breadcrumb/toc.json&toc=/azure/virtual-machines/windows/toc.json)

### <a name="memory-preserving-updates"></a>Atualizações para preservação de memória

Para esta classe de atualizações no Microsoft Azure, os utilizadores não têm qualquer impacto nos seus VMs de execução. Muitas destas atualizações são componentes ou serviços que podem ser atualizados sem interferir com a instância em execução. Algumas são atualizações de infraestruturas de plataforma no sistema operativo anfitrião que podem ser aplicadas sem um reboot dos VMs.

Estas atualizações de preservação da memória são realizadas com tecnologia que permite a migração em direto no local. Quando está a ser atualizado, o VM é colocado em *estado de pausa.* Este estado preserva a memória RAM enquanto o sistema operativo anfitrião subjacente recebe as atualizações e patches necessários. A VM retoma o funcionamento 30 segundos após ter sido colocada em pausa. Depois de a VM retomar o funcionamento, o relógio da mesma é sincronizado automaticamente.

Devido ao curto período de pausa, a implementação de atualizações através deste mecanismo reduz consideravelmente o impacto nos VMs. No entanto, nem todas as atualizações podem ser implementadas desta forma. 

As atualizações de várias instâncias (para VMs num conjunto de disponibilidade) são aplicadas num domínio de atualização de cada vez.

> [!NOTE]
> As máquinas Linux que têm versões antigas de kernel são afetadas por um pânico de núcleo durante este método de atualização. Para evitar este problema, atualize a versão kernel 3.10.0-327.10.1 ou posterior. Para obter mais informações, consulte [Um Azure Linux VM num núcleo de 3.10 polegadas em pânico após uma atualização do nó de anfitrião](https://support.microsoft.com/help/3212236).

### <a name="user-initiated-reboot-or-shutdown-actions"></a>Ações de reinício ou encerramento iniciadas pelo utilizador

Se realizar um reboot a partir do portal Azure, Azure PowerShell, interface de linha de comando ou REST API, pode encontrar o evento no Diário de Atividades do [Azure](../../azure-monitor/platform/platform-logs-overview.md).

Se realizar a ação a partir do sistema operativo do VM, poderá encontrar o evento nos registos do sistema.

Outros cenários que normalmente fazem com que o VM reinicie incluem múltiplas ações de alteração de configuração. Normalmente, verá uma mensagem de aviso indicando que executar uma ação específica resultará num reinício do VM. Exemplos incluem quaisquer operações de redimensionar VM, alterar a palavra-passe da conta administrativa e definir um endereço IP estático.

### <a name="azure-security-center-and-windows-update"></a>Centro de Segurança Azure e Atualização do Windows

O Azure Security Center monitoriza diariamente os VMs windows e Linux diários para atualizações em falta do sistema operativo. O Security Center recupera uma lista de atualizações de segurança e críticas disponíveis a partir de Serviços de Atualização do Windows Update ou Windows Server Update Services (WSUS), dependendo do serviço configurado num VM do Windows. O Security Center também verifica as últimas atualizações para os sistemas Linux. Se o seu VM estiver a perder uma atualização do sistema, o Security Center recomenda que aplique atualizações do sistema. A aplicação destas atualizações do sistema é controlada através do Centro de Segurança no portal Azure. Depois de aplicar algumas atualizações, poderão ser necessárias reinicializações em VM. Para obter mais informações, consulte [Aplicar atualizações do sistema no Centro de Segurança Azure.](../../security-center/asset-inventory.md)

Tal como os servidores no local, o Azure não empurra as atualizações do Windows Update para os VMs do Windows, uma vez que estas máquinas se destinam a ser geridas pelos seus utilizadores. No entanto, é encorajado a deixar ativada a definição automática de Atualização do Windows. A instalação automática de atualizações a partir do Windows Update também pode fazer com que as reinicializações ocorram após a aplicação das atualizações. Para mais informações, consulte [o Windows Update FAQ](https://support.microsoft.com/help/12373/windows-update-faq).

### <a name="other-situations-affecting-the-availability-of-your-vm"></a>Outras situações que afetam a disponibilidade do seu VM

Existem outros casos em que a Azure pode suspender ativamente a utilização de um VM. Receberá notificações por e-mail antes desta ação ser tomada, para que tenha a oportunidade de resolver os problemas subjacentes. Exemplos de problemas que afetam a disponibilidade de VM incluem violações de segurança e a expiração dos métodos de pagamento.

### <a name="host-server-faults"></a>Falhas do servidor anfitrião

O VM está hospedado num servidor físico que está a funcionar dentro de um datacenter Azure. O servidor físico executa um agente chamado Agente Anfitrião, além de alguns outros componentes Azure. Quando estes componentes de software Azure no servidor físico ficam sem resposta, o sistema de monitorização ativa um reboot do servidor anfitrião para tentar a recuperação. O VM está normalmente disponível novamente dentro de cinco minutos e continua a viver no mesmo hospedeiro que anteriormente.

As falhas do servidor são geralmente causadas por falha de hardware, como a falha de um disco rígido ou uma unidade de estado sólido. O Azure monitoriza continuamente estas ocorrências, identifica os bugs subjacentes e lança atualizações após a mitigação ter sido implementada e testada.

Uma vez que algumas falhas do servidor de anfitrião podem ser específicas desse servidor, uma situação de reinicialização repetida do VM pode ser melhorada através da recolocação manual do VM para outro servidor anfitrião. Esta operação pode ser desencadeada utilizando a opção de **reafectação** na página de detalhes do VM, ou parando e reiniciando o VM no portal Azure.

### <a name="auto-recovery"></a>Recuperação automática

Se o servidor anfitrião não conseguir reiniciar por qualquer motivo, a plataforma Azure inicia uma ação de recuperação automática para tirar o servidor anfitrião defeituoso da rotação para uma investigação mais aprofundada. 

Todos os VMs do hospedeiro são automaticamente transferidos para um servidor anfitrião diferente e saudável. Este processo é geralmente concluído dentro de 15 minutos. Para saber mais sobre o processo de recuperação automática, consulte [a recuperação automática de VMs](https://azure.microsoft.com/blog/service-healing-auto-recovery-of-virtual-machines).

### <a name="unplanned-maintenance"></a>Manutenção não planeada

Em raras ocasiões, a equipa de operações da Azure poderá necessitar de realizar atividades de manutenção para garantir a saúde geral da plataforma Azure. Este comportamento pode afetar a disponibilidade de VM, e geralmente resulta na mesma ação de autorrecuperação que descrito anteriormente.  

A manutenção não planeada inclui o seguinte:

- Desfragmentação urgente do nó
- Atualizações urgentes do switch de rede

### <a name="vm-crashes"></a>VM cai

Os VMs podem reiniciar devido a problemas dentro do próprio VM. A carga de trabalho ou função que está a ser executada no VM pode desencadear uma verificação de erros dentro do sistema operativo do hóspede. Para ajudar a determinar o motivo da falha, consulte o sistema e os registos de aplicações para VMs do Windows e os registos em série para Os VMs Do Linux.

### <a name="storage-related-forced-shutdowns"></a>Encerramentos forçados relacionados com o armazenamento

Os VMs em Azure dependem de discos virtuais para sistema operativo e armazenamento de dados que estão alojados na infraestrutura de Armazenamento Azure. Sempre que a disponibilidade ou conectividade entre o VM e os discos virtuais associados for afetada por mais de 120 segundos, a plataforma Azure realiza um encerramento forçado dos VMs para evitar a corrupção de dados. Os VMs são automaticamente ligados após a conectividade do armazenamento ter sido restaurada. 

A duração da paragem pode ser tão curta como cinco minutos, mas pode ser significativamente maior. Segue-se um dos casos específicos associados a paralisações forçadas relacionadas com o armazenamento: 

**Limites de IO superiores**

Os VMs podem ser temporariamente desligados quando os pedidos de E/S são consistentemente estrangulados porque o volume de operações de E/S por segundo (IOPS) excede os limites de E/S para o disco. (O armazenamento padrão do disco está limitado a 500 IOPS.) Para atenuar este problema, utilize a tiragem do disco ou configuure o espaço de armazenamento dentro do VM do hóspede, dependendo da carga de trabalho. 

### <a name="other-incidents"></a>Outros incidentes

Em circunstâncias raras, um problema generalizado pode afetar vários servidores num datacenter Azure. Se este problema ocorrer, a equipa do Azure envia notificações por e-mail às subscrições afetadas. Pode consultar o [painel de instrumentos de saúde do Serviço Azure](https://azure.microsoft.com/status/) e o portal Azure para o estado das interrupções em curso e incidentes passados.
---
title: Compreender um reboot do sistema para um Azure VM  Microsoft Docs
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
ms.openlocfilehash: e94ffb3d34082745c3d7ca86cfda2b93c0ed08da
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919418"
---
# <a name="understand-a-system-reboot-for-azure-vm"></a>Compreenda um reboot do sistema para O VM Azure

As máquinas virtuais Azure (VMs) podem por vezes reiniciar sem razão aparente, sem provas de ter iniciado a operação de reinício. Este artigo lista as ações e eventos que podem fazer com que os VMs reiniciem e forneça minún ismo sobre como evitar problemas de reinicialização inesperados ou reduzir o impacto de tais problemas.

## <a name="configure-the-vms-for-high-availability"></a>Configure os VMs para alta disponibilidade

A melhor maneira de proteger uma aplicação que está a funcionar em Azure contra reboots vm e tempo de inatividade é configurar os VMs para uma alta disponibilidade.

Para fornecer este nível de redundância à sua aplicação, recomendamos que agrupe dois ou mais VMs num conjunto de disponibilidade. Esta configuração garante que durante um evento de manutenção planeado ou não planeado, pelo menos um VM está disponível e cumpre o [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_5/)de 99,95%.

Para mais informações sobre conjuntos de disponibilidade, consulte [Gerir a disponibilidade de VMs](../windows/manage-availability.md)

## <a name="resource-health-information"></a>Informação sobre saúde de recursos

A Azure Resource Health é um serviço que expõe a saúde dos recursos azure individuais e fornece orientações atoveis para problemas de resolução de problemas. Num ambiente de nuvem onde não é possível aceder diretamente a servidores ou elementos de infraestrutura, o objetivo da Resource Health é reduzir o tempo que gasta na resolução de problemas. Em particular, o objetivo é reduzir o tempo que passa a determinar se a raiz do problema está na aplicação ou num evento dentro da plataforma Azure. Para mais informações, consulte Compreender e utilizar a [Saúde dos Recursos.](../../resource-health/resource-health-overview.md)

## <a name="actions-and-events-that-can-cause-the-vm-to-reboot"></a>Ações e eventos que podem fazer com que o VM reinicie

### <a name="planned-maintenance"></a>Manutenção planeada

O Microsoft Azure realiza periodicamente atualizações em todo o mundo para melhorar a fiabilidade, desempenho e segurança da infraestrutura de hospedar que está na base dos VMs. Muitas destas atualizações, incluindo atualizações que preservam a memória, são realizadas sem qualquer impacto nos seus VMs ou serviços na nuvem.

No entanto, algumas atualizações requerem um reboot. Nesses casos, os VMs são encerrados enquanto remendemos a infraestrutura, e depois os VMs são reiniciados.

Para entender o que é a manutenção planeada do Azure e como pode afetar a disponibilidade dos seus VMs Linux, consulte os artigos listados aqui. Os artigos fornecem informações sobre o processo de manutenção planeada do Azure e como agendar a manutenção planeada de modo a reduzir ainda mais o impacto.

- [Manutenção planeada para VMs no Azure](../windows/planned-maintenance.md)
- [Como agendar a manutenção planeada em VMs do Azure](../windows/classic/planned-maintenance-schedule.md)

### <a name="memory-preserving-updates"></a>Atualizações para preservação de memória

Para esta classe de atualizações no Microsoft Azure, os utilizadores não experimentam qualquer impacto nos seus VMs em execução. Muitas destas atualizações são componentes ou serviços que podem ser atualizados sem interferir com a instância em execução. Algumas são atualizações de infraestrutura de plataforma no sistema operativo anfitrião que podem ser aplicadas sem um reboot dos VMs.

Estas atualizações de preservação da memória são realizadas com tecnologia que permite a migração em direto no local. Quando está a ser atualizado, o VM é colocado em estado *de pausa.* Este estado preserva a memória RAM enquanto o sistema operativo anfitrião subjacente recebe as atualizações e patches necessários. A VM retoma o funcionamento 30 segundos após ter sido colocada em pausa. Depois de a VM retomar o funcionamento, o relógio da mesma é sincronizado automaticamente.

Devido ao curto período de pausa, a implementação de atualizações através deste mecanismo reduz consideravelmente o impacto nos VMs. No entanto, nem todas as atualizações podem ser implementadas desta forma. 

As atualizações de várias instâncias (para VMs num conjunto de disponibilidade) são aplicadas num domínio de atualização de cada vez.

> [!NOTE]
> As máquinas linux que têm versões antigas de kernel são afetadas por um pânico de kernel durante este método de atualização. Para evitar este problema, atualize para a versão kernel 3.10.0-327.10.1 ou posterior. Para mais informações, consulte [an Azure Linux VM num kernel 3.10 baseado em pânicoapós um upgrade](https://support.microsoft.com/help/3212236)do nó anfitrião .

### <a name="user-initiated-reboot-or-shutdown-actions"></a>Ações de reinício ou de encerramento iniciadas pelo utilizador

Se efetuar um reboot a partir do portal Azure, Azure PowerShell, interface de linha de comando ou REST API, pode encontrar o evento no Registo de [Atividades do Azure](../../azure-monitor/platform/platform-logs-overview.md).

Se executar a ação a partir do sistema operativo vM, pode encontrar o evento nos registos do sistema.

Outros cenários que geralmente fazem com que o VM reinicie incluem múltiplas ações de mudança de configuração. Normalmente verá uma mensagem de aviso indicando que executar uma determinada ação resultará num reinício do VM. Exemplos incluem quaisquer operações de redimensionação vm, alteração da palavra-passe da conta administrativa e definição de um endereço IP estático.

### <a name="azure-security-center-and-windows-update"></a>Centro de Segurança Azure e atualização do Windows

O Azure Security Center monitoriza diariamente os VMs Windows e Linux para as atualizações do sistema operativo em falta. O Security Center recupera uma lista de segurança disponível e atualizações críticas a partir de Windows Update ou Windows Server Update Services (WSUS), dependendo do serviço configurado num VM do Windows. O Security Center também verifica as últimas atualizações para os sistemas Linux. Se o seu VM não tiver uma atualização do sistema, o Security Center recomenda que aplique atualizações do sistema. A aplicação destas atualizações do sistema é controlada através do Centro de Segurança do portal Azure. Depois de aplicar algumas atualizações, podem ser necessários reboots vm. Para mais informações, consulte Aplicar atualizações do [sistema no Centro de Segurança Azure](../../security-center/security-center-apply-system-updates.md).

Tal como os servidores no local, o Azure não empurra as atualizações do Windows Update para os VMs do Windows, uma vez que estas máquinas se destinam a ser geridas pelos seus utilizadores. No entanto, é encorajado a deixar ativada a definição automática de Atualização do Windows. A instalação automática de atualizações a partir do Windows Update também pode causar reinicializações após a aplicação das atualizações. Para mais informações, consulte [o Windows Update FAQ](https://support.microsoft.com/help/12373/windows-update-faq).

### <a name="other-situations-affecting-the-availability-of-your-vm"></a>Outras situações que afetam a disponibilidade do seu VM

Há outros casos em que o Azure pode suspender ativamente a utilização de um VM. Receberá notificações por e-mail antes de esta ação ser tomada, para que tenha a oportunidade de resolver os problemas subjacentes. Exemplos de questões que afetam a disponibilidade de VM incluem violações de segurança e a expiração dos métodos de pagamento.

### <a name="host-server-faults"></a>Falhas no servidor de anfitriões

O VM está hospedado num servidor físico que está a funcionar dentro de um centro de dados Azure. O servidor físico executa um agente chamado Agente Anfitrião, além de alguns outros componentes Do Azure. Quando estes componentes de software Azure no servidor físico ficam sem resposta, o sistema de monitorização desencadeia um reboot do servidor hospedeiro para tentar a recuperação. O VM está geralmente disponível novamente dentro de cinco minutos e continua a viver no mesmo hospedeiro que anteriormente.

As falhas do servidor são geralmente causadas por falhas de hardware, tais como a falha de um disco rígido ou unidade de estado sólido. O Azure monitoriza continuamente estas ocorrências, identifica os bugs subjacentes e lança atualizações após a mitigação ter sido implementada e testada.

Como algumas falhas do servidor do anfitrião podem ser específicas para esse servidor, uma situação de reinicialização vM repetida pode ser melhorada reimplantando manualmente o VM para outro servidor anfitrião. Esta operação pode ser desencadeada utilizando a opção de **reimplantação** na página de detalhes do VM, ou parando e reiniciando o VM no portal Azure.

### <a name="auto-recovery"></a>Autorrecuperação

Se o servidor anfitrião não conseguir reiniciar por qualquer motivo, a plataforma Azure inicia uma ação de recuperação automática para tirar o servidor de hospedeiro defeituoso da rotação para uma investigação mais aprofundada. 

Todos os VMs desse hospedeiro são automaticamente transferidos para um servidor hospedeiro diferente e saudável. Este processo é geralmente concluído dentro de 15 minutos. Para saber mais sobre o processo de recuperação automática, consulte a [recuperação automática de VMs](https://azure.microsoft.com/blog/service-healing-auto-recovery-of-virtual-machines).

### <a name="unplanned-maintenance"></a>Manutenção não planeada

Em raras ocasiões, a equipa de operações do Azure poderá ter de realizar atividades de manutenção para garantir a saúde geral da plataforma Azure. Este comportamento pode afetar a disponibilidade de VM, e geralmente resulta na mesma ação de recuperação automática como descrito anteriormente.  

A manutenção não planeada inclui o seguinte:

- Desfragmentação urgente do nó
- Atualizações urgentes do switch de rede

### <a name="vm-crashes"></a>Acidentes vm

As VMs podem reiniciar devido a problemas dentro do próprio VM. A carga de trabalho ou função que está a decorrer no VM pode desencadear uma verificação de bugs dentro do sistema operativo convidado. Para ajudar a determinar o motivo do acidente, veja o sistema e os registos de aplicações para VMs do Windows e os registos em série para VMs Linux.

### <a name="storage-related-forced-shutdowns"></a>Paralisações forçadas relacionadas com armazenamento

Os VMs em Azure dependem de discos virtuais para sistema operativo e armazenamento de dados que está alojado na infraestrutura de armazenamento Azure. Sempre que a disponibilidade ou conectividade entre o VM e os discos virtuais associados seja afetado por mais de 120 segundos, a plataforma Azure realiza uma paragem forçada dos VMs para evitar a corrupção de dados. Os VMs são automaticamente ligados de volta após a conectividade de armazenamento ter sido restaurada. 

A duração da paragem pode ser tão curta como cinco minutos, mas pode ser significativamente mais longa. Segue-se um dos casos específicos associados a paralisações forçadas relacionadas com o armazenamento: 

**Excedendo os limites da IO**

Os VMs podem ser temporariamente desligados quando os pedidos de I/S são constantemente acelerados porque o volume de operações de I/S por segundo (IOPS) excede os limites de I/S para o disco. (O armazenamento em disco padrão é limitado a 500 IOPS.) Para mitigar este problema, utilize a tirade disquete de disco ou configure o espaço de armazenamento dentro do VM do hóspede, dependendo da carga de trabalho. Para mais detalhes, consulte [Configurar VMs Azure para obter](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)um ótimo desempenho de armazenamento .

### <a name="other-incidents"></a>Outros incidentes

Em circunstâncias raras, um problema generalizado pode afetar vários servidores num centro de dados Azure. Se este problema ocorrer, a equipa do Azure envia notificações por e-mail para as subscrições afetadas. Pode consultar o painel de saúde do [serviço Azure](https://azure.microsoft.com/status/) e o portal Azure sobre o estado das interrupções em curso e incidentes passados.

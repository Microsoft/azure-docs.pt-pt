---
title: Entender uma reinicialização do sistema para uma VM do Azure | Microsoft Docs
description: Lista os eventos que podem causar a reinicialização de uma VM
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
ms.openlocfilehash: a536eb89f2040333617329e963ce00f5c6b1ba7a
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086966"
---
# <a name="understand-a-system-reboot-for-azure-vm"></a>Entender uma reinicialização do sistema para a VM do Azure

As VMs (máquinas virtuais) do Azure podem, às vezes, ser reinicializadas sem nenhum motivo aparente, sem evidências de que você iniciou a operação de reinicialização. Este artigo lista as ações e os eventos que podem causar a reinicialização das VMs e fornece informações sobre como evitar problemas de reinicialização inesperados ou reduzir o impacto de tais problemas.

## <a name="configure-the-vms-for-high-availability"></a>Configurar as VMs para alta disponibilidade

A melhor maneira de proteger um aplicativo em execução no Azure contra reinicializações de VM e tempo de inatividade é configurar as VMs para alta disponibilidade.

Para fornecer esse nível de redundância ao seu aplicativo, recomendamos que você agrupe duas ou mais VMs em um conjunto de disponibilidade. Essa configuração garante que, durante um evento de manutenção planejada ou não planejada, pelo menos uma VM esteja disponível e atenda ao SLA de 99,95% [do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_5/).

Para obter mais informações sobre conjuntos de disponibilidade, consulte os seguintes artigos:

- [Gerenciar a disponibilidade de VMs](../windows/manage-availability.md)
- [Configurar a disponibilidade de VMs](../windows/classic/configure-availability.md)

## <a name="resource-health-information"></a>Informações de Resource Health

Azure Resource Health é um serviço que expõe a integridade de recursos individuais do Azure e fornece diretrizes acionáveis para solucionar problemas. Em um ambiente de nuvem em que não é possível acessar diretamente os servidores ou elementos de infraestrutura, o objetivo do Resource Health é reduzir o tempo gasto na solução de problemas. Em particular, o objetivo é reduzir o tempo gasto determinando se a raiz do problema está no aplicativo ou em um evento dentro da plataforma Azure. Para obter mais informações, consulte [entender e usar Resource Health](../../resource-health/resource-health-overview.md).

## <a name="actions-and-events-that-can-cause-the-vm-to-reboot"></a>Ações e eventos que podem fazer com que a VM seja reinicializada

### <a name="planned-maintenance"></a>Manutenção planeada

O Microsoft Azure realiza atualizações periodicamente em todo o mundo para melhorar a confiabilidade, o desempenho e a segurança da infraestrutura de host que utiliza as VMs. Muitas dessas atualizações, incluindo atualizações de preservação de memória, são executadas sem qualquer impacto em suas VMs ou serviços de nuvem.

No entanto, algumas atualizações exigem uma reinicialização. Nesses casos, as VMs são desligadas enquanto corrigimos a infraestrutura e, em seguida, as VMs são reiniciadas.

Para entender o que é a manutenção planejada do Azure e como ela pode afetar a disponibilidade de suas VMs Linux, consulte os artigos listados aqui. Os artigos fornecem informações sobre o processo de manutenção planeada do Azure e como agendar a manutenção planeada de modo a reduzir ainda mais o impacto.

- [Manutenção planeada para VMs no Azure](../windows/planned-maintenance.md)
- [Como agendar a manutenção planeada em VMs do Azure](../windows/classic/planned-maintenance-schedule.md)

### <a name="memory-preserving-updates"></a>Atualizações para preservação de memória

Para essa classe de atualizações no Microsoft Azure, os usuários não têm impacto em suas VMs em execução. Muitas destas atualizações são componentes ou serviços que podem ser atualizados sem interferir com a instância em execução. Algumas são atualizações de infraestrutura de plataforma no sistema operacional do host que podem ser aplicadas sem uma reinicialização das VMs.

Estas atualizações de preservação da memória são realizadas com tecnologia que permite a migração em direto no local. Quando ele está sendo atualizado, a VM é colocada em um estado de *pausa* . Este estado preserva a memória RAM enquanto o sistema operativo anfitrião subjacente recebe as atualizações e patches necessários. A VM retoma o funcionamento 30 segundos após ter sido colocada em pausa. Depois de a VM retomar o funcionamento, o relógio da mesma é sincronizado automaticamente.

Devido ao curto período de pausa, a implantação de atualizações por meio desse mecanismo reduz consideravelmente o impacto nas VMs. No entanto, nem todas as atualizações podem ser implantadas dessa maneira. 

As atualizações de várias instâncias (para VMs num conjunto de disponibilidade) são aplicadas num domínio de atualização de cada vez.

> [!NOTE]
> Computadores Linux com versões de kernel antigas são afetados por um pane de kernel durante esse método de atualização. Para evitar esse problema, atualize para o kernel versão 3.10.0-327.10.1 ou posterior. Para obter mais informações, consulte [uma VM Linux do Azure em um kernel baseado em 3,10 está em pânico após uma atualização de nó de host](https://support.microsoft.com/help/3212236).

### <a name="user-initiated-reboot-or-shutdown-actions"></a>Ações de reinicialização ou desligamento iniciadas pelo usuário

Se você executar uma reinicialização do portal do Azure, Azure PowerShell, interface de linha de comando ou API de redefinição, poderá encontrar o evento no [log de atividades do Azure](../../azure-monitor/platform/activity-logs-overview.md).

Se você executar a ação do sistema operacional da VM, poderá encontrar o evento nos logs do sistema.

Outros cenários que geralmente fazem com que a VM reinicie incluem várias ações de alteração de configuração. Você verá normalmente uma mensagem de aviso indicando que a execução de uma ação específica resultará em uma reinicialização da VM. Os exemplos incluem todas as operações de redimensionamento de VM, a alteração da senha da conta administrativa e a definição de um endereço IP estático.

### <a name="azure-security-center-and-windows-update"></a>Central de segurança do Azure e Windows Update

A central de segurança do Azure monitora as VMs diárias do Windows e do Linux para as atualizações de sistema operacional ausentes. A central de segurança recupera uma lista de atualizações críticas e de segurança disponíveis do Windows Update ou do Windows Server Update Services (WSUS), dependendo de qual serviço está configurado em uma VM do Windows. A central de segurança também verifica as atualizações mais recentes para sistemas Linux. Se a sua VM não tiver uma atualização do sistema, a central de segurança recomendará que você aplique as atualizações do sistema. O aplicativo dessas atualizações do sistema é controlado por meio da central de segurança no portal do Azure. Depois de aplicar algumas atualizações, podem ser necessárias reinicializações de VM. Para obter mais informações, consulte [aplicar atualizações do sistema na central de segurança do Azure](../../security-center/security-center-apply-system-updates.md).

Como servidores locais, o Azure não envia atualizações de Windows Update para VMs do Windows, pois essas máquinas devem ser gerenciadas por seus usuários. Você, no entanto, foi incentivado a deixar a configuração de Windows Update automática habilitada. A instalação automática de atualizações do Windows Update também pode fazer com que as reinicializações ocorram após a aplicação das atualizações. Para obter mais informações, consulte [perguntas frequentes Windows Update](https://support.microsoft.com/help/12373/windows-update-faq).

### <a name="other-situations-affecting-the-availability-of-your-vm"></a>Outras situações que afetam a disponibilidade de sua VM

Há outros casos em que o Azure pode suspender ativamente o uso de uma VM. Você receberá notificações por email antes que essa ação seja executada, portanto, você terá a oportunidade de resolver os problemas subjacentes. Exemplos de problemas que afetam a disponibilidade da VM incluem violações de segurança e a expiração dos métodos de pagamento.

### <a name="host-server-faults"></a>Falhas do servidor host

A VM é hospedada em um servidor físico que está sendo executado dentro de um datacenter do Azure. O servidor físico executa um agente chamado agente de host, além de alguns outros componentes do Azure. Quando esses componentes de software do Azure no servidor físico ficarem sem resposta, o sistema de monitoramento disparará uma reinicialização do servidor host para tentar a recuperação. A VM geralmente está disponível novamente dentro de cinco minutos e continua a residir no mesmo host que antes.

Falhas de servidor geralmente são causadas por falha de hardware, como a falha de um disco rígido ou uma unidade de estado sólido. O Azure monitora continuamente essas ocorrências, identifica os bugs subjacentes e distribui as atualizações depois que a mitigação é implementada e testada.

Como algumas falhas do servidor host podem ser específicas desse servidor, uma situação de reinicialização de VM repetida pode ser melhorada com a reimplantação manual da VM em outro servidor host. Essa operação pode ser disparada usando a opção **reimplantação** na página de detalhes da VM ou interrompendo e reiniciando a VM no portal do Azure.

### <a name="auto-recovery"></a>Recuperação automática

Se o servidor host não puder ser reinicializado por algum motivo, a plataforma do Azure iniciará uma ação de recuperação automática para levar o servidor host defeituoso para uma investigação mais aprofundada. 

Todas as VMs nesse host são automaticamente realocadas para um servidor host diferente e íntegro. Esse processo geralmente é concluído em 15 minutos. Para saber mais sobre o processo de recuperação automática, consulte [recuperação automática de VMs](https://azure.microsoft.com/blog/service-healing-auto-recovery-of-virtual-machines).

### <a name="unplanned-maintenance"></a>Manutenção não planejada

Em raras ocasiões, a equipe de operações do Azure pode precisar executar atividades de manutenção para garantir a integridade geral da plataforma Azure. Esse comportamento pode afetar a disponibilidade da VM e geralmente resulta na mesma ação de recuperação automática, conforme descrito anteriormente.  

A manutenção não planejada inclui o seguinte:

- Desfragmentação de nó urgente
- Atualizações urgentes do comutador de rede

### <a name="vm-crashes"></a>Falhas de VM

As VMs podem ser reiniciadas devido a problemas na própria VM. A carga de trabalho ou função que está sendo executada na VM pode disparar uma verificação de bug dentro do sistema operacional convidado. Para obter ajuda para determinar o motivo da falha, exiba os logs do sistema e do aplicativo para VMs do Windows e os logs de série para VMs do Linux.

### <a name="storage-related-forced-shutdowns"></a>Desligamentos forçados relacionados ao armazenamento

As VMs no Azure dependem de discos virtuais para o sistema operacional e o armazenamento de dados hospedados na infraestrutura de armazenamento do Azure. Sempre que a disponibilidade ou a conectividade entre a VM e os discos virtuais associados for afetada por mais de 120 segundos, a plataforma do Azure executará um desligamento forçado das VMs para evitar a corrupção de dados. As VMs são religadas automaticamente após a restauração da conectividade de armazenamento. 

A duração do desligamento pode ser tão curta quanto cinco minutos, mas pode ser significativamente maior. A seguir, um dos casos específicos que está associado aos desligamentos forçados relacionados ao armazenamento: 

**Excedendo os limites de e/s**

As VMs podem ser desligadas temporariamente quando as solicitações de e/s são limitadas consistentemente porque o volume de operações de e/s por segundo (IOPS) excede os limites de e/s para o disco. (O armazenamento em disco padrão é limitado a 500 IOPS.) Para atenuar esse problema, use a distribuição de disco ou configure o espaço de armazenamento dentro da VM convidada, dependendo da carga de trabalho. Para obter detalhes, consulte [Configurando VMs do Azure para desempenho de armazenamento ideal](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx).

### <a name="other-incidents"></a>Outros incidentes

Em raras circunstâncias, um problema amplo pode afetar vários servidores em um datacenter do Azure. Se esse problema ocorrer, a equipe do Azure enviará notificações por email para as assinaturas afetadas. Você pode verificar o [painel de integridade do serviço do Azure](https://azure.microsoft.com/status/) e o portal do Azure para o status de interrupções em andamento e incidentes passados.

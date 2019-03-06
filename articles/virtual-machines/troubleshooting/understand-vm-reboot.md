---
title: Compreender um reinício do sistema para uma VM do Azure | Documentos da Microsoft
description: Lista os eventos que podem causar uma VM a reiniciar
services: virtual-machines
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 70a6845349b90cf614a84e13680ebb6fc6b3e2a9
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433166"
---
# <a name="understand-a-system-reboot-for-azure-vm"></a>Compreender um reinício do sistema para VM do Azure

Máquinas virtuais do Azure (VMs), às vezes, poderá reiniciar por nenhuma razão aparente, sem provas do ter iniciou a operação de reinício. Este artigo lista as ações e os eventos que podem fazer com que as VMs reiniciar o computador e fornece informações sobre como evitar problemas de reinício inesperado ou reduzir o impacto de tais problemas.

## <a name="configure-the-vms-for-high-availability"></a>Configurar as VMs de elevada disponibilidade

A melhor forma de proteger uma aplicação que está em execução no Azure em relação a VM é reiniciada e tempo de inatividade é configurar as VMs de elevada disponibilidade.

Para fornecer esse nível de redundância à sua aplicação, recomendamos que agrupe duas ou mais VMs num conjunto de disponibilidade. Esta configuração assegura que durante a um evento de manutenção planeada ou, pelo menos uma VM está disponível e que atenda a 99,95 por cento [SLA do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_5/).

Para obter mais informações sobre os conjuntos de disponibilidade, consulte os artigos seguintes:

- [Gerir a disponibilidade de VMs](../windows/manage-availability.md)
- [Configurar a disponibilidade de VMs](../windows/classic/configure-availability.md)

## <a name="resource-health-information"></a>Informações de estado de funcionamento de recursos

Estado de funcionamento de recursos do Azure é um serviço que expõe o estado de funcionamento dos recursos do Azure individuais e oferece orientação acionável para resolução de problemas. Num ambiente de cloud em que não é possível acessar diretamente os servidores ou elementos de infra-estrutura, o objetivo do Resource Health é reduzir o tempo gasto na solução de problemas. Em particular, o objetivo é reduzir o tempo que passam a determinar se a raiz do problema reside no aplicativo ou num evento no interior da plataforma do Azure. Para obter mais informações, consulte [entender e usar estado de funcionamento do recurso](../../resource-health/resource-health-overview.md).

## <a name="actions-and-events-that-can-cause-the-vm-to-reboot"></a>Ações e eventos que podem fazer com que a VM a reiniciar

### <a name="planned-maintenance"></a>Manutenção planeada

Microsoft Azure efetua periodicamente atualizações a nível mundial para melhorar a fiabilidade, desempenho e segurança da infraestrutura do anfitrião que está subjacente às VMs. Muitas destas atualizações, incluindo atualizações de preservação de memória, são efetuadas sem nenhum impacto nas suas VMs ou serviços em nuvem.

No entanto, algumas atualizações requerem um reinício. Nesses casos, as VMs são encerradas durante a correção da infraestrutura e, em seguida, as VMs são reiniciadas.

Para compreender é que a manutenção planeada do Azure e como ele pode afetar a disponibilidade das suas VMs do Linux, veja os artigos listados aqui. Os artigos fornecem informações sobre o processo de manutenção planeada do Azure e como agendar a manutenção planeada de modo a reduzir ainda mais o impacto.

- [Manutenção planeada para VMs no Azure](../windows/planned-maintenance.md)
- [Como agendar a manutenção planeada em VMs do Azure](../windows/classic/planned-maintenance-schedule.md)

### <a name="memory-preserving-updates"></a>Atualizações para preservação de memória

Para esta classe de atualizações no Microsoft Azure, os utilizadores experiência sem impacto nas suas VMs em execução. Muitas destas atualizações são componentes ou serviços que podem ser atualizados sem interferir com a instância em execução. Algumas são atualizações de infraestrutura de plataforma no sistema operacional host que podem ser aplicadas sem um reinício de VMs.

Estas atualizações de preservação da memória são realizadas com tecnologia que permite a migração em direto no local. Quando está a ser atualizado, a VM é colocada numa *colocada em pausa* estado. Este estado preserva a memória RAM enquanto o sistema operativo anfitrião subjacente recebe as atualizações e patches necessários. A VM retoma o funcionamento 30 segundos após ter sido colocada em pausa. Depois de a VM retomar o funcionamento, o relógio da mesma é sincronizado automaticamente.

Devido ao curto período de pausa, a implantação de atualizações através destes mecanismo bastante reduz o impacto nas VMs. No entanto, nem todas as atualizações podem ser implementadas dessa forma. 

As atualizações de várias instâncias (para VMs num conjunto de disponibilidade) são aplicadas num domínio de atualização de cada vez.

> [!NOTE]
> Máquinas do Linux que têm versões antigas do kernel são afetadas por um entre em pânico do kernel durante este método de atualização. Para evitar este problema, atualize para o kernel versão 3.10.0-327.10.1 ou posterior. Para obter mais informações, consulte [pânicos de uma VM do Linux do Azure num kernel com base em 3.10 após uma atualização do nó de anfitrião](https://support.microsoft.com/help/3212236).

### <a name="user-initiated-reboot-or-shutdown-actions"></a>Ações de reinício ou encerramento iniciada pelo utilizador

Se executar um reinício a partir do portal do Azure, o Azure PowerShell, a interface de linha de comandos ou a API REST, pode encontrar o evento no [registo de atividades do Azure](../../azure-monitor/platform/activity-logs-overview.md).

Se executar a ação do sistema de operativo da VM, pode encontrar o evento nos registos do sistema.

Outros cenários que, normalmente, fazer com que a VM reiniciar o computador incluem várias ações de alteração de configuração. Normalmente verá uma mensagem de aviso que indica que executar uma ação específica irá resultar num reinício da VM. Os exemplos incluem quaisquer operações de redimensionamento de VM, alterar a palavra-passe da conta administrativa e definir um endereço IP estático.

### <a name="azure-security-center-and-windows-update"></a>Centro de segurança do Azure e o Windows Update

Centro de segurança do Azure monitoriza diárias Windows e VMs do Linux para atualizações de sistema operacional em falta. Centro de segurança obtém uma lista de atualizações críticas e de segurança disponíveis do Windows Update ou Windows Server Update Services (WSUS), dependendo de qual o serviço está configurado numa VM do Windows. Centro de segurança também verifica as atualizações mais recentes para sistemas Linux. Se a sua VM está em falta uma atualização do sistema, o Centro de segurança recomenda que aplique as atualizações do sistema. A aplicação destas atualizações de sistema é controlada através do Centro de segurança no portal do Azure. Depois de aplicar algumas atualizações, os reinícios da VM poderão ser necessários. Para obter mais informações, consulte [aplicar atualizações do sistema no Centro de segurança do Azure](../../security-center/security-center-apply-system-updates.md).

Como servidores no local, Azure não forçar atualizações a partir do Windows Update para VMs do Windows, porque estas máquinas têm a finalidade de ser geridas pelos seus utilizadores. É, no entanto, encorajados a deixar a definição de atualização do Windows automática ativada. Instalação automática de atualizações do Windows Update também pode causar os reinícios para ocorrer depois das atualizações são aplicadas. Para obter mais informações, consulte [FAQ de atualização do Windows](https://support.microsoft.com/help/12373/windows-update-faq).

### <a name="other-situations-affecting-the-availability-of-your-vm"></a>Outras situações que afetam a disponibilidade da sua VM

Existem outros casos em que Azure ativamente poderá suspender a utilização de uma VM. Receberá notificações por e-mail antes desta ação é executada, portanto, terá a oportunidade de se resolver os problemas subjacentes. Exemplos de problemas que afetam a disponibilidade da VM incluem violações de segurança e a expiração de métodos de pagamento.

### <a name="host-server-faults"></a>Falhas de servidor de anfitrião

A VM está alojada num servidor físico que está a ser executado dentro de um datacenter do Azure. O servidor físico executa um agente chamado do agente do anfitrião, além de alguns outros componentes do Azure. Quando esses componentes de software do Azure no servidor físico deixar de responder, o sistema de monitorização aciona uma reinicialização do servidor de anfitrião para tentar a recuperação. A VM está normalmente disponível novamente dentro de cinco minutos e continua a residem no mesmo anfitrião como anteriormente.

Falhas de servidor geralmente são causadas por falha de hardware, tais como a falha de um disco rígido ou unidade de estado sólido. Azure monitoriza essas ocorrências continuamente, identifica os bugs subjacentes e lança atualizações, depois que a atenuação é implementada e testada.

Como algumas falhas de servidor de anfitrião podem ser específicas para esse servidor, uma situação de reinício repetida VM pode ser aumentada com manualmente a reimplementação da VM para outro servidor de anfitrião. Esta operação pode ser acionada, utilizando o **Reimplementar** opção na página de detalhes da VM ou ao parar e reiniciar a VM no portal do Azure.

### <a name="auto-recovery"></a>Recuperação automática

Se o servidor de anfitrião não é possível reinicializar por qualquer motivo, a plataforma Azure inicia uma ação de recuperação automática, coloque o servidor de anfitrião com falhas da rotação para uma investigação mais aprofundada. 

Todas as VMs nesse anfitrião automaticamente são alteradas para um servidor de anfitrião diferente, bom estado de funcionamento. Este processo é normalmente concluído no prazo de 15 minutos. Para saber mais sobre o processo de recuperação automática, consulte [recuperação automática de VMs](https://azure.microsoft.com/blog/service-healing-auto-recovery-of-virtual-machines).

### <a name="unplanned-maintenance"></a>Manutenção não planeada

Em raras ocasiões, a equipe de operações do Azure poderá ter realizar atividades de manutenção para garantir que o estado de funcionamento geral da plataforma do Azure. Este comportamento poderá afetar a disponibilidade da VM e, ele geralmente faz com a mesma ação de recuperação automática, tal como descrito anteriormente.  

Manutenção não planeada incluem o seguinte:

- Desfragmentação de nó urgente
- Atualizações de comutador de rede urgente

### <a name="vm-crashes"></a>Falhas VM

VMs pode ser reiniciado devido a problemas de dentro da VM em si. A carga de trabalho ou da função que está em execução na VM pode disparar uma verificação de bug no sistema operativo convidado. Para obter ajuda a determinar o motivo da falha de sistema, exibir o sistema e registos de aplicações para as VMs do Windows e os registos de seriais para VMs do Linux.

### <a name="storage-related-forced-shutdowns"></a>Relacionados com o armazenamento encerramentos forçados

As VMs no Azure baseiam-se nos discos virtuais para o sistema operativo e o armazenamento de dados que está alojado na infraestrutura de armazenamento do Azure. Sempre que a disponibilidade ou a conectividade entre a VM e os discos virtuais associados é afetada por mais de 120 segundos, a plataforma do Azure executa um encerramento forçado das VMs para evitar danos em dados. As VMs são automaticamente novamente ativadas depois de conectividade de armazenamento foi restaurada. 

Durante o encerramento pode ser o mais curto cinco minutos, mas pode ser bastante maior. Segue-se um dos casos específicos que estão associados com relacionados com o armazenamento encerramentos forçados: 

**Limita a exceder a e/s**

As VMs podem ser temporariamente encerradas quando os pedidos de e/s consistentemente são limitados porque o volume de operações de e/s por segundo (IOPS) excede os limites de e/s para o disco. (O armazenamento de disco standard está limitado a 500 IOPS.) Para atenuar este problema, utilize a repartição de disco ou configurar o espaço de armazenamento dentro do convidado VM, consoante a carga de trabalho. Para obter detalhes, consulte [configurar as VMs do Azure para otimizar o desempenho de armazenamento](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx).

### <a name="other-incidents"></a>Outros incidentes

Em raras circunstâncias, um problema amplo pode afetar vários servidores no datacenter do Azure. Se este problema ocorrer, a equipa do Azure envia notificações por e-mail para as subscrições afetadas. Pode verificar o [dashboard do Azure Service Health](https://azure.microsoft.com/status/) e o portal do Azure para o estado de indisponibilidade em curso e incidentes passados.

---
title: Perguntas frequentes sobre o Servidor de Backup do Azure e o DPM (Data Protection Manager)
description: Neste artigo, descubra respostas para perguntas comuns sobre o MABS (servidor de Backup do Microsoft Azure) e o DPM.
ms.reviewer: srinathv
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: dacurwin
ms.openlocfilehash: 48339addf6020a7a95e1c0b650bfcb43e1898483
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747491"
---
# <a name="azure-backup-server-and-dpm---faq"></a>Servidor de Backup do Azure e o DPM-perguntas frequentes

## <a name="general-questions"></a>Perguntas Gerais

Este artigo responde a perguntas frequentes sobre o Servidor de Backup do Azure e o DPM.

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server"></a>Posso utilizar o Servidor do Backup do Azure para criar uma cópia de segurança de Recuperação Bare-metal (BMR) para um servidor físico?

Sim.

### <a name="can-i-register-the-server-to-multiple-vaults"></a>Posso registrar o servidor em vários cofres?

Não. Um servidor de backup do Azure ou do DPM pode ser registrado em apenas um cofre.

### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>Posso usar o DPM para fazer backup de aplicativos no Azure Stack?

Não. Você pode usar o backup do Azure para proteger Azure Stack, o backup do Azure não dá suporte ao uso do DPM para fazer backup de aplicativos no Azure Stack.

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>Se eu instalei o agente de backup do Azure para proteger meus arquivos e pastas, posso instalar o System Center DPM para fazer backup de cargas de trabalho locais no Azure?

Sim. Mas você deve configurar o DPM primeiro e, em seguida, instalar o agente de backup do Azure.  A instalação de componentes nesta ordem garante que o agente de backup do Azure funcione com o DPM. A instalação do agente antes da instalação do DPM não é aconselhável ou tem suporte.

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Por que não posso adicionar um servidor DPM externo após instalar o UR7 e o agente de backup do Azure mais recente?

Para os servidores DPM com fontes de dados que são protegidas para a nuvem (usando um pacote cumulativo de atualizações anterior ao pacote cumulativo de atualizações 7), você deve aguardar pelo menos um dia depois de instalar o UR7 e o agente de backup do Azure mais recente para começar a **Adicionar um servidor DPM externo**. O período de tempo de um dia é necessário para carregar os metadados dos grupos de proteção do DPM no Azure. Os metadados do grupo de proteção são carregados na primeira vez por meio de um trabalho noturno.

## <a name="vmware-and-hyper-v-backup"></a>Backup do VMware e do Hyper-V

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Posso criar cópias de segurança dos servidores VMware vCenter para o Azure?

Sim. Você pode usar Servidor de Backup do Azure para fazer backup de VMware vCenter Server e hosts ESXi no Azure.

- [Saiba mais](backup-mabs-protection-matrix.md) sobre as versões com suporte.
- [Siga estas etapas](backup-azure-backup-server-vmware.md) para fazer backup de um servidor VMware.

### <a name="do-i-need-a-separate-license-to-recover-a-full-on-premises-vmwarehyper-v-cluster"></a>Preciso de uma licença separada para recuperar um cluster VMware/Hyper-V local completo?

Você não precisa de um licenciamento separado para a proteção do VMware/Hyper-V.

- Se você for um cliente do System Center, use o System Center Data Protection Manager (DPM) para proteger VMs VMware.
- Se você não for um cliente do System Center, poderá usar Servidor de Backup do Azure (pago conforme o uso) para proteger as VMs VMware.

## <a name="sharepoint"></a>SharePoint

### <a name="can-i-recover-a-sharepoint-item-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson-with-protection-on-disk"></a>Posso recuperar um item do SharePoint no local original se o SharePoint estiver configurado usando o SQL AlwaysOn (com proteção em disco)?

Sim, o item pode ser recuperado para o site do SharePoint original.

### <a name="can-i-recover-a-sharepoint-database-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson"></a>Posso recuperar um banco de dados do SharePoint para o local original se o SharePoint estiver configurado usando o SQL AlwaysOn?

Como os bancos de dados do SharePoint são configurados no AlwaysOn do SQL, eles não podem ser modificados, a menos que o grupo de disponibilidade seja removido. Como resultado, o DPM não pode restaurar um banco de dados para o local original. Você pode recuperar um banco de dados SQL Server para outra instância do SQL Server.

## <a name="next-steps"></a>Passos seguintes

Leia as outras perguntas frequentes:

- [Saiba mais](backup-support-matrix-mabs-dpm.md) sobre a matriz de suporte do servidor de backup do Azure e do DPM.
- [Saiba mais](backup-azure-mabs-troubleshoot.md) sobre as diretrizes de solução de problemas do servidor de backup do Azure e do DPM.

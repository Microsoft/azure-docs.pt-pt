---
title: Servidor de backup Azure e FAQ DPM
description: Neste artigo, descubra respostas a perguntas comuns sobre o Microsoft Azure Backup Server (MABS) e DPM (Data Protection Manager).
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/05/2019
ms.openlocfilehash: d0a5c1b5800bbc626453ba8896dc706a0cd5bfd4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324948"
---
# <a name="azure-backup-server-and-dpm---faq"></a>Azure Backup Server e DPM - FAQ

## <a name="general-questions"></a>Perguntas gerais

Este artigo responde frequentemente a perguntas sobre o Azure Backup Server e DPM.

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server"></a>Posso utilizar o Servidor do Backup do Azure para criar uma cópia de segurança de Recuperação Bare-metal (BMR) para um servidor físico?

Sim.

### <a name="can-i-register-the-server-to-multiple-vaults"></a>Posso registar o servidor em vários cofres?

N.º Um servidor DPM ou Azure Backup pode ser registado em apenas um cofre.

### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>Posso usar o DPM para fazer o back up apps em Azure Stack?

N.º Pode utilizar o Azure Backup para proteger o Azure Stack, o Azure Backup não suporta o uso de DPM para apoiar aplicações em Azure Stack.

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>Se instalei o agente Azure Backup para proteger os meus ficheiros e pastas, posso instalar o System Center DPM para fazer cópias de segurança no local para o Azure?

Sim. Mas primeiro deve configurar o DPM e depois instalar o agente Azure Backup.  A instalação de componentes por esta ordem garante que o agente de backup Azure funciona com o DPM. A instalação do agente antes de instalar o DPM não é aconselhável ou suportado.

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Por que não posso adicionar um servidor DPM externo depois de instalar UR7 e o mais recente agente de backup Azure?

Para os servidores DPM com fontes de dados protegidas à nuvem (utilizando um rollup de atualização mais cedo do que o Rollup 7 de atualização), deve esperar pelo menos um dia após a instalação do UR7 e do mais recente agente Azure Backup, para iniciar **adicionar o servidor DPM externo**. O período de um dia é necessário para enviar os metadados dos grupos de proteção DPM para Azure. Os metadados do grupo de proteção são enviados pela primeira vez através de um trabalho noturno.

### <a name="are-there-recommendations-for-configuring-exclusions-for-antivirus-software"></a>Existem recomendações para configurar exclusões para software antivírus?

Sim, é recomendado para configurar a exclusão antivírus. Para obter exclusões para DPM, consulte [executar o software antivírus no servidor DPM](https://docs.microsoft.com/system-center/dpm/run-antivirus-server). Para exclusões para MABS, consulte [o antivírus configurado para o servidor MABS](backup-azure-mabs-troubleshoot.md#configure-antivirus-for-mabs-server).

## <a name="vmware-and-hyper-v-backup"></a>Backup VMware e Hyper-V

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Posso criar cópias de segurança dos servidores VMware vCenter para o Azure?

Sim. Pode utilizar o Azure Backup Server para fazer backup com os anfitriões VMware vCenter Server e ESXi para o Azure.

- [Saiba mais](backup-mabs-protection-matrix.md) sobre versões suportadas.
- [Siga estes passos](backup-azure-backup-server-vmware.md) para fazer uma errósia de apoio a um servidor VMware.

### <a name="do-i-need-a-separate-license-to-recover-a-full-on-premises-vmwarehyper-v-cluster"></a>Preciso de uma licença separada para recuperar um cluster VMware/Hyper-V completo no local?

Não precisa de licença separada para proteção VMware/Hiper-V.

- Se for cliente do System Center, utilize o Gestor de Proteção de Dados do Centro de Sistema (DPM) para proteger vMware VMs.
- Se não for cliente do System Center, pode utilizar o Azure Backup Server (pay-as-you-go) para proteger VMware VMs.

## <a name="sharepoint"></a>SharePoint

### <a name="can-i-recover-a-sharepoint-item-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson-with-protection-on-disk"></a>Posso recuperar um item SharePoint para a localização original se o SharePoint estiver configurado utilizando o SQL AlwaysOn (com proteção no disco)?

Sim, o item pode ser recuperado para o site original do SharePoint.

### <a name="can-i-recover-a-sharepoint-database-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson"></a>Posso recuperar uma base de dados sharePoint para a localização original se o SharePoint estiver configurado utilizando o SQL AlwaysOn?

Como as bases de dados do SharePoint estão configuradas no SQL AlwaysOn, não podem ser modificadas a menos que o grupo de disponibilidade seja removido. Como resultado, o DPM não pode restaurar uma base de dados para a localização original. Pode recuperar uma base de dados do SQL Server para outra instância do SQL Server.

## <a name="next-steps"></a>Passos seguintes

Leia as outras FAQs:

- [Saiba mais](backup-support-matrix-mabs-dpm.md) sobre o Servidor de Backup Azure e a matriz de suporte DPM.
- [Saiba mais](backup-azure-mabs-troubleshoot.md) sobre as diretrizes de resolução de problemas do Azure Backup Server e DPM.

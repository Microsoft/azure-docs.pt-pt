---
title: Servidor de backup Azure e FAQ DPM
description: Neste artigo, descubra respostas a perguntas comuns sobre o Microsoft Azure Backup Server (MABS) e DPM (Data Protection Manager).
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/05/2019
ms.openlocfilehash: 35957a1e8a3d6c3d9be06d9d44dbcd47efa0e6ee
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74173162"
---
# <a name="azure-backup-server-and-dpm---faq"></a>Servidor de backup Azure e DPM - FAQ

## <a name="general-questions"></a>Perguntas gerais

Este artigo responde frequentemente a perguntas sobre o Servidor de Backup Azure e dPM.

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server"></a>Posso utilizar o Servidor do Backup do Azure para criar uma cópia de segurança de Recuperação Bare-metal (BMR) para um servidor físico?

Sim.

### <a name="can-i-register-the-server-to-multiple-vaults"></a>Posso registar o servidor em vários cofres?

Não. Um servidor DPM ou Azure Backup pode ser registado em apenas um cofre.

### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>Posso usar o DPM para fazer back apps em Azure Stack?

Não. Pode utilizar o Azure Backup para proteger o Azure Stack, o Azure Backup não suporta o uso de DPM para fazer backup de aplicações em Azure Stack.

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>Se instalei o agente de backup Azure para proteger os meus ficheiros e pastas, posso instalar o System Center DPM para fazer o backup das cargas de trabalho no local para o Azure?

Sim. Mas deve configurar o DPM primeiro, e depois instalar o agente de reserva Azure.  A instalação de componentes nesta ordem garante que o agente de backup Azure trabalha com o DPM. A instalação do agente antes de instalar o DPM não é aconselhável ou suportada.

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Por que não posso adicionar um servidor DPM externo depois de instalar UR7 e o mais recente agente de backup Azure?

Para os servidores DPM com fontes de dados protegidas para a nuvem (utilizando uma atualização de rollup mais cedo do que o Rollup de atualização 7), deve esperar pelo menos um dia após a instalação do UR7 e mais recente agente de backup Azure, para iniciar **adicionar servidor DPM Externo**. O período de tempo de um dia é necessário para enviar os metadados dos grupos de proteção DPM para o Azure. Os metadados do grupo de proteção são carregados pela primeira vez através de um trabalho noturno.

## <a name="vmware-and-hyper-v-backup"></a>VMware e backup Hyper-V

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Posso criar cópias de segurança dos servidores VMware vCenter para o Azure?

Sim. Pode utilizar o Servidor de Backup Azure para fazer backup vCenter Server vCenter Server vMware e anfitriões ESXi para o Azure.

- [Saiba mais](backup-mabs-protection-matrix.md) sobre versões suportadas.
- [Siga estes passos](backup-azure-backup-server-vmware.md) para fazer o apoio a um servidor VMware.

### <a name="do-i-need-a-separate-license-to-recover-a-full-on-premises-vmwarehyper-v-cluster"></a>Preciso de uma licença separada para recuperar um aglomerado VMware/Hyper-V completo?

Não precisa de licenciamento separado para proteção VMware/Hyper-V.

- Se for cliente do System Center, utilize o System Center Data Protection Manager (DPM) para proteger os VMware VMs.
- Se não for um cliente do System Center, pode utilizar o Servidor de Backup Azure (pay-as-you-go) para proteger vMware VMs.

## <a name="sharepoint"></a>SharePoint

### <a name="can-i-recover-a-sharepoint-item-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson-with-protection-on-disk"></a>Posso recuperar um item Do SharePoint para a localização original se o SharePoint estiver configurado utilizando o SQL AlwaysOn (com proteção no disco)?

Sim, o item pode ser recuperado para o site original do SharePoint.

### <a name="can-i-recover-a-sharepoint-database-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson"></a>Posso recuperar uma base de dados sharePoint para a localização original se o SharePoint estiver configurado utilizando o SQL AlwaysOn?

Uma vez que as bases de dados do SharePoint estão configuradas no SQL AlwaysOn, não podem ser modificadas a menos que o grupo de disponibilidade seja removido. Como resultado, o DPM não pode restaurar uma base de dados para a localização original. Pode recuperar uma base de dados do SQL Server para outra instância do Servidor SQL.

## <a name="next-steps"></a>Passos seguintes

Leia as outras FAQs:

- [Saiba mais](backup-support-matrix-mabs-dpm.md) sobre o Servidor de Backup Azure e a matriz de suporte dPM.
- [Saiba mais](backup-azure-mabs-troubleshoot.md) sobre as diretrizes de resolução de problemas do Servidor de Backup Azure e dPM.

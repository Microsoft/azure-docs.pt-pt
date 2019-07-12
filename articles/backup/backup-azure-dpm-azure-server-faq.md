---
title: FAQ do Azure de servidor e do DPM de cópia de segurança
description: 'Respostas a perguntas comuns sobre: O servidor de cópia de segurança do Azure e o DPM.'
services: backup
author: srinathvasireddy
manager: sivan
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: srinathv
ms.openlocfilehash: 7a598038ee435b67b9ad8f06bdec2490bc1c53c3
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705111"
---
# <a name="azure-backup-server-and-dpm---faq"></a>O Azure Backup Server e do DPM - FAQ
Este artigo responde às perguntas mais frequentes sobre o Azure Backup Server e o DPM.

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>Posso utilizar o Servidor do Backup do Azure para criar uma cópia de segurança de Recuperação Bare-metal (BMR) para um servidor físico? <br/>
Sim.

### <a name="can-i-register-the-server-to-multiple-vaults"></a>Pode registar o servidor para diversos cofres?
Não. Um servidor DPM ou o Azure Backup pode ser registado para apenas um cofre.


### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>Pode utilizar o DPM para efetuar cópias de segurança de aplicações no Azure Stack?
Não. Pode utilizar o Azure Backup para proteger o Azure Stack, Azure Backup não suporta a utilização do DPM na cópia de segurança de aplicações no Azure Stack.

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>Se tiver instalado o agente de cópia de segurança do Azure para proteger os meus ficheiros e pastas, posso instalar o System Center DPM para efetuar cópias de segurança de cargas de trabalho no local para o Azure?
Sim. Mas deve configurar o DPM primeiro e, em seguida, instale o agente de cópia de segurança do Azure.  Instalar componentes por esta ordem garante que o Azure Backup agent funciona com o DPM. Instalar o agente antes de instalar o DPM não é aconselhado ou suportada.

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Por que não é possível adicionar um servidor DPM externo após a instalação UR7 e ao agente de cópia de segurança do Azure mais recente?
Para os servidores DPM com origens de dados que estão protegidos para a cloud (ao utilizar um update rollup anterior ao Update Rollup 7), tem de aguardar pelo menos um dia depois de instalar o UR7 e o agente de cópia de segurança do Azure mais recente, para iniciar **deservidordeadicionarDPMexterno**. O período de tempo de um dia é necessário para carregar os metadados dos grupos de proteção do DPM para o Azure. Metadados de grupo de proteção é carregado pela primeira vez por meio de uma tarefa noturna.

## <a name="vmware-and-hyper-v-backup"></a>Cópia de segurança do VMware e Hyper-V

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Posso criar cópias de segurança dos servidores VMware vCenter para o Azure?
Sim. Pode utilizar o Azure Backup Server para cópia de segurança no servidor do VMware vCenter e anfitriões ESXi para o Azure.

- [Saiba mais](backup-mabs-protection-matrix.md) sobre versões suportadas.
- [Siga estes passos](backup-azure-backup-server-vmware.md) para fazer backup de um servidor VMware.

### <a name="do-i-need-a-separate-license-to-recover-a-full-on-premises-vmwarehyper-v-cluster"></a>É necessário uma licença separada para recuperar um cluster de VMware/Hyper-V completa no local?
Não precisa separar de licenciamento para a proteção de VMware/Hyper-V.

- Se for um cliente do System Center, utilize o System Center Data Protection Manager (DPM) para proteger as VMs de VMware.
- Se não tenha um cliente do System Center, pode utilizar o Azure Backup Server (pay as you go) para proteger as VMs de VMware.


## <a name="sharepoint"></a>SharePoint

### <a name="can-i-recover-a-sharepoint-item-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson-with-protection-on-disk"></a>Posso recuperar um item do SharePoint para a localização original se o SharePoint estiver configurado com o SQL AlwaysOn (com a proteção no disco)?
Sim, o item pode ser recuperado para o site do SharePoint original.

### <a name="can-i-recover-a-sharepoint-database-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson"></a>Posso recuperar uma base de dados do SharePoint para a localização original se o SharePoint estiver configurado com o AlwaysOn de SQL?
Como os bancos de dados do SharePoint são configurados no SQL AlwaysOn, não é possível modificar a menos que o grupo de disponibilidade é removido. Como resultado, o DPM não é possível restaurar uma base de dados para a localização original. Pode recuperar uma base de dados do SQL Server para outra instância do SQL Server.

## <a name="next-steps"></a>Passos Seguintes

Leia as perguntas frequentes:

- [Saiba mais](backup-support-matrix-mabs-dpm.md) matriz de suporte sobre o Azure Backup Server e o DPM.
- [Saiba mais](backup-azure-mabs-troubleshoot.md) sobre o Azure Backup Server e o DPM diretrizes de resolução de problemas.

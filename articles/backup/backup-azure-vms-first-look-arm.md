---
title: Criar cópias de segurança uma VM do Azure a partir das definições de VM com o serviço de cópia de segurança do Azure
description: Saiba como fazer cópias de segurança de uma VM do Azure com o serviço de cópia de segurança do Azure
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: 16c7fe68e67094cef21d9efbffdccd614827ae6d
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56887476"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>Criar cópias de segurança uma VM do Azure a partir das definições de VM

Este artigo explica como fazer cópias de segurança de VMs do Azure com o [Azure Backup](backup-overview.md) serviço. Pode fazer backup de VMs do Azure através de dois métodos:

- VM do Azure única: As instruções neste artigo descrevem como efetuar cópias de segurança de uma VM do Azure diretamente a partir das definições de VM.
- Várias VMs do Azure: Pode configurar um cofre dos serviços de recuperação e configurar a cópia de segurança de várias VMs do Azure. Siga as instruções em [este artigo](backup-azure-arm-vms-prepare.md) para este cenário.

 

## <a name="before-you-start"></a>Antes de começar

1. [Saiba mais](backup-architecture.md#how-does-azure-backup-work) como cópia de segurança funciona, e [verificar](backup-support-matrix.md#azure-vm-backup-support) dar suporte aos requisitos. 
2. [Obtenha uma visão geral](backup-azure-vms-introduction.md) da cópia de segurança de VM do Azure.

### <a name="azure-vm-agent-installation"></a>Instalação do agente VM do Azure

Para fazer uma cópia de segurança de VMs do Azure, o Azure Backup instala uma extensão no agente VM em execução na máquina. Se a VM foi criada a partir de uma imagem do Azure marketplace, o agente será executado. Em alguns casos, por exemplo, se criar uma VM personalizada ou migrar uma máquina no local. poderá ter de instalar o agente manualmente. 

- Se tiver de instalar manualmente o agente da VM, siga as instruções para [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) ou [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) VMs. 
- Após o instalação do agente, quando ativa a cópia de segurança, o Azure Backup instala a extensão de cópia de segurança para o agente. -Atualizações e correções a extensão sem intervenção do utilizador.

## <a name="back-up-from-azure-vm-settings"></a>Criar cópias de segurança das definições de VM do Azure


1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Clique em **todos os serviços** e no filtro, escreva **máquinas virtuais**e, em seguida, clique em **máquinas virtuais**. 
3. Na lista de VMs, selecione a VM que pretende criar cópias de segurança.
4. No menu da VM, clique em **cópia de segurança**. 
5. Na **cofre dos serviços de recuperação**, efetue o seguinte procedimento:
  - Se já tiver um cofre, clique em **selecionar existente**e selecione um cofre.
  - Se não tiver um cofre, clique em **criar novo**. Especifique um nome para o cofre. Ele é criado no mesmo grupo região e recursos da VM. Não é possível modificar estas definições, quando ativa a cópia de segurança diretamente a partir das definições de VM.

  ![Assistente para Ativar Cópia de Segurança](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. Na **escolher política de cópia de segurança**, efetue o seguinte procedimento:

  - Deixe a política predefinida. Isso cria uma cópia de segurança da VM uma vez por dia ao período de tempo especificado e mantém cópias de segurança no cofre durante 30 dias.
  - Se tiver uma, selecione uma política de cópia de segurança existente.
  - Criar uma nova política e definir as definições de política.  

  ![Selecionar política de cópia de segurança](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Clique em **ativar cópia de segurança**. Isso associa a política de cópia de segurança com a VM. 

    ![Botão Ativar cópia de segurança](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. Pode controlar o progresso da configuração de notificações do portal.
9. Depois da tarefa é concluída, no menu da VM, clique em **cópia de segurança**. A página mostra o estado da cópia de segurança para a VM, informações sobre pontos de recuperação, tarefas em execução e alertas emitidas.

  ![Estado da cópia de segurança](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. Depois de ativar a cópia de segurança, é executada uma cópia de segurança inicial. Pode iniciar a cópia de segurança inicial imediatamente ou aguarde até que ele é iniciado em conformidade com o agendamento de cópia de segurança.
    - Até que a cópia de segurança inicial estiver concluída, o **última cópia de segurança status** é apresentado como **aviso (backup inicial pendente)**.
    - Para ver quando o próximo backup agendado será executado, clique no nome de política de cópia de segurança.
    
   

> [!NOTE]
> O Azure Backup cria um grupo de recursos separado (que não seja o grupo de recursos VM) para armazenar os pontos de restauro, com o formato de nomenclatura **AzureBackupRG_geography_number** (exemplo: AzureBackupRG_northeurope_1). Não deve bloquear este grupo de recursos.



## <a name="run-a-backup-immediately"></a>Executar imediatamente uma cópia de segurança 

1. Para executar uma cópia de segurança imediatamente, no menu da VM, clique em **cópia de segurança** > **agora a cópia de segurança**.

    ![Executar cópia de segurança](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. Na **cópia de segurança agora** usar o controle de calendário para selecionar até quando o ponto de recuperação será mantido > e **OK**.
  
    ![Dias de retenção de cópia de segurança](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. Notificações do portal informá-lo que a tarefa de cópia de segurança foi acionada. Para monitorizar o progresso de cópia de segurança, clique em **ver todas as tarefas**.




## <a name="back-up-from-the-recovery-services-vault"></a>Cópia de segurança do Cofre de serviços de recuperação

Siga as instruções neste artigo para ativar a cópia de segurança para VMs do Azure ao configurar um cofre dos serviços de recuperação de cópia de segurança do Azure e ativar a cópia de segurança no cofre.

## <a name="next-steps"></a>Passos Seguintes

- Se tiver dificuldades com qualquer um dos procedimentos neste artigo, consulte a [guia de resolução de problemas](backup-azure-vms-troubleshoot.md).
- [Saiba mais sobre](backup-azure-manage-vms.md) gerir as cópias de segurança.


---
title: Fazer backup de uma VM do Azure por meio das configurações da VM
description: Neste artigo, saiba como fazer backup de uma VM do Azure singular ou de várias VMs do Azure com o serviço de backup do Azure.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: bd75bdfb3fe9939c576fe289f6be38ebe875e020
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294070"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>Fazer backup de uma VM do Azure por meio das configurações da VM

Este artigo explica como fazer backup de VMs do Azure com o serviço de [backup do Azure](backup-overview.md) . Você pode fazer backup de VMs do Azure usando alguns métodos:

- Única VM do Azure: as instruções neste artigo descrevem como fazer backup de uma VM do Azure diretamente das configurações da VM.
- Várias VMs do Azure: você pode configurar um cofre de serviços de recuperação e configurar o backup para várias VMs do Azure. Siga as instruções neste [artigo](backup-azure-arm-vms-prepare.md) para este cenário.

## <a name="before-you-start"></a>Antes de começar

1. [Saiba](backup-architecture.md#how-does-azure-backup-work) como funciona o backup e [Verifique](backup-support-matrix.md#azure-vm-backup-support) os requisitos de suporte.
2. [Obtenha uma visão geral](backup-azure-vms-introduction.md) do backup de VM do Azure.

### <a name="azure-vm-agent-installation"></a>Instalação do agente de VM do Azure

Para fazer backup de VMs do Azure, o backup do Azure instala uma extensão no agente de VM em execução no computador. Se sua VM tiver sido criada a partir de uma imagem do Azure Marketplace, o agente estará em execução. Em alguns casos, por exemplo, se você criar uma VM personalizada ou migrar um computador do local. Talvez seja necessário instalar o agente manualmente.

- Se você precisar instalar o agente de VM manualmente, siga as instruções para VMs [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) ou [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) .
- Depois que o agente é instalado, quando você habilita o backup, o backup do Azure instala a extensão de backup para o agente. Ele atualiza e corrige a extensão sem intervenção do usuário.

## <a name="back-up-from-azure-vm-settings"></a>Fazer backup das configurações de VM do Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Clique em **todos os serviços** e, no filtro, digite **máquinas virtuais**e clique em **máquinas virtuais**.
3. Na lista de VMs, selecione a VM que você deseja fazer backup.
4. No menu VM, clique em **backup**.
5. No **cofre dos serviços de recuperação**, faça o seguinte:
   - Se você já tiver um cofre, clique em **selecionar existente**e selecione um cofre.
   - Se você não tiver um cofre, clique em **criar novo**. Especifique um nome para o cofre. Ele é criado na mesma região e no mesmo grupo de recursos que a VM. Não é possível modificar essas configurações quando você habilita o backup diretamente das configurações da VM.

   ![Assistente para Ativar Cópia de Segurança](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. Em **escolher política de backup**, faça o seguinte:

   - Deixe a política padrão. Isso faz backup da VM uma vez por dia no momento especificado e retém os backups no cofre por 30 dias.
   - Selecione uma política de backup existente se você tiver uma.
   - Crie uma nova política e defina as configurações de política.  

   ![Selecionar política de cópia de segurança](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Clique em **habilitar backup**. Isso associa a política de backup à VM.

    ![Botão Ativar cópia de segurança](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. Você pode acompanhar o progresso da configuração nas notificações do Portal.
9. Depois que o trabalho for concluído, no menu VM, clique em **backup**. A página mostra o status de backup da VM, informações sobre pontos de recuperação, trabalhos em execução e alertas emitidos.

   ![Estado da cópia de segurança](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. Depois de habilitar o backup, um backup inicial é executado. Você pode iniciar o backup inicial imediatamente ou aguardar até que ele comece de acordo com o agendamento de backup.
    - Até que o backup inicial seja concluído, o **status do último backup** será exibido como **aviso (backup inicial pendente)** .
    - Para ver quando o próximo backup agendado será executado, clique no nome da política de backup.

## <a name="run-a-backup-immediately"></a>Executar um backup imediatamente

1. Para executar um backup imediatamente, no menu VM, clique em **backup** > **Backup Now**.

    ![Executar backup](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. Em **backup agora** , use o controle Calendar para selecionar até que o ponto de recuperação seja retido > e **OK**.

    ![Dia de retenção de backup](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. As notificações do portal permitem que você saiba que o trabalho de backup foi disparado. Para monitorar o progresso do backup, clique em **Exibir todos os trabalhos**.

## <a name="back-up-from-the-recovery-services-vault"></a>Fazer backup do cofre dos serviços de recuperação

Siga as instruções neste artigo para habilitar o backup para VMs do Azure Configurando um cofre dos serviços de recuperação de backup do Azure e habilitando o backup no cofre.

>[!NOTE]
> O backup do Azure agora oferece suporte a backup e restauração de disco seletivo usando a solução de backup de máquina virtual do Azure.
>
>Hoje, o backup do Azure dá suporte ao backup de todos os discos (sistema operacional e dados) em uma VM em conjunto usando a solução de backup de máquina virtual. Com a funcionalidade excluir disco, você obtém uma opção para fazer backup de um ou alguns dos vários discos de dados em uma VM. Isso fornece uma solução eficiente e econômica para suas necessidades de backup e restauração. Cada ponto de recuperação contém dados dos discos incluídos na operação de backup, o que permite que você tenha um subconjunto de discos restaurados do ponto de recuperação fornecido durante a operação de restauração. Isso se aplica à restauração tanto do instantâneo quanto do cofre.
>
> Essa solução é particularmente útil nos seguintes cenários:
>  
>1. Você tem dados críticos para fazer backup em apenas um disco e não deseja fazer backup do restante dos discos anexados a uma VM. Isso minimiza os custos de armazenamento de backup.  
>2. Você tem outras soluções de backup para parte dos dados da VM. Por exemplo, você faz o backup de seus bancos de dados ou de sua capacidade com uma solução de backup de carga de trabalho diferente e deseja usar o backup de nível de VM do Azure para o restante dos seus discos e dados para criar um sistema eficiente e robusto utilizando os melhores recursos disponíveis.
>
>Para se inscrever na versão prévia, escreva para nós em AskAzureBackupTeam@microsoft.com

## <a name="next-steps"></a>Passos seguintes

- Se você tiver dificuldades com qualquer um dos procedimentos deste artigo, consulte o [Guia de solução de problemas](backup-azure-vms-troubleshoot.md).
- [Saiba mais sobre como](backup-azure-manage-vms.md) gerenciar seus backups.

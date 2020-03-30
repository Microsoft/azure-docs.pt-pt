---
title: Volte a fazer um VM Azure a partir das definições vm
description: Neste artigo, aprenda a apoiar um Azure VM singular ou vários VMs Azure com o serviço De backup Azure.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 72d6e5657add3e815bb0d77fadbdbc716712bee5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705450"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>Volte a fazer um VM Azure a partir das definições vm

Este artigo explica como apoiar os VMs Azure com o serviço [de backup Azure.](backup-overview.md) Pode fazer o back-up De VMs Azure utilizando um par de métodos:

- VM Azure único: As instruções deste artigo descrevem como fazer o backup de um VM Azure diretamente a partir das definições vm.
- Vários VMs Azure: Você pode configurar um cofre de Serviços de Recuperação e configurar backup para vários VMs Azure. Siga as instruções [deste artigo](backup-azure-arm-vms-prepare.md) para este cenário.

## <a name="before-you-start"></a>Antes de começar

1. [Saiba](backup-architecture.md#how-does-azure-backup-work) como funciona o backup e [verifique os](backup-support-matrix.md#azure-vm-backup-support) requisitos de suporte.
2. [Obtenha uma visão geral](backup-azure-vms-introduction.md) do backup Azure VM.

### <a name="azure-vm-agent-installation"></a>Instalação de agente Azure VM

Para fazer backup os VMs Azure, o Azure Backup instala uma extensão no agente VM em execução na máquina. Se o seu VM foi criado a partir de uma imagem de mercado Azure, o agente estará em execução. Em alguns casos, por exemplo, se criar um VM personalizado, ou se migrar uma máquina a partir do local. pode ser necessário instalar o agente manualmente.

- Se necessitar de instalar manualmente o agente VM, siga as instruções para [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) ou [VMs Linux.](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)
- Depois de instalado o agente, quando ativa a cópia de segurança, o Azure Backup instala a extensão de reserva ao agente. Atualiza e remende a extensão sem a intervenção do utilizador.

## <a name="back-up-from-azure-vm-settings"></a>Back up from Azure VM settings

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Clique em **todos os serviços** e no Filtro, escreva **máquinas virtuais,** e, em seguida, clique em **máquinas virtuais**.
3. A partir da lista de VMs selecione o VM que pretende fazer apoio.
4. No menu VM, clique em **Backup**.
5. No cofre dos Serviços de **Recuperação,** faça o seguinte:
   - Se já tiver um cofre, clique em **Selecionar existentee**selecione um cofre.
   - Se não tiver um cofre, clique em **Criar novo**. Especifique um nome para o cofre. É criado na mesma região e grupo de recursos que o VM. Não é possível modificar estas definições quando ativa a cópia de segurança diretamente a partir das definições vM.

   ![Assistente para Ativar Cópia de Segurança](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. Em Escolha a política de **backup,** faça o seguinte:

   - Deixe a política de incumprimento. Isto apoia o VM uma vez por dia na hora especificada, e retém cópias de segurança no cofre por 30 dias.
   - Selecione uma política de backup existente se tiver uma.
   - Crie uma nova política e defina as definições políticas.  

   ![Selecionar política de cópia de segurança](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Clique em **ativar a cópia de segurança**. Isto associa a política de backup com o VM.

    ![Botão Ativar cópia de segurança](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. Pode acompanhar o progresso da configuração nas notificações do portal.
9. Depois de concluída a função, no menu VM, clique em **Backup**. A página mostra o estado de backup para o VM, informações sobre pontos de recuperação, empregos em execução e alertas emitidos.

   ![Estado de reserva](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. Depois de permitir a cópia de segurança, uma cópia de segurança inicial corre. Pode iniciar o backup inicial imediatamente, ou esperar até que comece de acordo com o horário de reserva.
    - Até que a cópia de segurança inicial esteja concluída, o **último estado** de cópia de segurança mostra como Aviso (Cópia inicial **pendente)**.
    - Para ver quando será executada a próxima cópia de segurança programada, clique no nome da política de backup.

## <a name="run-a-backup-immediately"></a>Faça uma cópia de segurança imediatamente

1. Para executar uma cópia de segurança imediatamente, no menu VM, clique agora em **Backup** > **.**

    ![Executar backup](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. Em **Backup Agora** utilize o controlo do calendário para selecionar até quando o ponto de recuperação será mantido > e **OK**.

    ![Dia de retenção de backup](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. As notificações do portal permitem-lhe saber que o trabalho de reserva foi desencadeado. Para monitorizar o progresso da cópia de segurança, clique em **Ver todos os trabalhos**.

## <a name="back-up-from-the-recovery-services-vault"></a>Back up do cofre dos Serviços de Recuperação

Siga as instruções deste artigo para ativar o backup para Os VMs Azure, configurando um cofre azure backup Serviços de recuperação e permitindo backup no cofre.

>[!NOTE]
> **O Azure Backup suporta agora a cópia de segurança seletiva do disco e restaura utilizando a solução de backup da Máquina Virtual Azure.**
>
>Hoje em dia, o Azure Backup suporta o backup de todos os discos (Sistema Operativo e dados) num VM juntos utilizando a solução de backup da Máquina Virtual. Com a funcionalidade de exclusão de disco, obtém-se a opção de fazer backup de um ou alguns dos muitos discos de dados de um VM. Isto fornece uma solução eficiente e rentável para as suas necessidades de backup e restauro. Cada ponto de recuperação contém dados dos discos incluídos na operação de backup, o que permite ainda ter um subconjunto de discos restaurado sabotado a partir do ponto de recuperação dado durante a operação de restauro. Isto aplica-se para restaurar tanto a partir do instantâneo como do cofre.
>
>**Para se inscrever para a pré-visualização, escreva-nos emAskAzureBackupTeam@microsoft.com**

## <a name="next-steps"></a>Passos seguintes

- Se tiver dificuldades com algum dos procedimentos deste artigo, consulte o guia de [resolução](backup-azure-vms-troubleshoot.md)de problemas.
- [Aprenda a](backup-azure-manage-vms.md) gerir os seus backups.

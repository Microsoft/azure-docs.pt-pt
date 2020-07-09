---
title: Apoie um VM Azure a partir das definições de VM
description: Neste artigo, aprenda a apoiar um VM Azure singular ou vários VMs Azure com o serviço Azure Backup.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 72d6e5657add3e815bb0d77fadbdbc716712bee5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76705450"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>Apoie um VM Azure a partir das definições de VM

Este artigo explica como apoiar os VMs Azure com o serviço [Azure Backup.](backup-overview.md) Pode apoiar os VMs Azure utilizando um par de métodos:

- Single Azure VM: As instruções deste artigo descrevem como fazer o back up de um VM Azure diretamente a partir das definições de VM.
- Múltiplos VMs Azure: Pode configurar um cofre dos Serviços de Recuperação e configurar o backup para vários VMs Azure. Siga as instruções [deste artigo](backup-azure-arm-vms-prepare.md) para este cenário.

## <a name="before-you-start"></a>Antes de começar

1. [Saiba](backup-architecture.md#how-does-azure-backup-work) como funciona a cópia de segurança e [verifique os](backup-support-matrix.md#azure-vm-backup-support) requisitos de suporte.
2. [Obtenha uma visão geral](backup-azure-vms-introduction.md) do backup Azure VM.

### <a name="azure-vm-agent-installation"></a>Instalação do agente Azure VM

Para apoiar os VMs Azure, o Azure Backup instala uma extensão no agente VM em funcionamento na máquina. Se o seu VM foi criado a partir de uma imagem de mercado Azure, o agente estará em execução. Em alguns casos, por exemplo, se criar um VM personalizado, ou migrar uma máquina a partir do local. pode ser necessário instalar o agente manualmente.

- Se necessitar de instalar manualmente o agente VM, siga as instruções para [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) ou [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) VMs.
- Depois de o agente ser instalado, quando ativa a cópia de segurança, o Azure Backup instala a extensão de reserva ao agente. Atualiza e corrige a extensão sem a intervenção do utilizador.

## <a name="back-up-from-azure-vm-settings"></a>Recua das definições de Azure VM

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Clique em **Todos os serviços** e no Filtro, escreva **máquinas Virtuais**e, em seguida, clique em **máquinas Virtuais**.
3. Na lista de VMs selecione o VM que pretende fazer.
4. No menu VM, clique em **Cópia de Segurança**.
5. No **cofre dos Serviços de Recuperação,** faça o seguinte:
   - Se já tiver um cofre, clique em **Selecionar a existência**e selecione um cofre.
   - Se não tiver um cofre, clique em **Criar novo**. Especifique um nome para o cofre. É criado na mesma região e grupo de recursos que o VM. Não é possível modificar estas definições quando ativa a cópia de segurança diretamente a partir das definições de VM.

   ![Assistente para Ativar Cópia de Segurança](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. Na **política de backup,** faça o seguinte:

   - Deixe a política de incumprimento. Isto confirma o VM uma vez por dia no momento especificado, e retém cópias de segurança no cofre durante 30 dias.
   - Selecione uma política de backup existente se tiver uma.
   - Criar uma nova política e definir as definições de política.  

   ![Selecionar política de cópia de segurança](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Clique **em Ativar a Cópia de Segurança**. Isto associa a política de backup com o VM.

    ![Botão Ativar cópia de segurança](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. Pode rastrear o progresso da configuração nas notificações do portal.
9. Depois de concluído o trabalho, no menu VM, clique em **Backup**. A página mostra o estado de backup para o VM, informações sobre pontos de recuperação, empregos em execução e alertas emitidos.

   ![Estado da cópia de segurança](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. Depois de ativar o backup, uma cópia de segurança inicial é executado. Pode iniciar a cópia de segurança inicial imediatamente ou esperar que comece de acordo com o horário de reserva.
    - Até que a cópia de segurança inicial esteja concluída, o **último estado de backup** mostra como Aviso **(Cópia de Segurança Inicial pendente)**.
    - Para ver quando será executado o próximo backup programado, clique no nome da política de backup.

## <a name="run-a-backup-immediately"></a>Executar um backup imediatamente

1. Para executar uma cópia de segurança imediatamente, no menu VM, clique em **Backup**  >  **Backup agora**.

    ![Executar backup](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. Em **Backup Agora** utilize o controlo do calendário para selecionar até quando o ponto de recuperação for mantido > e **OK**.

    ![Dia de retenção de backup](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. As notificações do portal permitem-lhe saber que o trabalho de backup foi desencadeado. Para monitorizar o progresso da cópia de segurança, clique em **Ver todos os trabalhos**.

## <a name="back-up-from-the-recovery-services-vault"></a>Voltar do cofre dos Serviços de Recuperação

Siga as instruções deste artigo para permitir a cópia de segurança para os VMs Azure, criando um cofre Azure Backup Recovery Services e permitindo a cópia de segurança no cofre.

>[!NOTE]
> **O Azure Backup suporta agora a cópia de segurança seletiva do disco e restaura utilizando a solução de backup da Máquina Virtual Azure.**
>
>Hoje em dia, o Azure Backup suporta o backup de todos os discos (Sistema Operativo e dados) num VM em conjunto utilizando a solução de backup da Máquina Virtual. Com a funcionalidade de exclusão do disco, obtém-se uma opção de backup de um ou alguns dos muitos discos de dados num VM. Isto fornece uma solução eficiente e rentável para as suas necessidades de backup e restauro. Cada ponto de recuperação contém dados dos discos incluídos na operação de backup, o que permite ainda ter um subconjunto de discos restaurados a partir do ponto de recuperação dado durante a operação de restauro. Isto aplica-se para restaurar tanto do instantâneo como do cofre.
>
>**Para se inscrever para a pré-visualização, escreva-nos emAskAzureBackupTeam@microsoft.com**

## <a name="next-steps"></a>Próximos passos

- Se tiver dificuldades com algum dos procedimentos deste artigo, consulte o [guia de resolução de problemas](backup-azure-vms-troubleshoot.md).
- [Saiba como](backup-azure-manage-vms.md) gerir os seus backups.

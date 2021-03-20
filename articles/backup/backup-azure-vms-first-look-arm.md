---
title: Apoie um VM Azure a partir das definições de VM
description: Neste artigo, aprenda a apoiar um VM Azure singular ou vários VMs Azure com o serviço Azure Backup.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 55b71d2a2901cdde984df3ebfd68a2a643b78b74
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89667512"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>Apoie um VM Azure a partir das definições de VM

Este artigo explica como apoiar os VMs Azure com o serviço [Azure Backup.](backup-overview.md) Pode apoiar os VMs Azure utilizando um par de métodos:

- Single Azure VM: As instruções deste artigo descrevem como fazer o back up de um VM Azure diretamente a partir das definições de VM.
- Múltiplos VMs Azure: Pode configurar um cofre dos Serviços de Recuperação e configurar o backup para vários VMs Azure. Siga as instruções [deste artigo](backup-azure-arm-vms-prepare.md) para este cenário.

## <a name="before-you-start"></a>Antes de começar

1. [Saiba](backup-architecture.md#how-does-azure-backup-work) como funciona a cópia de segurança e [verifique os](backup-support-matrix.md#azure-vm-backup-support) requisitos de suporte.
2. [Obtenha uma visão geral](backup-azure-vms-introduction.md) do backup Azure VM.

### <a name="azure-vm-agent-installation"></a>Instalação do agente Azure VM

Para apoiar os VMs Azure, o Azure Backup instala uma extensão no agente VM em funcionamento na máquina. Se o seu VM foi criado a partir de uma imagem do Azure Marketplace, o agente estará em execução. Em alguns casos, por exemplo, se criar um VM personalizado, ou migrar uma máquina a partir do local, poderá ter de instalar o agente manualmente.

- Se necessitar de instalar manualmente o agente VM, siga as instruções para [Windows](../virtual-machines/extensions/agent-windows.md) ou [Linux](../virtual-machines/extensions/agent-linux.md) VMs.
- Depois de o agente ser instalado, quando ativa a cópia de segurança, o Azure Backup instala a extensão de reserva ao agente. Atualiza e corrige a extensão sem a intervenção do utilizador.

## <a name="back-up-from-azure-vm-settings"></a>Recua das definições de Azure VM

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione **Todos os serviços** e no Filtro, escreva **máquinas Virtuais** e, em seguida, selecione **máquinas Virtuais**.
3. Na lista de VMs, selecione o VM que pretende fazer.
4. No menu VM, selecione **Backup**.
5. No **cofre dos Serviços de Recuperação,** faça o seguinte:
   - Se já tiver um cofre, **selecione Selecione a existência** e selecione um cofre.
   - Se não tiver um cofre, selecione **Criar novo**. Especifique um nome para o cofre. É criado na mesma região e grupo de recursos que o VM. Não é possível modificar estas definições quando ativa a cópia de segurança diretamente a partir das definições de VM.

        ![Assistente para Ativar Cópia de Segurança](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. Na **política de backup,** faça uma das seguintes:

   - Deixe a política de incumprimento. Isto confirma o VM uma vez por dia no momento especificado, e retém cópias de segurança no cofre durante 30 dias.
   - Selecione uma política de backup existente se tiver uma.
   - Criar uma nova política e definir as definições de política.  

       ![Selecionar política de cópia de segurança](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Selecione **Ativar Cópia de Segurança**. Isto associa a política de backup com o VM.

    ![Botão Ativar cópia de segurança](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. Pode rastrear o progresso da configuração nas notificações do portal.
9. Depois de concluído o trabalho, no menu VM, selecione **Backup**. A página mostra o estado de backup para o VM, informações sobre pontos de recuperação, empregos em execução e alertas emitidos.

   ![Estado da cópia de segurança](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. Depois de ativar o backup, uma cópia de segurança inicial é executado. Pode iniciar a cópia de segurança inicial imediatamente ou esperar que comece de acordo com o horário de reserva.
    - Até que a cópia de segurança inicial esteja concluída, o **último estado de backup** mostra como Aviso **(Cópia de Segurança Inicial pendente)**.
    - Para ver quando será executado o próximo backup programado, selecione o nome da política de backup.

## <a name="run-a-backup-immediately"></a>Executar um backup imediatamente

1. Para executar uma cópia de segurança imediatamente, no menu VM, selecione  >  **Backup Backup agora**.

    ![Executar backup](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. In **Backup Now**, utilize o controlo do calendário para selecionar até quando o ponto de recuperação for mantido > e **OK**.

    ![Dia de retenção de backup](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. As notificações do portal permitem-lhe saber que o trabalho de backup foi desencadeado. Para monitorizar o progresso da cópia de segurança, **selecione Ver todos os trabalhos**.

## <a name="back-up-from-the-recovery-services-vault"></a>Voltar do cofre dos Serviços de Recuperação

Siga as instruções [deste artigo](backup-azure-arm-vms-prepare.md) para permitir a cópia de segurança para os VMs Azure, criando um cofre Azure Backup Recovery Services e permitindo a cópia de segurança no cofre.

## <a name="next-steps"></a>Passos seguintes

- Se tiver dificuldades com algum dos procedimentos deste artigo, consulte o [guia de resolução de problemas](backup-azure-vms-troubleshoot.md).
- [Saiba como](backup-azure-manage-vms.md) gerir os seus backups.

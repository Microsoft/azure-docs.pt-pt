---
title: Utilizar o Azure Backup para restaurar as bases de dados de cópia de segurança do SQL Server numa VM do Azure | Documentos da Microsoft
description: Este artigo descreve como restaurar bases de dados do SQL Server que estão em execução numa VM do Azure e que estão uma cópia de segurança com o Azure Backup.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: raynew
ms.openlocfilehash: 1712e46494796e563c26316b4f45d968872c304f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57996757"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Restaurar bases de dados do SQL Server em VMs do Azure

Este artigo descreve como restaurar uma base de dados do SQL Server que está em execução numa máquina virtual do Azure (VM) que o [Azure Backup](backup-overview.md) serviço tem uma cópia de segurança para um cofre dos serviços de recuperação de cópia de segurança do Azure.

Este artigo descreve como restaurar bases de dados do SQL Server. Para obter mais informações, consulte [cópia de segurança de bases de dados do SQL Server em VMs do Azure](backup-azure-sql-database.md).

## <a name="restore-to-a-time-or-a-recovery-point"></a>Restaurar para um período de tempo ou de um ponto de recuperação

Cópia de segurança do Azure, pode restaurar bases de dados do SQL Server em execução em VMs do Azure da seguinte forma:

- Restaure para uma data específica ou a hora (para o segundo) com os backups de log de transação. O Azure Backup determina automaticamente a cópia de segurança completa, diferencial adequada e a cadeia de cópias de segurança de registo que são necessários para restaurar com base no tempo selecionado.
- Restaure um backup completo ou diferencial específico para restaurar para um ponto de recuperação específico.


## <a name="prerequisites"></a>Pré-requisitos

Antes de restaurar uma base de dados, tenha em atenção o seguinte:

- Pode restaurar a base de dados para uma instância do SQL Server na mesma região do Azure.
- O servidor de destino tem de estar registado no mesmo cofre como origem.
- Para restaurar uma base de dados encriptados TDE para outro SQL Server, precisa primeiro [restaurar o certificado para o servidor de destino](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
- Antes de restaurar a base de dados "Mestra", iniciar a instância do SQL Server no modo de utilizador único utilizando a opção de arranque **-m AzureWorkloadBackup**.
    - O valor para **-m** é o nome do cliente.
    - Apenas o nome de cliente especificado pode abrir a ligação.
- Para todas as bases de dados do sistema (modelo, mestra, msdb), pare o serviço de agente do SQL Server antes de acionar o restauro.
- Feche todas as aplicações que poderão tentar tomar uma ligação a qualquer uma destas bases de dados.

## <a name="restore-a-database"></a>Restaurar uma base de dados

Para restaurar, necessita das seguintes permissões:

* **Operador de cópia de segurança** permissões no cofre em que está fazendo o restauro.
* **Contribuidor (escrita)** acesso à origem de VM que é uma cópia de segurança.
* **Contribuidor (escrita)** acesso para a VM de destino:
    - Se estiver a restaurar para a mesma VM, esta é a VM de origem.
    - Se estiver a restaurar para uma localização alternativa, esta é a nova VM de destino.

Restaure da seguinte forma:
1. Abra o Cofre no qual a VM do SQL Server está registado.
2. No dashboard do cofre, sob **utilização**, selecione **itens de cópia de segurança**.
3. Na **itens de cópia de segurança**, em **tipo de gestão de cópia de segurança**, selecione **SQL na VM do Azure**.

    ![Selecione o SQL numa VM do Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Selecione a base de dados para restaurar.

    ![Selecione a base de dados para restaurar](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Reveja o menu de base de dados. Fornece informações sobre a cópia de segurança da base de dados, incluindo:

    * Pontos de restauro mais antigo e mais recentes.
    * O estado de cópia de segurança do registo nas últimas 24 horas para bases de dados que estão em modo de recuperação completa e registadas em massa e que estão configurados para cópias de segurança do registo transacional.

6. Selecione **restaurar DB**.

    ![Selecione restaurar DB](./media/backup-azure-sql-database/restore-db-button.png)

7. Na **restaurar configuração**, especifique onde pretende restaurar os dados:
   - **Alternativo de localização**: Restaurar a base de dados para uma localização alternativa e manter a base de dados de origem original.
   - **Substituir a BD**: Restaure os dados para a mesma instância do SQL Server como a origem original. Esta opção substitui a base de dados original.

     > [!Important]
     > Se a base de dados selecionada pertence a um grupo de disponibilidade Always On, o SQL Server não permite que a base de dados sejam substituídas. Apenas **localização alternativa** está disponível.
     >

     ![Restaurar o menu de configuração](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Restaurar para uma localização alternativa

1. Na **restaurar Configuration** menu, em **restaurar onde**, selecione **localização alternativa**.
2. Selecione o nome do SQL Server e a instância à qual pretende restaurar a base de dados.
3. Na **nome de BD restaurada** , introduza o nome da base de dados de destino.
4. Se aplicável, selecione **substituir se a BD com o mesmo nome já existe na instância SQL selecionada**.
5. Selecione **OK**.

    ![Fornecer valores para o menu de restaurar a configuração](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. Na **selecionar ponto de restauro**, selecione se pretende [restaurar para um ponto específico no tempo](#restore-to-a-specific-point-in-time) ou a [restaurar para um ponto de recuperação específica](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > O restauro de ponto no tempo só está disponível para backups de log para bases de dados que estejam no modo de recuperação completa e registadas em massa.

### <a name="restore-and-overwrite"></a>Restaurar e substituir

1. Na **restaurar Configuration** menu, em **restaurar onde**, selecione **substituir DB** > **OK**.

    ![Selecione substituir a BD](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. Na **selecionar ponto de restauro**, selecione **registos (ponto no tempo)** para [restaurar para um ponto específico no tempo](#restore-to-a-specific-point-in-time). Ou selecione **completa e diferencial** restaurar para um [ponto de recuperação específico](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > O restauro de ponto no tempo só está disponível para backups de log para bases de dados que estejam no modo de recuperação completa e registadas em massa.

### <a name="restore-to-a-specific-point-in-time"></a>Restaurar para um ponto específico no tempo

Se tiver selecionado **registos (ponto no tempo)** como o tipo de restauro, efetue o seguinte procedimento:

1.  Sob **restaurar data/hora**, abrir o calendário. No calendário, as datas que têm pontos de recuperação são apresentadas no tipo de negrito e a data atual é realçada.
1. Selecione uma data com pontos de recuperação. Não é possível selecionar datas com nenhum ponto de recuperação.

    ![Abrir o calendário](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. Depois de selecionar uma data, o gráfico de linha cronológica mostra os pontos de recuperação disponíveis num intervalo contínuo.
1. Especificar uma hora para a recuperação no gráfico de linha de tempo ou selecione uma hora. Em seguida, selecione **OK**.

    ![Selecione um tempo de restauro](./media/backup-azure-sql-database/recovery-point-logs-graph.png)


1. Sobre o **configuração avançada** menu, se pretender manter a base de dados nonoperational após o restauro, ative **restaurar com NORECOVERY**.
1. Se pretender alterar a localização de restauro no servidor de destino, introduza um novo caminho de destino.
1. Selecione **OK**.

    ![menu de configuração avançada](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. Sobre o **restaurar** menu, selecione **restaurar** para iniciar a tarefa de restauro.
1. Acompanhar o progresso de restauro no **notificações** área, ou controlá-lo ao selecionar **restaurar trabalhos** no menu da base de dados.

    ![Restaurar o progresso da tarefa](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>Restaurar para um ponto de restauro específico

Se tiver selecionado **completa e diferencial** como o tipo de restauro, efetue o seguinte procedimento:

1. Selecione um ponto de recuperação da lista e selecione **OK** para concluir o procedimento de ponto de restauro.

    ![Escolha um ponto de recuperação completo](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

1. Sobre o **configuração avançada** menu, se pretender manter a base de dados nonoperational após o restauro, ative **restaurar com NORECOVERY**.
1. Se pretender alterar a localização de restauro no servidor de destino, introduza um novo caminho de destino.
1. Selecione **OK**.

    ![Menu de configuração avançada](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. Sobre o **restaurar** menu, selecione **restaurar** para iniciar a tarefa de restauro.
1. Acompanhar o progresso de restauro no **notificações** área, ou controlá-lo ao selecionar **restaurar trabalhos** no menu da base de dados.

    ![Restaurar o progresso da tarefa](./media/backup-azure-sql-database/restore-job-notification.png)

## <a name="next-steps"></a>Passos Seguintes

[Gerir e monitorizar](manage-monitor-sql-database-backup.md) bases de dados do SQL Server que são uma cópia de segurança pelo Azure Backup.

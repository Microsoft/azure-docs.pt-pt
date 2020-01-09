---
title: Restaurar bancos de dados SQL Server em uma VM do Azure
description: Este artigo descreve como restaurar SQL Server bancos de dados que estão em execução em uma VM do Azure e cujo backup é feito com o backup do Azure.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 58525069af28be250c3536db076a38fb350bc1da
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75390751"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Restaurar bancos de dados SQL Server em VMs do Azure

Este artigo descreve como restaurar um banco de dados SQL Server que está em execução em uma VM (máquina virtual) do Azure em que o backup do serviço [do Azure](backup-overview.md) foi feito em um cofre dos serviços de recuperação de backup do Azure.

Este artigo descreve como restaurar bancos de dados do SQL Server. Para obter mais informações, consulte [fazer backup de bancos de dados SQL Server em VMs do Azure](backup-azure-sql-database.md).

## <a name="restore-to-a-time-or-a-recovery-point"></a>Restaurar para um momento ou um ponto de recuperação

O backup do Azure pode restaurar SQL Server bancos de dados que estão em execução em VMs do Azure da seguinte maneira:

- Restaurar para uma data ou hora específica (para o segundo) usando backups de log de transações. O backup do Azure determina automaticamente o backup diferencial completo apropriado e a cadeia de backups de log que são necessários para restaurar com base na hora selecionada.
- Restaure um backup completo ou diferencial específico para restaurar para um ponto de recuperação específico.

## <a name="prerequisites"></a>Pré-requisitos

Antes de restaurar um banco de dados, observe o seguinte:

- Você pode restaurar o banco de dados para uma instância de um SQL Server na mesma região do Azure.
- O servidor de destino deve ser registrado no mesmo cofre que a origem.
- Para restaurar um banco de dados criptografado com TDE para outro SQL Server, primeiro você precisa [restaurar o certificado para o servidor de destino](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
- Antes de restaurar o banco de dados "mestre", inicie a instância de SQL Server no modo de usuário único usando a opção de inicialização **-m AzureWorkloadBackup**.
  - O valor de **-m** é o nome do cliente.
  - Somente o nome do cliente especificado pode abrir a conexão.
- Para todos os bancos de dados do sistema (modelo, mestre, msdb), interrompa o serviço de SQL Server Agent antes de disparar a restauração.
- Feche todos os aplicativos que possam tentar fazer uma conexão com qualquer um desses bancos de dados.
- Se você tiver várias instâncias em execução em um servidor, todas as instâncias deverão estar ativas e em execução, caso contrário, o servidor não aparecerá na lista de servidores de destino para os quais você deseja restaurar o banco de dados.

## <a name="restore-a-database"></a>Restaurar uma base de dados

Para restaurar, você precisa das seguintes permissões:

- Permissões de **operador de backup** no cofre em que você está fazendo a restauração.
- Acesso de **colaborador (gravação)** à VM de origem cujo backup foi feito.
- Acesso de **colaborador (gravação)** à VM de destino:
  - Se você estiver restaurando para a mesma VM, esta é a VM de origem.
  - Se você estiver restaurando para um local alternativo, essa será a nova VM de destino.

Restaure da seguinte maneira:

1. Abra o cofre no qual a VM SQL Server está registrada.
2. No painel do cofre, em **uso**, selecione **itens de backup**.
3. Em **itens de backup**, em **tipo de gerenciamento de backup**, selecione **SQL na VM do Azure**.

    ![Selecionar SQL na VM do Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Selecione o banco de dados a ser restaurado.

    ![Selecionar o banco de dados a ser restaurado](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Examine o menu banco de dados. Ele fornece informações sobre o backup do banco de dados, incluindo:

    - Os pontos de restauração mais antigos e mais recentes.
    - O status de backup de log das últimas 24 horas para bancos de dados que estão no modo de recuperação completa e bulk-logged e configurados para backups de log transacionais.

6. Selecione **restaurar**.

    ![Selecione restaurar](./media/backup-azure-sql-database/restore-db.png)

7. Em **Restaurar configuração**, especifique onde (ou como) restaurar os dados:
   - **Local alternativo**: restaure o banco de dados para um local alternativo e mantenha o banco de dados de origem original.
   - **Substituir BD**: restaure os dados para a mesma instância de SQL Server como a fonte original. Essa opção substitui o banco de dados original.

    > [!IMPORTANT]
    > Se o banco de dados selecionado pertencer a um grupo de disponibilidade Always On, SQL Server não permitirá que o banco de dados seja substituído. Somente o **local alternativo** está disponível.
    >
   - **Restaurar como arquivos**: em vez de restaurar como um banco de dados, restaure os arquivos de backup que podem ser recuperados como um banco de dados posteriormente em qualquer computador em que os arquivos estejam presentes usando SQL Server Management Studio.
     ![menu de configuração de restauração](./media/backup-azure-sql-database/restore-configuration.png)

### <a name="restore-to-an-alternate-location"></a>Restaurar em um local alternativo

1. No menu **configuração de restauração** , em **onde restaurar**, selecione **local alternativo**.
2. Selecione o nome de SQL Server e a instância para a qual você deseja restaurar o banco de dados.
3. Na caixa **nome do BD restaurado** , digite o nome do banco de dados de destino.
4. Se aplicável, selecione **substituir se o banco de BD com o mesmo nome já existir na instância do SQL selecionada**.
5. Selecione **OK**.

    ![Fornecer valores para o menu de configuração de restauração](./media/backup-azure-sql-database/restore-configuration.png)

6. Em **selecionar ponto de restauração**, selecione se deseja [restaurar para um ponto específico no tempo](#restore-to-a-specific-point-in-time) ou para [restaurar para um ponto de recuperação específico](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > A restauração pontual está disponível apenas para backups de log de bancos de dados que estão no modo de recuperação completa e bulk-logged.

### <a name="restore-and-overwrite"></a>Restaurar e substituir

1. No menu **configuração de restauração** , em **onde restaurar**, selecione **substituir DB** > **OK**.

    ![Selecione substituir BD](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. Em **selecionar ponto de restauração**, selecione **logs (ponto no tempo)** para [restaurar para um ponto específico no tempo](#restore-to-a-specific-point-in-time). Ou selecione a **& total diferencial** para restaurar para um [ponto de recuperação específico](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > A restauração pontual está disponível apenas para backups de log de bancos de dados que estão no modo de recuperação completa e bulk-logged.

### <a name="restore-as-files"></a>Restaurar como arquivos

Para restaurar os dados de backup como arquivos. bak, em vez de em um banco de dados, escolha **restaurar como arquivos**. Depois que os arquivos são despejados em um caminho especificado, você pode pegar esses arquivos em qualquer computador em que deseja restaurá-los como um banco de dados. Em virtude de poder mover esses arquivos para qualquer computador, agora você pode restaurar os dados entre assinaturas e regiões.

1. No menu **configuração de restauração** , em **onde restaurar**, selecione **restaurar como arquivos**.
2. Selecione o nome de SQL Server para o qual você deseja restaurar os arquivos de backup.
3. No **caminho de destino no servidor** , insira o caminho da pasta no servidor selecionado na etapa 2. Esse é o local onde o serviço irá despejar todos os arquivos de backup necessários. Normalmente, um caminho de compartilhamento de rede, ou caminho de um compartilhamento de arquivos do Azure montado quando especificado como o caminho de destino, permite o acesso mais fácil a esses arquivos por outros computadores na mesma rede ou com o mesmo compartilhamento de arquivos do Azure montado neles.<BR>

>Para restaurar os arquivos de backup de banco de dados em um compartilhamento de arquivos do Azure montado na VM registrada de destino, verifique se NT AUTHORITY\SYSTEM tem acesso ao compartilhamento de arquivos. Você pode executar as etapas fornecidas abaixo para conceder as permissões de leitura/gravação para o AFS montado na VM:
>- Execute `PsExec -s cmd` para entrar no Shell NT AUTHORITY\SYSTEM
>   - Execute `cmdkey /add:<storageacct>.file.core.windows.net /user:AZURE\<storageacct> /pass:<storagekey>`
>   - Verificar o acesso com `dir \\<storageacct>.file.core.windows.net\<filesharename>`
>- Disparar uma restauração como arquivos do cofre de backup para `\\<storageacct>.file.core.windows.net\<filesharename>` como o caminho<BR>
Você pode baixar o PsExec por meio do <https://docs.microsoft.com/sysinternals/downloads/psexec>

4. Selecione **OK**.

![Selecione restaurar como arquivos](./media/backup-azure-sql-database/restore-as-files.png)

5. Selecione o **ponto de restauração** correspondente ao qual todos os arquivos. bak disponíveis serão restaurados.

![Selecionar um ponto de restauração](./media/backup-azure-sql-database/restore-point.png)

6. Todos os arquivos de backup associados ao ponto de recuperação selecionado são despejados no caminho de destino. Você pode restaurar os arquivos como um banco de dados em qualquer computador em que estejam presentes usando SQL Server Management Studio.

![Arquivos de backup restaurados no caminho de destino](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>Restaurar para um ponto específico no tempo

Se você tiver selecionado **logs (ponto no tempo)** como o tipo de restauração, faça o seguinte:

1. Em **restaurar data/hora**, abra o calendário. No calendário, as datas que têm pontos de recuperação são exibidas em negrito e a data atual é realçada.
1. Selecione uma data que tenha pontos de recuperação. Não é possível selecionar datas que não tenham pontos de recuperação.

    ![Abrir o calendário](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. Depois de selecionar uma data, o grafo de linha do tempo exibe os pontos de recuperação disponíveis em um intervalo contínuo.
1. Especifique uma hora para a recuperação no grafo da linha do tempo ou selecione uma hora. Em seguida, selecione **OK**.

    ![Selecionar um tempo de restauração](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

1. No menu **Configuração avançada** , se você quiser manter o banco de dados não operacional após a restauração, habilite **RESTORE WITH NORECOVERY**.
1. Se você quiser alterar o local de restauração no servidor de destino, insira um novo caminho de destino.
1. Selecione **OK**.

    ![Menu de configuração avançada](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. No menu **restaurar** , selecione **restaurar** para iniciar o trabalho de restauração.
1. Acompanhe o progresso da restauração na área **notificações** ou acompanhe-a selecionando **restaurar trabalhos** no menu banco de dados.

    ![Progresso do trabalho de restauração](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>Restaurar para um ponto de restauração específico

Se você tiver selecionado **& diferencial completo** como o tipo de restauração, faça o seguinte:

1. Selecione um ponto de recuperação na lista e selecione **OK** para concluir o procedimento de ponto de restauração.

    ![Escolher um ponto de recuperação completo](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

1. No menu **Configuração avançada** , se você quiser manter o banco de dados não operacional após a restauração, habilite **RESTORE WITH NORECOVERY**.
1. Se você quiser alterar o local de restauração no servidor de destino, insira um novo caminho de destino.
1. Selecione **OK**.

    ![Menu de configuração avançada](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. No menu **restaurar** , selecione **restaurar** para iniciar o trabalho de restauração.
1. Acompanhe o progresso da restauração na área **notificações** ou acompanhe-a selecionando **restaurar trabalhos** no menu banco de dados.

    ![Progresso do trabalho de restauração](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-databases-with-large-number-of-files"></a>Restaurar bancos de dados com um grande número de arquivos

Se o tamanho total da cadeia de caracteres de arquivos em um banco de dados for maior que um [limite específico](backup-sql-server-azure-troubleshoot.md#size-limit-for-files), o backup do Azure armazenará a lista de arquivos de banco de dados em um componente Pit diferente, de modo que não será possível definir o caminho de restauração de destino durante a operação de restauração. Em vez disso, os arquivos serão restaurados no caminho padrão do SQL.

  ![Restaurar banco de dados com arquivo grande](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="next-steps"></a>Passos seguintes

[Gerenciar e monitorar](manage-monitor-sql-database-backup.md) SQL Server bancos de dados que são submetidos a backup pelo backup do Azure.

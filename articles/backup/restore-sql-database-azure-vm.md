---
title: Restaurar as bases de dados do Servidor SQL num VM Azure
description: Este artigo descreve como restaurar as bases de dados do SQL Server que estão a funcionar num VM Azure e que estão apoiadas com o Azure Backup.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 642476c98ca223da01bda5c6eb79ee9b53732468
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252457"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Restaurar as bases de dados do Servidor SQL em VMs Azure

Este artigo descreve como restaurar uma base de dados do SQL Server que está a funcionar numa máquina virtual Azure (VM) que o serviço [de backup Azure](backup-overview.md) tem apoiado num cofre dos Serviços de Recuperação de Backup Azure.

Este artigo descreve como restaurar as bases de dados do SQL Server. Para mais informações, consulte as [bases de dados do SQL Server nas VMs Azure](backup-azure-sql-database.md).

## <a name="restore-to-a-time-or-a-recovery-point"></a>Restaurar um tempo ou um ponto de recuperação

A Backup Azure pode restaurar as bases de dados do SQL Server que estão a funcionar em VMs Azure da seguinte forma:

- Restaurar para uma data ou hora específica (para a segunda) utilizando cópias de segurança de registo de transações. A Cópia de Segurança Azure determina automaticamente a cópia de segurança diferencial adequada e a cadeia de backups de registo que são necessárias para restaurar com base no tempo selecionado.
- Restaurar uma cópia de segurança completa ou diferencial específica para restaurar um ponto de recuperação específico.

## <a name="prerequisites"></a>Pré-requisitos

Antes de restaurar uma base de dados, note o seguinte:

- Pode restaurar a base de dados numa instância de um Servidor SQL na mesma região do Azure.
- O servidor de destino deve estar registado no mesmo cofre que a fonte.
- Para restaurar uma base de dados encriptada por TDE para outro Servidor SQL, é necessário [primeiro restaurar o certificado no servidor de destino](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
- Antes de restaurar a base de dados "master", inicie a instância Do Servidor SQL no modo de utilizador único utilizando a opção de arranque **-m AzureWorkloadBackup**.
  - O valor para **-m** é o nome do cliente.
  - Só o nome do cliente especificado pode abrir a ligação.
- Para todas as bases de dados do sistema (modelo, mestre, msdb), pare o serviço sQL Server Agent antes de acionar o restauro.
- Feche todas as aplicações que possam tentar estabelecer uma ligação a qualquer uma destas bases de dados.
- Se tiver várias instâncias a funcionar num servidor, todas as instâncias devem estar a funcionar, caso contrário, o servidor não apareceria na lista de servidores de destino para que possa restaurar a base de dados.

## <a name="restore-a-database"></a>Restaurar uma base de dados

Para restaurar, precisa das seguintes permissões:

- **O operador** de reserva permissões no cofre onde está a fazer o restauro.
- **O colaborador (escreva)** acesso à fonte VM que está apoiada.
- **Acesso do contribuinte (escreva)** ao VM-alvo:
  - Se está saqueando para o mesmo VM, esta é a fonte VM.
  - Se está a restaurar para um local alternativo, este é o novo alvo VM.

Restaurar da seguinte forma:

1. Abra o cofre no qual o VM do Servidor SQL está registado.
2. No painel do cofre, sob **utilização,** selecione itens de **backup**.
3. Em **itens de backup,** em tipo de **gestão de backup,** selecione **SQL em VM Azure**.

    ![Selecione SQL em Azure VM](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Selecione a base de dados para restaurar.

    ![Selecione a base de dados para restaurar](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Reveja o menu da base de dados. Fornece informações sobre a cópia de segurança da base de dados, incluindo:

    - Os pontos de restauro mais antigos e últimos.
    - O estado de backup de registo nas últimas 24 horas para bases de dados que estão em modo de recuperação completa e a granel e que estão configurados para cópias de segurança de registo transacional.

6. Selecione **Restaurar**.

    ![Selecione Restaurar](./media/backup-azure-sql-database/restore-db.png)

7. Na **Configuração de Restauro,** especifique onde (ou como) restaurar os dados:
   - **Localização Alternativa**: Restaurar a base de dados num local alternativo e manter a base de dados original de origem.
   - **Sobrepor DB**: Restaurar os dados na mesma instância do Servidor SQL que a fonte original. Esta opção substitui a base de dados original.

    > [!IMPORTANT]
    > Se a base de dados selecionada pertencer a um grupo de disponibilidade Always On, o SQL Server não permite que a base de dados seja substituída. Apenas está disponível **a Localização Alternativa.**
    >
   - **Restaurar como ficheiros**: Em vez de restaurar como base de dados, restaure os ficheiros de backup que podem ser recuperados como base de dados mais tarde em qualquer máquina onde os ficheiros estejam presentes usando o Estúdio de Gestão de Servidores SQL.
     ![restaurar o menu de configuração](./media/backup-azure-sql-database/restore-configuration.png)

### <a name="restore-to-an-alternate-location"></a>Restaurar para um local alternativo

1. No menu **de configuração restaurar,** em **onde restaurar,** selecione **Localização Alternativa**.
2. Selecione o nome e a instância do Servidor SQL para a qual pretende restaurar a base de dados.
3. Na caixa **de nomes DB restaurados,** introduza o nome da base de dados do alvo.
4. Se aplicável, **selecione Sobreescrever se o DB com o mesmo nome já existir na instância SQL selecionada**.
5. Selecione **OK**.

    ![Fornecer valores para o menu de configuração de restauro](./media/backup-azure-sql-database/restore-configuration.png)

6. No **ponto de restauro Select,** selecione se deve restaurar a um ponto específico no [tempo](#restore-to-a-specific-point-in-time) ou para restaurar para um ponto [de recuperação específico](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > A restauração pontual está disponível apenas para cópias de segurança de registo para bases de dados que se encontram em modo de recuperação completa e a granel.

### <a name="restore-and-overwrite"></a>Restaurar e substituir

1. No menu **de configuração restaurar,** em **onde restaurar,** selecione **Overwrite DB** > **OK**.

    ![Selecione Sobreescrever DB](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. No **ponto de restauração Selecione** **Registos (Ponto no Tempo)** para [restaurar a um ponto específico no tempo](#restore-to-a-specific-point-in-time). Ou selecione **Full & Diferencial** para restaurar um ponto de [recuperação específico](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > A restauração pontual está disponível apenas para cópias de segurança de registo para bases de dados que se encontram em modo de recuperação completa e a granel.

### <a name="restore-as-files"></a>Restaurar como ficheiros

Para restaurar os dados de backup como ficheiros .bak em vez de uma base de dados, escolha **Restaurar como Ficheiros**. Uma vez que os ficheiros sejam despejados num caminho especificado, pode levar estes ficheiros a qualquer máquina onde pretenda restaurá-los como base de dados. Em virtude de poder mover estes ficheiros para qualquer máquina, pode agora restaurar os dados através de subscrições e regiões.

1. No menu **de configuração restaurar,** em **onde restaurar,** selecione **Restaurar como ficheiros**.
2. Selecione o nome SQL Server para o qual pretende restaurar os ficheiros de backup.
3. No **caminho destino no servidor** insere o caminho da pasta no servidor selecionado no passo 2. Este é o local onde o serviço irá despejar todos os ficheiros de reserva necessários. Normalmente, um caminho de partilha de rede, ou caminho de uma partilha de ficheiros Azure montado quando especificado como o caminho de destino, permite um acesso mais fácil a estes ficheiros por outras máquinas na mesma rede ou com a mesma partilha de ficheiros Azure montada neles.<BR>

    >Para restaurar os ficheiros de backup da base de dados de uma Partilha de Ficheiros Azure montada no VM registado no alvo, certifique-se de que o NT AUTHORITY\SYSTEM tem acesso à parte do ficheiro. Pode executar os passos abaixo indicados para conceder as permissões de leitura/escrita à AFS montada no VM:
    >
    >- Executar `PsExec -s cmd` para entrar na concha NT AUTHORITY\SYSTEM
    >   - Execute `cmdkey /add:<storageacct>.file.core.windows.net /user:AZURE\<storageacct> /pass:<storagekey>`
    >   - Verifique o acesso com `dir \\<storageacct>.file.core.windows.net\<filesharename>`
    >- Inicie um restauro como ficheiros do Cofre de Reserva para `\\<storageacct>.file.core.windows.net\<filesharename>` como o caminho<BR>
    Você pode baixar Psexec via <https://docs.microsoft.com/sysinternals/downloads/psexec>

4. Selecione **OK**.

    ![Selecione Restaurar como ficheiros](./media/backup-azure-sql-database/restore-as-files.png)

5. Selecione o **Ponto de Restauro** correspondente ao qual todos os ficheiros .bak disponíveis serão restaurados.

    ![Selecione um ponto de restauro](./media/backup-azure-sql-database/restore-point.png)

6. Todos os ficheiros de backup associados ao ponto de recuperação selecionado são despejados no caminho do destino. Pode restaurar os ficheiros como base de dados em qualquer máquina que estejam presentes na utilização do Estúdio de Gestão de Servidores SQL.

    ![Arquivos de backup restaurados no caminho do destino](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>Restaurar a um ponto específico no tempo

Se selecionou **os Registos (Ponto no Tempo)** como o tipo de restauro, faça o seguinte:

1. Em **data/hora de restauro,** abra o calendário. No calendário, as datas que têm pontos de recuperação são apresentadas em tipo arrojado, e a data atual é destacada.
1. Selecione uma data que tenha pontos de recuperação. Não se pode selecionar datas que não têm pontos de recuperação.

    ![Abra o calendário](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. Depois de selecionar uma data, o gráfico de linha do tempo mostra os pontos de recuperação disponíveis numa gama contínua.
1. Especifique um tempo para a recuperação no gráfico da linha do tempo ou selecione um tempo. Em seguida, selecione **OK**.

    ![Selecione um tempo de restauração](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

1. No menu **Configuração Avançada,** se pretender manter a base de dados não operacional após a restauração, ative **restaurar com NORECOVERY**.
1. Se quiser alterar a localização de restauro no servidor de destino, introduza um novo caminho-alvo.
1. Selecione **OK**.

    ![Menu de configuração avançado](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. No menu **Restaurar,** selecione **Restaurar** para iniciar o trabalho de restauro.
1. Acompanhe o progresso da restauração na área de **Notificações** ou rastreie-o selecionando **trabalhos de restauro** no menu de base de dados.

    ![Restaurar o progresso do trabalho](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>Restaurar para um ponto de restauro específico

Se selecionou **Full & Diferencial** como o tipo de restauro, faça o seguinte:

1. Selecione um ponto de recuperação da lista e selecione **OK** para completar o procedimento do ponto de restauro.

    ![Escolha um ponto de recuperação completo](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

    >[!NOTE]
    > Por predefinição, são apresentados pontos de recuperação dos últimos 30 dias. Pode exibir pontos de recuperação com mais de 30 dias clicando em **Filtro** e selecionando uma gama personalizada.

1. No menu **Configuração Avançada,** se pretender manter a base de dados não operacional após a restauração, ative **restaurar com NORECOVERY**.
1. Se quiser alterar a localização de restauro no servidor de destino, introduza um novo caminho-alvo.
1. Selecione **OK**.

    ![Menu de Configuração Avançada](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. No menu **Restaurar,** selecione **Restaurar** para iniciar o trabalho de restauro.
1. Acompanhe o progresso da restauração na área de **Notificações** ou rastreie-o selecionando **trabalhos de restauro** no menu de base de dados.

    ![Restaurar o progresso do trabalho](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-databases-with-large-number-of-files"></a>Restaurar bases de dados com um grande número de ficheiros

Se o tamanho total das cordas dos ficheiros numa base de dados for superior a um [determinado limite,](backup-sql-server-azure-troubleshoot.md#size-limit-for-files)o Azure Backup armazena a lista de ficheiros de base de dados num componente diferente de poços, de modo a que não consiga definir o caminho de restauro do alvo durante a operação de restauro. Em vez disso, os ficheiros serão restaurados para o caminho predefinido do SQL.

  ![Restaurar base de dados com ficheiro grande](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="next-steps"></a>Passos seguintes

[Gerir e monitorizar](manage-monitor-sql-database-backup.md) Bases de dados do SQL Server que são apoiadas por Backup Azure.

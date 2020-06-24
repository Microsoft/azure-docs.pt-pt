---
title: Restaurar bases de dados do SQL Server num Azure VM
description: Este artigo descreve como restaurar as bases de dados do SQL Server que estão a funcionar num Azure VM e que são cópias de segurança do Azure.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 642476c98ca223da01bda5c6eb79ee9b53732468
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/11/2020
ms.locfileid: "84687434"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Restaurar bases de dados do SQL Server em VMs Azure

Este artigo descreve como restaurar uma base de dados do SQL Server que está a funcionar numa máquina virtual Azure (VM) que o serviço [de Backup Azure](backup-overview.md) tem apoiado até um cofre dos Serviços de Recuperação de Backup Azure.

Este artigo descreve como restaurar as bases de dados do SQL Server. Para obter mais informações, consulte [as bases de dados do SQL Server em VMs Azure](backup-azure-sql-database.md).

## <a name="restore-to-a-time-or-a-recovery-point"></a>Restaurar a um tempo ou um ponto de recuperação

O Azure Backup pode restaurar as bases de dados do SQL Server que estão a ser executadas em VMs Azure da seguinte forma:

- Restaurar a data ou hora específicas (para a segunda) utilizando cópias de segurança de registo de transações. O Azure Backup determina automaticamente a cópia de segurança diferencial adequada e a cadeia de backups de registos que são necessárias para restaurar com base no tempo selecionado.
- Restaurar uma cópia de segurança completa ou diferencial específica para restaurar um ponto de recuperação específico.

## <a name="prerequisites"></a>Pré-requisitos

Antes de restaurar uma base de dados, note o seguinte:

- Pode restaurar a base de dados a um exemplo de um SQL Server na mesma região do Azure.
- O servidor de destino deve estar registado no mesmo cofre que a fonte.
- Para restaurar uma base de dados encriptada TDE para outro SqL Server, é necessário restaurar primeiro [o certificado no servidor de destino](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
- Antes de restaurar a base de dados "master", inicie a instância do SQL Server no modo de utilizador único utilizando a opção de arranque **-m AzureWorkloadBackup**.
  - O valor para **-m** é o nome do cliente.
  - Apenas o nome do cliente especificado pode abrir a ligação.
- Para todas as bases de dados do sistema (modelo, master, msdb), pare o serviço sql Server Agent antes de ativar a restauração.
- Feche quaisquer aplicações que possam tentar estabelecer uma ligação a qualquer uma destas bases de dados.
- Se tiver várias instâncias em execução num servidor, todas as instâncias devem estar em funcionamento, caso contrário o servidor não apareceria na lista de servidores de destino para restaurar a base de dados.

## <a name="restore-a-database"></a>Restaurar uma base de dados

Para restaurar, precisa das seguintes permissões:

- **Permissões do operador de reserva** no cofre onde estás a fazer a restauração.
- **Acesso ao** VM de origem que está apoiado.
- **Acesso do contribuinte (escrever)** ao VM-alvo:
  - Se está a restaurar o mesmo VM, esta é a fonte VM.
  - Se está a restaurar para um local alternativo, este é o novo alvo VM.

Restaurar da seguinte forma:

1. Abra o cofre no qual está registado o SQL Server VM.
2. No painel de instrumentos do cofre, sob **utilização,** selecione **Itens de Reserva**.
3. Em **Itens de Cópia de Segurança**, sob o Tipo de **Gestão de Cópias**de Segurança, selecione **SQL em Azure VM**.

    ![Selecione SQL em Azure VM](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Selecione a base de dados para restaurar.

    ![Selecione a base de dados para restaurar](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Reveja o menu da base de dados. Fornece informações sobre a cópia de segurança da base de dados, incluindo:

    - Os pontos de restauro mais antigos e mais recentes.
    - O estado de backup de registo nas últimas 24 horas para bases de dados que estão em modo de recuperação completo e a granel e que estão configurados para cópias de segurança de registos transacionais.

6. Selecione **Restaurar**.

    ![Selecione Restaurar](./media/backup-azure-sql-database/restore-db.png)

7. Na **Configuração de Restauro,** especifique onde (ou como) restaurar os dados:
   - **Localização Alternativa**: Restaurar a base de dados para uma localização alternativa e manter a base de dados de origem original.
   - **Overwrite DB**: Restaurar os dados na mesma instância do SQL Server que a fonte original. Esta opção substitui a base de dados original.

    > [!IMPORTANT]
    > Se a base de dados selecionada pertencer a um grupo de disponibilidade Always On, o SQL Server não permite que a base de dados seja substituída. Só existe **localização alternativa.**
    >
   - **Restaurar como ficheiros**: Em vez de restaurar como base de dados, restaurar os ficheiros de cópia de segurança que podem ser recuperados como base de dados mais tarde em qualquer máquina onde os ficheiros estejam presentes usando o SQL Server Management Studio.
     ![Restaurar menu de configuração](./media/backup-azure-sql-database/restore-configuration.png)

### <a name="restore-to-an-alternate-location"></a>Restaurar para um local alternativo

1. No menu **'Configuração',** em **Onde Restaurar,** selecione **Localização Alternativa**.
2. Selecione o nome e instância do SQL Server para o qual pretende restaurar a base de dados.
3. Na caixa **de nome DB restaurado,** insira o nome da base de dados-alvo.
4. Se aplicável, selecione **Overwrite se o DB com o mesmo nome já existir em instância SQL selecionada**.
5. Selecione **OK**.

    ![Fornecer valores para o menu De Configuração Restaurar](./media/backup-azure-sql-database/restore-configuration.png)

6. No **ponto de restauro Select**, selecione se deve restaurar um ponto específico no [tempo](#restore-to-a-specific-point-in-time) ou para restaurar um ponto [de recuperação específico](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > A restauração pontual está disponível apenas para cópias de segurança de registo para bases de dados que estejam em modo de recuperação completo e a granel.

### <a name="restore-and-overwrite"></a>Restaurar e substituir

1. No menu **'Configuração',** em **"Onde Restaurar",** selecione **Overwrite DB**  >  **OK**.

    ![Selecione Overwrite DB](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. No **ponto de restauro Select**, selecione **Logs (Ponto no Tempo)** para restaurar um ponto específico no [tempo](#restore-to-a-specific-point-in-time). Ou selecione **Full & Differential** para restaurar um [ponto de recuperação específico](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > A restauração pontual está disponível apenas para cópias de segurança de registo para bases de dados que estejam em modo de recuperação completo e a granel.

### <a name="restore-as-files"></a>Restaurar como ficheiros

Para restaurar os dados de cópia de segurança como ficheiros .bak em vez de uma base de dados, escolha **Restaurar como Ficheiros**. Uma vez que os ficheiros são despejados para um caminho especificado, pode levar estes ficheiros a qualquer máquina onde pretenda restaurá-los como base de dados. Em virtude de poder mover estes ficheiros para qualquer máquina, pode agora restaurar os dados através de subscrições e regiões.

1. No menu **'Configuração',** em **Onde Restaurar,** selecione **Restaurar como ficheiros**.
2. Selecione o nome SQL Server para o qual pretende restaurar os ficheiros de cópia de segurança.
3. Na **trajetória destino do servidor,** introduza o caminho da pasta no servidor selecionado no passo 2. Este é o local onde o serviço irá despejar todos os ficheiros de cópia de segurança necessários. Normalmente, um caminho de partilha de rede, ou caminho de uma partilha de ficheiros Azure montado quando especificado como o caminho de destino, permite um acesso mais fácil a estes ficheiros por outras máquinas na mesma rede ou com a mesma partilha de ficheiros Azure montada neles.<BR>

    >Para restaurar os ficheiros de backup da base de dados numa Partilha de Ficheiros Azure montada no VM registado no alvo, certifique-se de que a NT AUTHORITY\SYSTEM tem acesso à partilha de ficheiros. Pode executar os passos abaixo para conceder as permissões de leitura/escrita à AFS montada no VM:
    >
    >- Corra `PsExec -s cmd` para entrar na concha NT AUTHORITY\SYSTEM
    >   - Execute `cmdkey /add:<storageacct>.file.core.windows.net /user:AZURE\<storageacct> /pass:<storagekey>`
    >   - Verificar o acesso com`dir \\<storageacct>.file.core.windows.net\<filesharename>`
    >- Inicie uma restauração como ficheiros do Cofre de Reserva para `\\<storageacct>.file.core.windows.net\<filesharename>` como o caminho<BR>
    Você pode baixar Psexec via<https://docs.microsoft.com/sysinternals/downloads/psexec>

4. Selecione **OK**.

    ![Selecione Restaurar como ficheiros](./media/backup-azure-sql-database/restore-as-files.png)

5. Selecione o **Ponto de Restauro** correspondente ao qual todos os ficheiros .bak disponíveis serão restaurados.

    ![Selecione um ponto de restauro](./media/backup-azure-sql-database/restore-point.png)

6. Todos os ficheiros de backup associados ao ponto de recuperação selecionado são despejados na rota de destino. Pode restaurar os ficheiros como base de dados em qualquer máquina em que estejam presentes utilizando o SQL Server Management Studio.

    ![Ficheiros de backup restaurados no caminho do destino](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>Restaurar a um ponto específico no tempo

Se selecionou **Os Registos (Ponto no Tempo)** como tipo de restauro, faça o seguinte:

1. Em **Data/Hora de Restauro,** abra o calendário. No calendário, as datas que têm pontos de recuperação são apresentadas em tipo arrojado, e a data atual é realçada.
1. Selecione uma data que tenha pontos de recuperação. Não é possível selecionar datas que não tenham pontos de recuperação.

    ![Abra o calendário](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. Depois de selecionar uma data, o gráfico de linha do tempo exibe os pontos de recuperação disponíveis num intervalo contínuo.
1. Especifique um tempo para a recuperação no gráfico da linha do tempo ou selecione um tempo. Em seguida, selecione **OK**.

    ![Selecione um tempo de restauração](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

1. No menu **Configuração Avançada,** se pretender manter a base de dados não operativa após a restauração, ative **restaurar com NORECOVERY**.
1. Se quiser alterar a localização de restauro no servidor de destino, insira um novo caminho-alvo.
1. Selecione **OK**.

    ![Menu de configuração avançado](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. No menu **Restaurar,** **selecione Restaurar** para iniciar a função de restauro.
1. Acompanhe o progresso do restauro na área de **Notificações** ou rastreie-o selecionando **empregos de Restauro** no menu da base de dados.

    ![Restaurar o progresso do trabalho](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>Restaurar a um ponto de restauro específico

Se selecionou **o Diferencial Full &** como tipo de restauro, faça o seguinte:

1. Selecione um ponto de recuperação da lista e selecione **OK** para completar o procedimento de ponto de restauro.

    ![Escolha um ponto de recuperação completo](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

    >[!NOTE]
    > Por predefinição, são apresentados pontos de recuperação dos últimos 30 dias. Pode apresentar pontos de recuperação com mais de 30 dias clicando em **Filter** e selecionando uma gama personalizada.

1. No menu **Configuração Avançada,** se pretender manter a base de dados não operativa após a restauração, ative **restaurar com NORECOVERY**.
1. Se quiser alterar a localização de restauro no servidor de destino, insira um novo caminho-alvo.
1. Selecione **OK**.

    ![Menu de configuração avançada](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. No menu **Restaurar,** **selecione Restaurar** para iniciar a função de restauro.
1. Acompanhe o progresso do restauro na área de **Notificações** ou rastreie-o selecionando **empregos de Restauro** no menu da base de dados.

    ![Restaurar o progresso do trabalho](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-databases-with-large-number-of-files"></a>Restaurar bases de dados com um grande número de ficheiros

Se o tamanho total das cordas dos ficheiros numa base de dados for superior a um [determinado limite,](backup-sql-server-azure-troubleshoot.md#size-limit-for-files)o Azure Backup armazena a lista de ficheiros de base de dados num componente diferente do poço de modo a não conseguir definir a trajetória de restauro do alvo durante a operação de restauro. Em vez disso, os ficheiros serão restaurados para o caminho padrão SQL.

  ![Restaurar a Base de Dados com grande arquivo](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="next-steps"></a>Passos seguintes

[Gerir e monitorizar](manage-monitor-sql-database-backup.md) Bases de dados do SQL Server que são apoiadas por Azure Backup.

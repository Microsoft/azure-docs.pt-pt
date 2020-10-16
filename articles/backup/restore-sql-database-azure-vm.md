---
title: Restaurar bases de dados do SQL Server num Azure VM
description: Este artigo descreve como restaurar as bases de dados do SQL Server que estão a funcionar num Azure VM e que são cópias de segurança do Azure. Também pode utilizar o Cross Region Restore para restaurar as suas bases de dados para uma região secundária.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 0d6feb512ab4ebcc5b5eaffafe607602fc552984
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90985376"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Restaurar bases de dados do SQL Server em VMs do Azure

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
- Para restaurar uma base de dados encriptada TDE para outro SqL Server, é necessário restaurar primeiro [o certificado no servidor de destino](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server).
- [As](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server) bases de dados ativadas pelo CDC devem ser restauradas utilizando a opção [Restaurar como ficheiros.](#restore-as-files)
- Antes de restaurar a base de dados "master", inicie a instância do SQL Server no modo de utilizador único utilizando a opção de arranque **-m AzureWorkloadBackup**.
  - O valor para **-m** é o nome do cliente.
  - Apenas o nome do cliente especificado pode abrir a ligação.
- Para todas as bases de dados do sistema (modelo, master, msdb), pare o serviço sql Server Agent antes de ativar a restauração.
- Feche quaisquer aplicações que possam tentar estabelecer uma ligação a qualquer uma destas bases de dados.
- Se tiver várias instâncias em execução num servidor, todas as instâncias devem estar em funcionamento, caso contrário o servidor não aparecerá na lista de servidores de destino para restaurar a base de dados.

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
1. Selecione o nome e instância do SQL Server para o qual pretende restaurar a base de dados.
1. Na caixa **de nome DB restaurado,** insira o nome da base de dados-alvo.
1. Se aplicável, selecione **Overwrite se o DB com o mesmo nome já existir em instância SQL selecionada**.
1. Selecione **Restore Point**, e selecione se [deve restaurar um ponto específico no tempo](#restore-to-a-specific-point-in-time) ou para restaurar um ponto [de recuperação específico](#restore-to-a-specific-restore-point).

    ![Selecione Ponto de Restauro](./media/backup-azure-sql-database/select-restore-point.png)

    ![Restaurar a ponto no tempo](./media/backup-azure-sql-database/restore-to-point-in-time.png)

1. No menu **Configuração Avançada:**

    - Se pretender manter a base de dados não operativa após a restauração, ative **o Restore com NORECOVERY**.
    - Se pretender alterar a localização de restauro no servidor de destino, introduza novos caminhos-alvo.

        ![Insira caminhos-alvo](./media/backup-azure-sql-database/target-paths.png)

1. Selecione **OK** para ativar o restauro. Acompanhe o progresso do restauro na área **de Notificações** ou rastreie-o sob a vista **de Backup Jobs** no cofre.

    > [!NOTE]
    > A restauração pontual está disponível apenas para cópias de segurança de registo para bases de dados que estejam em modo de recuperação completo e a granel.

### <a name="restore-and-overwrite"></a>Restaurar e substituir

1. No menu **'Configuração',** em **"Onde Restaurar",** selecione **Overwrite DB**  >  **OK**.

    ![Selecione Overwrite DB](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. No **ponto de restauro Select**, selecione **Logs (Ponto no Tempo)** para restaurar um ponto específico no [tempo](#restore-to-a-specific-point-in-time). Ou selecione **Full & Differential** para restaurar um [ponto de recuperação específico](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > A restauração pontual está disponível apenas para cópias de segurança de registo para bases de dados que estejam em modo de recuperação completo e a granel.

### <a name="restore-as-files"></a>Restaurar como ficheiros

Para restaurar os dados de cópia de segurança como ficheiros .bak em vez de uma base de dados, escolha **Restaurar como Ficheiros**. Uma vez que os ficheiros são despejados para um caminho especificado, pode levar estes ficheiros a qualquer máquina onde pretenda restaurá-los como base de dados. Uma vez que pode mover estes ficheiros para qualquer máquina, pode agora restaurar os dados através de subscrições e regiões.

1. Em **Onde e como Restaurar,** selecione Restaurar como **ficheiros**.
1. Selecione o nome SQL Server para o qual pretende restaurar os ficheiros de cópia de segurança.
1. Na **trajetória destino do servidor,** introduza o caminho da pasta no servidor selecionado no passo 2. Este é o local onde o serviço irá despejar todos os ficheiros de cópia de segurança necessários. Normalmente, um caminho de partilha de rede, ou caminho de uma partilha de ficheiros Azure montado quando especificado como o caminho de destino, permite um acesso mais fácil a estes ficheiros por outras máquinas na mesma rede ou com a mesma partilha de ficheiros Azure montada neles.<BR>

    >Para restaurar os ficheiros de backup da base de dados numa Partilha de Ficheiros Azure montada no VM registado no alvo, certifique-se de que a NT AUTHORITY\SYSTEM tem acesso à partilha de ficheiros. Pode executar os passos abaixo para conceder as permissões de leitura/escrita à AFS montada no VM:
    >
    >- Corra `PsExec -s cmd` para entrar na concha NT AUTHORITY\SYSTEM
    >   - Execute `cmdkey /add:<storageacct>.file.core.windows.net /user:AZURE\<storageacct> /pass:<storagekey>`
    >   - Verificar o acesso com `dir \\<storageacct>.file.core.windows.net\<filesharename>`
    >- Inicie uma restauração como ficheiros do Cofre de Reserva para `\\<storageacct>.file.core.windows.net\<filesharename>` como o caminho<BR>
    Você pode baixar PsExec a partir da página [Sysinternals.](/sysinternals/downloads/psexec)

1. Selecione **OK**.

    ![Selecione Restaurar como ficheiros](./media/backup-azure-sql-database/restore-as-files.png)

1. Selecione **Restore Point**, e selecione se [deve restaurar um ponto específico no tempo](#restore-to-a-specific-point-in-time) ou para restaurar um ponto [de recuperação específico](#restore-to-a-specific-restore-point).

1. Todos os ficheiros de backup associados ao ponto de recuperação selecionado são despejados na rota de destino. Pode restaurar os ficheiros como base de dados em qualquer máquina em que estejam presentes utilizando o SQL Server Management Studio.

    ![Ficheiros de backup restaurados no caminho do destino](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>Restaurar a um ponto específico no tempo

Se selecionou **Os Registos (Ponto no Tempo)** como tipo de restauro, faça o seguinte:

1. Em **Data/Hora de Restauro,** abra o calendário. No calendário, as datas que têm pontos de recuperação são apresentadas em tipo arrojado, e a data atual é realçada.
1. Selecione uma data que tenha pontos de recuperação. Não é possível selecionar datas que não tenham pontos de recuperação.

    ![Abra o calendário](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. Depois de selecionar uma data, o gráfico de linha do tempo exibe os pontos de recuperação disponíveis num intervalo contínuo.
1. Especifique um tempo para a recuperação no gráfico da linha do tempo ou selecione um tempo. Em seguida, selecione **OK**.

### <a name="restore-to-a-specific-restore-point"></a>Restaurar a um ponto de restauro específico

Se selecionou **o Diferencial Full &** como tipo de restauro, faça o seguinte:

1. Selecione um ponto de recuperação da lista e selecione **OK** para completar o procedimento de ponto de restauro.

    ![Escolha um ponto de recuperação completo](./media/backup-azure-sql-database/choose-full-recovery-point.png)

    >[!NOTE]
    > Por predefinição, são apresentados pontos de recuperação dos últimos 30 dias. Pode apresentar pontos de recuperação com mais de 30 dias selecionando **Filter** e selecionando uma gama personalizada.

### <a name="restore-databases-with-large-number-of-files"></a>Restaurar bases de dados com um grande número de ficheiros

Se o tamanho total das cordas dos ficheiros numa base de dados for superior a um [determinado limite,](backup-sql-server-azure-troubleshoot.md#size-limit-for-files)o Azure Backup armazena a lista de ficheiros de base de dados num componente diferente do poço, para que não possa definir o caminho de restauro do alvo durante a operação de restauro. Em vez disso, os ficheiros serão restaurados para o caminho padrão SQL.

  ![Restaurar a Base de Dados com grande arquivo](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="cross-region-restore"></a>Restauro da Região Transversal

Como uma das opções de restauro, o Cross Region Restore (CRR) permite restaurar as bases de dados SQL hospedadas em VMs Azure numa região secundária, que é uma região emparelhada Azure.

Para embarcar na funcionalidade durante a pré-visualização, leia a [secção Antes de Começar](./backup-create-rs-vault.md#set-cross-region-restore).

Para ver se a CRR está ativada, siga as instruções no [Configure Cross Region Restore](backup-create-rs-vault.md#configure-cross-region-restore)

### <a name="view-backup-items-in-secondary-region"></a>Ver itens de backup na região secundária

Se o CRR estiver ativado, pode ver os itens de reserva na região secundária.

1. A partir do portal, aceda a itens de backup de **cofres dos Serviços de Recuperação.**  >  **Backup items**
1. Selecione **Região Secundária** para ver os itens na região secundária.

>[!NOTE]
>Apenas os tipos de gestão de backup que suportam a função CRR serão apresentados na lista. Atualmente, apenas é permitido o apoio à reposição de dados da região secundária para uma região secundária.

![Artigos de reserva na região secundária](./media/backup-azure-sql-database/backup-items-secondary-region.png)

![Bases de dados na região secundária](./media/backup-azure-sql-database/databases-secondary-region.png)

### <a name="restore-in-secondary-region"></a>Restauro na região secundária

A região secundária restaurará a experiência do utilizador será semelhante à região primária restaurar a experiência do utilizador. Ao configurar detalhes no painel de configuração restaurar para configurar a sua restauração, será solicitado que forneça apenas parâmetros de região secundária.

![Onde e como restaurar](./media/backup-azure-sql-database/restore-secondary-region.png)

>[!NOTE]
>A rede virtual na região secundária precisa de ser atribuída de forma única, e não pode ser usada para quaisquer outros VMs nesse grupo de recursos.

![Trigger restaurar na notificação de progresso](./media/backup-azure-arm-restore-vms/restorenotifications.png)

>[!NOTE]
>
>- Após o restauro ser desencadeado e na fase de transferência de dados, o trabalho de restauro não pode ser cancelado.
>- Os papéis do Azure necessários para restaurar na região secundária são os mesmos que na região primária.

### <a name="monitoring-secondary-region-restore-jobs"></a>Monitorização da região secundária restabelece postos de trabalho

1. A partir do portal, vá para o **cofre dos Serviços de Recuperação**  >  **trabalhos de backup**
1. Selecione **Região Secundária** para ver os itens na região secundária.

    ![Trabalhos de reserva filtrados](./media/backup-azure-sql-database/backup-jobs-secondary-region.png)

## <a name="next-steps"></a>Passos seguintes

[Gerir e monitorizar](manage-monitor-sql-database-backup.md) Bases de dados do SQL Server que são apoiadas por Azure Backup.

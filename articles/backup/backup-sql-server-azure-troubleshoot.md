---
title: Solucionar problemas SQL Server backup de banco de dados
description: Informações de solução de problemas para fazer backup de SQL Server bancos de dados em execução em VMs do Azure com o backup do Azure.
ms.topic: troubleshooting
ms.date: 06/18/2019
ms.openlocfilehash: 57630749b53224032c763481d12e33366274f13f
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978777"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Solucionar problemas SQL Server backup de banco de dados usando o backup do Azure

Este artigo fornece informações de solução de problemas para SQL Server bancos de dados em execução em máquinas virtuais do Azure.

Para obter mais informações sobre o processo e as limitações de backup, consulte [about SQL Server Backup in Azure VMs](backup-azure-sql-database.md#feature-consideration-and-limitations).

## <a name="sql-server-permissions"></a>SQL Server permissões

Para configurar a proteção para um banco de dados SQL Server em uma máquina virtual, você deve instalar a extensão **AzureBackupWindowsWorkload** nessa máquina virtual. Se você receber o erro **UserErrorSQLNoSysadminMembership**, isso significa que sua instância de SQL Server não tem as permissões de backup necessárias. Para corrigir esse erro, siga as etapas em [definir permissões de VM](backup-azure-sql-database.md#set-vm-permissions).

## <a name="troubleshoot-discover-and-configure-issues"></a>Solucionar problemas de descoberta e configuração
Depois de criar e configurar um cofre dos serviços de recuperação, descobrir bancos de dados e configurar o backup é um processo de duas etapas.<br>

![sql](./media/backup-azure-sql-database/sql.png)

Durante a configuração de backup, se a VM do SQL e suas instâncias não estiverem visíveis nos bancos de entrada de **descoberta em VMs** e **Configurar o backup** (consulte a imagem acima), verifique se:

### <a name="step-1-discovery-dbs-in-vms"></a>Etapa 1: bancos de entrada de descoberta em VMs

- Se a VM não estiver listada na lista de VMs descobertas e também não estiver registrada para backup do SQL em outro cofre, siga as etapas de [backup de SQL Server de descoberta](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#discover-sql-server-databases) .

### <a name="step-2-configure-backup"></a>Etapa 2: configurar o backup

- Se o cofre no qual a VM do SQL está registrada no mesmo cofre usado para proteger os bancos de dados, siga as etapas [Configurar backup](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#configure-backup) .

Se a VM do SQL precisar ser registrada no novo cofre, ele deverá ter o registro cancelado do cofre antigo.  O cancelamento do registro de uma VM do SQL do cofre exige que todas as fontes de dados protegidas sejam interrompidas e, em seguida, você pode excluir os dados de backup. A exclusão de dados de backup é uma operação destrutiva.  Depois de examinar e tomar todas as precauções para cancelar o registro da VM do SQL, registre essa mesma VM com um novo cofre e repita a operação de backup.



## <a name="error-messages"></a>Mensagens de erro

### <a name="backup-type-unsupported"></a>Tipo de backup sem suporte

| Gravidade | Descrição | Possíveis causas | Ação recomendada |
|---|---|---|---|
| Aviso | As configurações atuais deste banco de dados não dão suporte a determinados tipos de backup presentes na política associada. | <li>Somente uma operação de backup de banco de dados completa pode ser executada no banco de dados mestre. Nenhum backup diferencial nem backup de log de transações é possível. </li> <li>Qualquer banco de dados no modelo de recuperação simples não permite o backup de logs de transações.</li> | Modifique as configurações do banco de dados de modo que haja suporte para todos os tipos de backup na política. Ou altere a política atual para incluir apenas os tipos de backup com suporte. Caso contrário, os tipos de backup sem suporte serão ignorados durante o backup agendado ou o trabalho de backup falhará para o backup sob demanda.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| Esta base de dados SQL não suporta o tipo de cópia de segurança pedido | Ocorre quando o modelo de recuperação de banco de dados não permite o tipo de backup solicitado. O erro pode ocorrer nas seguintes situações: <br/><ul><li>Um banco de dados que está usando um modelo de recuperação simples não permite o backup de log.</li><li>Backups diferenciais e de log não são permitidos para um banco de dados mestre.</li></ul>Para obter mais detalhes, consulte a documentação dos [modelos de recuperação do SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) . | Se o backup de log falhar para o banco de dados no modelo de recuperação simples, tente uma destas opções:<ul><li>Se o banco de dados estiver no modo de recuperação simples, desabilite os backups de log.</li><li>Use a [documentação SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) para alterar o modelo de recuperação de banco de dados para bulk-logged ou completo. </li><li> Se você não quiser alterar o modelo de recuperação e tiver uma política padrão para fazer backup de vários bancos de dados que não podem ser alterados, ignore o erro. Seus backups completos e diferenciais funcionarão por agendamento. Os backups de log serão ignorados, o que é esperado nesse caso.</li></ul>Se for um banco de dados mestre e tiver configurado o backup diferencial ou de log, use uma das seguintes etapas:<ul><li>Use o portal para alterar o agendamento da política de backup do banco de dados mestre para completo.</li><li>Se você tiver uma política padrão para fazer backup de vários bancos de dados que não podem ser alterados, ignore o erro. O backup completo funcionará por agendamento. Os backups diferenciais ou de log não acontecerão, o que é esperado nesse caso.</li></ul> |
| A operação foi cancelada porque uma operação conflitante já estava em execução no mesmo banco de dados. | Consulte a [entrada de blog sobre as limitações de backup e restauração](https://deep.data.blog/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database/) que são executadas simultaneamente.| [Use o SQL Server Management Studio (SSMS) para monitorar os trabalhos de backup](manage-monitor-sql-database-backup.md). Depois que a operação conflitante falhar, reinicie a operação.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| A base de dados SQL não existe. | O banco de dados foi excluído ou renomeado. | Verifique se o banco de dados foi acidentalmente excluído ou renomeado.<br/><br/> Se o banco de dados tiver sido acidentalmente excluído, para continuar backups, restaure o banco de dados para o local original.<br/><br/> Se você excluiu o banco de dados e não precisa de backups futuros, no cofre dos serviços de recuperação, selecione **parar backup** com **manter dados de backup** ou **excluir dados de backup**. Para obter mais informações, consulte [gerenciar e monitorar bancos de dados SQL Server de backup](manage-monitor-sql-database-backup.md).

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| A cadeia de registos está quebrada. | O backup do banco de dados ou da VM é feito por meio de outra solução de backup, que trunca a cadeia de logs.|<ul><li>Verifique se outra solução ou script de backup está em uso. Nesse caso, pare a outra solução de backup. </li><li>Se o backup foi um backup de log sob demanda, dispare um backup completo para iniciar uma nova cadeia de logs. Para backups de log agendados, nenhuma ação é necessária porque o serviço de backup do Azure irá disparar automaticamente um backup completo para corrigir esse problema.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| O backup do Azure não é capaz de se conectar à instância do SQL. | O backup do Azure não pode se conectar à instância de SQL Server. | Use os detalhes adicionais no menu portal do Azure erro para restringir as causas raiz. Consulte [solução de problemas de backup do SQL](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) para corrigir o erro.<br/><ul><li>Se as configurações padrão do SQL não permitirem conexões remotas, altere as configurações. Consulte os seguintes artigos para obter informações sobre como alterar as configurações:<ul><li>[MSSQLSERVER_-1](/previous-versions/sql/sql-server-2016/bb326495(v=sql.130))</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Se houver problemas de logon, use estes links para corrigi-los:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| O primeiro backup completo está ausente para esta fonte de dados. | O backup completo está ausente para o banco de dados. Backups diferenciais e de log são pais para um backup completo, portanto, certifique-se de fazer backups completos antes de disparar backups diferenciais ou de log. | Disparar um backup completo sob demanda.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| Não é possível fazer backup, pois o log de transações da fonte de dados está cheio. | O espaço de log transacional do banco de dados está cheio. | Para corrigir esse problema, consulte a [documentação do SQL Server](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| Já existe um banco de dados com o mesmo nome no local de destino | O destino da restauração de destino já tem um banco de dados com o mesmo nome.  | <ul><li>Altere o nome do banco de dados de destino.</li><li>Ou use a opção forçar substituição na página restaurar.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| O restauro falhou, porque não foi possível colocar a base de dados offline. | Enquanto você estiver fazendo uma restauração, o banco de dados de destino precisará ser colocado offline. O backup do Azure não pode colocar esses dados offline. | Use os detalhes adicionais no menu portal do Azure erro para restringir as causas raiz. Para obter mais informações, veja a [documentação do SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

### <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| Não é possível localizar o certificado do servidor com impressão digital no destino. | O banco de dados mestre na instância de destino não tem uma impressão digital de criptografia válida. | Importe a impressão digital de certificado válida usada na instância de origem para a instância de destino. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| A cópia de segurança do registo utilizada para recuperação contém alterações registadas em massa. Não pode ser utilizada para parar num ponto no tempo arbitrário de acordo com as diretrizes SQL. | Quando um banco de dados está no modo de recuperação bulk-logged, os dados entre uma transação bulk-logged e a próxima transação de log não podem ser recuperados. | Escolha um ponto diferente no tempo para a recuperação. [Saiba mais](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms186229(v=sql.105)).

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| Não é possível respeitar a preferência de cópia de segurança do Grupo de Disponibilidade AlwaysOn do SQL, uma vez que alguns nós do Grupo de Disponibilidade não estão registados. | Os nós necessários para executar backups não estão registrados ou estão inacessíveis. | <ul><li>Verifique se todos os nós necessários para executar backups deste banco de dados estão registrados e íntegros e repita a operação.</li><li>Altere a preferência de backup para o SQL Server Always On grupo de disponibilidade.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| A VM do SQL Server está desligada e não está acessível para o serviço de backup do Azure. | A VM está desligada. | Verifique se a instância de SQL Server está em execução. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| O serviço de backup do Azure usa o agente convidado de VM do Azure para fazer backup, mas o agente convidado não está disponível no servidor de destino. | O agente convidado não está habilitado ou não está íntegro. | [Instale o agente convidado da VM](../virtual-machines/extensions/agent-windows.md) manualmente. |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| A intenção de proteção automática foi removida ou não é mais válida. | Ao habilitar a proteção automática em uma instância do SQL Server, **Configure** os trabalhos de backup executados para todos os bancos de dados nessa instância. Se você desabilitar a proteção automática enquanto os trabalhos estiverem em execução, os trabalhos **em andamento** serão cancelados com esse código de erro. | Habilite a proteção automática mais uma vez para ajudar a proteger todos os bancos de dados restantes. |

### <a name="clouddosabsolutelimitreached"></a>CloudDosAbsoluteLimitReached

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
A operação está bloqueada, pois você atingiu o limite de número de operações permitidas em 24 horas. | Quando você atingir o limite máximo permitido para uma operação em um intervalo de 24 horas, esse erro será fornecido. <br> Por exemplo: se você atingiu o limite do número de trabalhos de backup de configuração que podem ser disparados por dia e tentar configurar o backup em um novo item, você verá esse erro. | Normalmente, repetir a operação após 24 horas resolve esse problema. No entanto, se o problema persistir, você poderá entrar em contato com o suporte da Microsoft para obter ajuda.

### <a name="clouddosabsolutelimitreachedwithretry"></a>CloudDosAbsoluteLimitReachedWithRetry

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
A operação está bloqueada porque o cofre atingiu seu limite máximo para essas operações permitidas em um intervalo de 24 horas. | Quando você atingir o limite máximo permitido para uma operação em um intervalo de 24 horas, esse erro será fornecido. Esse erro geralmente é exibido quando há operações em escala, como modificar política ou proteção automática. Ao contrário do caso do CloudDosAbsoluteLimitReached, não há muito que você possa fazer para resolver esse estado. na verdade, o serviço de backup do Azure tentará novamente as operações internamente para todos os itens em questão.<br> Por exemplo: se você tiver um grande número de fontes de fonte protegidas por uma política e tentar modificar essa política, ela irá disparar configurar trabalhos de proteção para cada um dos itens protegidos e, às vezes, poderá atingir o limite máximo permitido para essas operações por dia.| O serviço de backup do Azure repetirá essa operação automaticamente após 24 horas.

### <a name="usererrorvminternetconnectivityissue"></a>UserErrorVMInternetConnectivityIssue

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
A VM não é capaz de contatar o serviço de backup do Azure devido a problemas de conectividade com a Internet. | A VM precisa de conectividade de saída para o serviço de backup do Azure, o armazenamento do Azure ou serviços de Azure Active Directory.| -Se você usar NSG para restringir a conectividade, deverá usar a marca de serviço AzureBackup para permitir o acesso de saída ao backup do Azure para o serviço de backup do Azure, armazenamento do Azure ou serviços de Azure Active Directory. Siga estas [etapas](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#allow-access-using-nsg-tags) para conceder acesso.<br>-Verifique se o DNS está resolvendo os pontos de extremidade do Azure.<br>-Verifique se a VM está atrás de um balanceador de carga bloqueando o acesso à Internet. Ao atribuir o IP público às VMs, a descoberta funcionará.<br>-Verifique se não há firewall/antivírus/proxy que esteja bloqueando chamadas para os três serviços de destino acima.


## <a name="re-registration-failures"></a>Falhas de novo registro

Verifique se há um ou mais dos seguintes sintomas antes de disparar a operação de novo registro:

* Todas as operações (como backup, restauração e configuração de backup) estão falhando na VM com um dos seguintes códigos de erro: **WorkloadExtensionNotReachable**, **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent**, **WorkloadExtensionDidntDequeueMsg**.
* Se a área **status de backup** do item de backup estiver mostrando **não acessível**, descartar todas as outras causas que podem resultar no mesmo status:

  * Falta de permissão para executar operações relacionadas ao backup na VM.
  * Desligamento da VM, portanto, os backups não podem ocorrer.
  * Problemas de rede.

   ![Registrando novamente a VM](./media/backup-azure-sql-database/re-register-vm.png)



* No caso de um grupo de disponibilidade Always On, os backups começaram a falhar após a alteração da preferência de backup ou após um failover.

Esses sintomas podem surgir por um ou mais dos seguintes motivos:

* Uma extensão foi excluída ou desinstalada do Portal.
* Uma extensão foi desinstalada do **painel de controle** na VM em **desinstalar ou alterar um programa**.
* A VM foi restaurada de volta no tempo por meio da restauração de disco in-loco.
* A VM foi desligada por um período estendido, portanto, a configuração de extensão nela expirou.
* A VM foi excluída e outra VM foi criada com o mesmo nome e no mesmo grupo de recursos que a VM excluída.
* Um dos nós do grupo de disponibilidade não recebeu a configuração de backup completa. Isso pode acontecer quando o grupo de disponibilidade é registrado no cofre ou quando um novo nó é adicionado.

Nos cenários anteriores, recomendamos que você dispare uma operação de novo registro na VM. Consulte [aqui](https://docs.microsoft.com/azure/backup/backup-azure-sql-automation#enable-backup) para obter instruções sobre como executar essa tarefa no PowerShell.

## <a name="size-limit-for-files"></a>Limite de tamanho para arquivos

O tamanho total da cadeia de caracteres de arquivos depende não apenas do número de arquivos, mas também de seus nomes e caminhos. Para cada arquivo de banco de dados, obtenha o nome de arquivo lógico e o caminho físico. Você pode usar esta consulta SQL:

```sql
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

Agora, organize-os no seguinte formato:

```json
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

Segue-se um exemplo:

```json
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

Se o tamanho da cadeia de caracteres do conteúdo exceder 20.000 bytes, os arquivos do banco de dados serão armazenados de maneira diferente. Durante a recuperação, você não poderá definir o caminho do arquivo de destino para restauração. Os arquivos serão restaurados para o caminho SQL padrão fornecido pelo SQL Server.

### <a name="override-the-default-target-restore-file-path"></a>Substituir o caminho de arquivo de restauração de destino padrão

Você pode substituir o caminho do arquivo de restauração de destino durante a operação de restauração, colocando um arquivo JSON que contém o mapeamento do arquivo de banco de dados para o caminho de restauração de destino. Crie um arquivo de `database_name.json` e coloque-o no local *C:\Program Programas\azure Workload Backup\bin\plugins\SQL*.

O conteúdo do arquivo deve estar neste formato:

```json
[
  {
    "Path": "<Restore_Path>",
    "LogicalName": "<LogicalName>",
    "IsDir": "false"
  },
  {
    "Path": "<Restore_Path>",
    "LogicalName": "LogicalName",
    "IsDir": "false"
  },  
]
```

Segue-se um exemplo:

```json
[
  {
   "Path": "F:\\Data\\testdb2_1546408741449456.mdf",
   "LogicalName": "testdb7",
   "IsDir": "false"
  },
  {
    "Path": "F:\\Log\\testdb2_log_1546408741449456.ldf",
    "LogicalName": "testdb7_log",
    "IsDir": "false"
  },  
]
```

No conteúdo anterior, você pode obter o nome lógico do arquivo de banco de dados usando a seguinte consulta SQL:

```sql
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```

Esse arquivo deve ser colocado antes de você disparar a operação de restauração.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o backup do Azure para VMs SQL Server (visualização pública), consulte [backup do Azure para VMs do SQL](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).

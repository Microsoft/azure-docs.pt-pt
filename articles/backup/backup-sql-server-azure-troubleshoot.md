---
title: Backup da base de dados do SQL Server de resolução de problemas
description: Informações de resolução de problemas para fazer backup das bases de dados do SQL Server em execução em VMs Azure com Azure Backup.
ms.topic: troubleshooting
ms.date: 06/18/2019
ms.openlocfilehash: d502a4188b4f9f383188804f86abbb9a6d05d146
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99429471"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Resolução de problemas ML Server base de dados backup usando Azure Backup

Este artigo fornece informações de resolução de problemas para bases de dados do SQL Server em execução em máquinas virtuais Azure.

Para obter mais informações sobre o processo de backup e limitações, consulte [a cópia de segurança do SQL Server em VMs Azure](sql-support-matrix.md#feature-considerations-and-limitations).

## <a name="sql-server-permissions"></a>Permissões do Servidor SQL

Para configurar a proteção de uma base de dados do SQL Server numa máquina virtual, tem de instalar a extensão **AzureBackupWindowsWorkload** naquela máquina virtual. Se tiver o erro **UserErrorSQLNoSysadminMemberbership,** significa que a sua instância do SQL Server não tem as permissões de backup necessárias. Para corrigir este erro, siga os passos nas [permissões set VM](backup-azure-sql-database.md#set-vm-permissions).

## <a name="troubleshoot-discover-and-configure-issues"></a>Resolver problemas de descoberta e configuração

Depois de criar e configurar um cofre dos Serviços de Recuperação, descobrir bases de dados e configurar backup é um processo em duas etapas.<br>

![Objetivo de Backup - SQL Server em Azure VM](./media/backup-azure-sql-database/sql.png)

Durante a configuração de backup, se o SQL VM e as suas instâncias não forem visíveis nos **DBs discovery em VMs** e **Configure Backup** (consulte a imagem acima) certifique-se de que:

### <a name="step-1-discovery-dbs-in-vms"></a>Passo 1: Discovery DBs em VMs

- Se o VM não estiver listado na lista de VM descoberto e também não estiver registado para backup SQL em outro cofre, siga os passos de backup do [Discovery SQL Server.](./backup-sql-server-database-azure-vms.md#discover-sql-server-databases)

### <a name="step-2-configure-backup"></a>Passo 2: Configurar backup

- Se o cofre no qual o SQL VM está registado no mesmo cofre utilizado para proteger as bases de dados, siga os passos [de backup de configuração.](./backup-sql-server-database-azure-vms.md#configure-backup)

Se o SQL VM precisa de ser registado no novo cofre, então deve não estar registado do antigo cofre.  A não inscrição de um SQL VM do cofre requer que todas as fontes de dados protegidas sejam protegidas e, em seguida, pode eliminar os dados de back-up. Eliminar dados com o apoio é uma operação destrutiva.  Depois de ter revisto e tomado todas as precauções para desregistral do SQL VM, registe este mesmo VM com um novo cofre e recomprinando a operação de backup.

## <a name="troubleshoot-backup-and-recovery-issues"></a>Problemas de backup e recuperação de problemas de resolução de problemas  

Por vezes, ou falhas aleatórias podem ocorrer em operações de backup e restauro ou essas operações podem ficar presas. Isto pode ser devido a programas antivírus no seu VM. Como uma boa prática, sugerimos os seguintes passos:

1. Excluir as seguintes pastas da digitalização antivírus:

    `C:\Program Files\Azure Workload Backup` `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.WorkloadBackup.AzureBackupWindowsWorkload`

    `C:\`Substitua-a pela letra do seu *SystemDrive*.

1. Excluir os três processos seguintes em execução num VM da digitalização antivírus:

    - IaasWLPluginSvc.exe
    - IaaSWorkloadCoordinatorService.exe
    - TriggerExtensionJob.exe

1. A SQL também oferece algumas diretrizes prestes a trabalhar com programas antivírus. Consulte [este artigo](https://support.microsoft.com/help/309422/choosing-antivirus-software-for-computers-that-run-sql-server) para mais detalhes.

## <a name="faulty-instance-in-a-vm-with-multiple-sql-server-instances"></a>Instância defeituosa num VM com várias instâncias do SQL Server

Só pode restaurar um SQL VM se todas as instâncias SQL que estão a decorrer dentro do VM forem reportadas como saudáveis. Se uma ou mais instâncias forem "defeituosas", o VM não aparecerá como um alvo de restauro. Portanto, esta pode ser uma possível razão pela qual um VM de vários casos pode não aparecer no dropdown "servidor" durante a operação de restauro.

Pode validar a "Preparação de Backup" de todas as instâncias SQL no VM, sob **cópia de segurança configurada:**

![Validar a prontidão de backup](./media/backup-sql-server-azure-troubleshoot/backup-readiness.png)

Se quiser desencadear uma restauração nas ocorrências saudáveis do SQL, faça os seguintes passos:

1. Inscreva-se no SQL VM e vá a `C:\Program Files\Azure Workload Backup\bin` .
1. Crie um ficheiro JSON nomeado `ExtensionSettingsOverrides.json` (se ainda não estiver presente). Se este ficheiro já estiver presente no VM, continue a usá-lo.
1. Adicione o seguinte conteúdo no ficheiro JSON e guarde o ficheiro:

    ```json
    {
                  "<ExistingKey1>":"<ExistingValue1>",
                    …………………………………………………… ,
              "whitelistedInstancesForInquiry": "FaultyInstance_1,FaultyInstance_2"
            }
            
            Sample content:        
            { 
              "whitelistedInstancesForInquiry": "CRPPA,CRPPB "
            }

    ```

1. Desencadeie a operação **De Rediscover DBs** no servidor impactado a partir do portal Azure (o mesmo local onde a prontidão de backup pode ser vista). O VM começará a aparecer como alvo para operações de restauro.

    ![Redescobrir DBs](./media/backup-sql-server-azure-troubleshoot/rediscover-dbs.png)

1. Remova a entrada *whitelistedInstancesForInquiry* do ExtensionSettingsOverrides.jsem ficheiro uma vez que a operação de restauro esteja concluída.

## <a name="error-messages"></a>Mensagens de erro

### <a name="backup-type-unsupported"></a>Tipo de backup não suportado

| Gravidade | Descrição | Possíveis causas | Ação recomendada |
|---|---|---|---|
| Aviso | As definições atuais para esta base de dados não suportam certos tipos de backup presentes na política associada. | <li>Apenas uma operação de backup de base de dados completa pode ser realizada na base de dados principal. A cópia de segurança diferencial e a cópia de segurança do registo de transações não são possíveis. </li> <li>Qualquer base de dados no modelo de recuperação simples não permite a cópia de segurança dos registos de transações.</li> | Modifique as definições da base de dados para que todos os tipos de backup da política sejam suportados. Ou alterar a política atual para incluir apenas os tipos de backup suportados. Caso contrário, os tipos de backup não suportados serão ignorados durante o backup programado ou o trabalho de backup falhará para o backup a pedido.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| Esta base de dados SQL não suporta o tipo de cópia de segurança pedido | Ocorre quando o modelo de recuperação da base de dados não permite o tipo de cópia de segurança solicitado. O erro pode ocorrer nas seguintes situações: <br/><ul><li>Uma base de dados que está a usar um modelo simples de recuperação não permite a cópia de segurança.</li><li>Os backups diferenciais e de registo não são permitidos para uma base de dados principal.</li></ul>Para obter mais detalhes, consulte a documentação dos [modelos de recuperação do SQL Server.](/sql/relational-databases/backup-restore/recovery-models-sql-server) | Se a cópia de segurança de registo falhar para a base de dados no modelo de recuperação simples, experimente uma destas opções:<ul><li>Se a base de dados estiver em modo de recuperação simples, desative as cópias de segurança de registo.</li><li>Utilize a documentação do [SQL Server](/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) para alterar o modelo de recuperação da base de dados para registos completos ou a granel. </li><li> Se não quiser alterar o modelo de recuperação e tiver uma política padrão para fazer o back-up de várias bases de dados que não podem ser alteradas, ignore o erro. As suas cópias de segurança completas e diferenciais funcionarão por horário. As cópias de segurança serão ignoradas, o que é esperado neste caso.</li></ul>Se for uma base de dados principal e tiver configurado diferencial ou cópia de segurança de registo, utilize qualquer um dos seguintes passos:<ul><li>Utilize o portal para alterar o calendário de política de backup para a base de dados principal, para a totalidade.</li><li>Se tiver uma política padrão para fazer o back-up de várias bases de dados que não podem ser alteradas, ignore o erro. A sua cópia de segurança completa funcionará por horário. As cópias de segurança diferenciais ou de registo não vão acontecer, o que é esperado neste caso.</li></ul> |
| A operação cancelada como uma operação conflituosa já estava a decorrer na mesma base de dados. | Consulte a entrada do [blog sobre backup e restaure as limitações](https://deep.data.blog/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database/) que funcionam simultaneamente.| [Utilize o SQL Server Management Studio (SSMS) para monitorizar os trabalhos de backup](manage-monitor-sql-database-backup.md). Depois de a operação conflituosa falhar, reinicie a operação.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| A base de dados SQL não existe. | A base de dados foi apagada ou renomeada. | Verifique se a base de dados foi acidentalmente eliminada ou renomeada.<br/><br/> Se a base de dados foi acidentalmente eliminada, para continuar as cópias de segurança, restaure a base de dados para a localização original.<br/><br/> Se eliminar a base de dados e não precisar de cópias de segurança futuras, então no cofre dos Serviços de Recuperação, selecione **Parar a cópia de segurança** com os **dados de backup retidos** ou **eliminar dados de backup**. Para obter mais informações, consulte Gerir e monitorizar bases de dados do [SQL Server com ressurge.](manage-monitor-sql-database-backup.md)

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| A cadeia de registos está quebrada. | A base de dados ou o VM é apoiado através de outra solução de backup, que trunca a cadeia de registos.|<ul><li>Verifique se está a ser utilizada outra solução de backup ou script. Em caso afirmativo, pare a outra solução de reserva. </li><li>Se a cópia de segurança foi uma cópia de segurança de registo a pedido, desencadeie uma cópia de segurança completa para iniciar uma nova corrente de registo. Para cópias de segurança agendadas, não é necessária qualquer ação porque o serviço de Backup Azure ativará automaticamente uma cópia de segurança completa para corrigir este problema.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| O Azure Backup não é capaz de se ligar à instância SQL. | O Azure Backup não pode ligar-se à instância do SQL Server. | Utilize os detalhes adicionais no menu de erro do portal Azure para reduzir as causas da raiz. Consulte a [resolução de problemas de backup SQL](/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) para corrigir o erro.<br/><ul><li>Se as definições SQL predefinidos não permitirem ligações remotas, altere as definições. Consulte os seguintes artigos para obter informações sobre a alteração das definições:<ul><li>[MSSQLSERVER_-1](/sql/relational-databases/errors-events/mssqlserver-1-database-engine-error)</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Se houver problemas de login, utilize estes links para os corrigir:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| Falta a primeira cópia de segurança completa para esta fonte de dados. | Falta cópia de segurança completa para a base de dados. Os backups de registo e diferenciais são os pais de uma cópia de segurança completa, por isso certifique-se de fazer cópias de segurança completas antes de desencadear backups diferenciais ou de registo. | Desencadeie uma cópia de segurança a pedido.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| Não é possível obter cópia de segurança, uma vez que o registo de transações para a fonte de dados está cheio. | O espaço de registo transacional da base de dados está cheio. | Para corrigir este problema, consulte a documentação do [SQL Server](/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| Base de dados com o mesmo nome já existe no local alvo | O destino de restauro do alvo já tem uma base de dados com o mesmo nome.  | <ul><li>Mude o nome da base de dados-alvo.</li><li>Ou, use a opção de sobrepor a força na página de restauro.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| O restauro falhou, porque não foi possível colocar a base de dados offline. | Enquanto estás a fazer uma restauração, a base de dados do alvo tem de ser desligada. O Azure Backup não pode desligar estes dados. | Utilize os detalhes adicionais no menu de erro do portal Azure para reduzir as causas da raiz. Para obter mais informações, veja a [documentação do SQL Server](/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

### <a name="wlextgenericiofaultusererror"></a>WlExtGenericIOFaultUserError

|Mensagem de Erro |Possíveis causas  |Ação Recomendada  |
|---------|---------|---------|
|Durante a operação ocorreu um erro de entrada/saída. Verifique se há erros comuns de IO na máquina virtual.   |   Permissões de acesso ou restrições de espaço no alvo.       |  Verifique se há erros comuns de IO na máquina virtual. Certifique-se de que a unidade-alvo/ partilha de rede na máquina: <li> tem permissão de leitura/escrita para a conta NT AUTHORITY\SYSTEM na máquina. <li> tem espaço suficiente para a operação completar com sucesso.<br> Para obter mais informações, consulte [Restaurar como ficheiros](restore-sql-database-azure-vm.md#restore-as-files).
       |

### <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| Não é possível encontrar o certificado do servidor com impressão digital no alvo. | A base de dados principal sobre a instância de destino não tem uma impressão digital de encriptação válida. | Importar a impressão digital de certificado válida utilizada na instância de origem, para a instância-alvo. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| A cópia de segurança do registo utilizada para recuperação contém alterações registadas em massa. Não pode ser utilizado para parar num ponto arbitrário no tempo, de acordo com as diretrizes do SQL. | Quando uma base de dados está em modo de recuperação registado a granel, os dados entre uma transação registada em massa e a próxima transação de registo não podem ser recuperados. | Escolha um ponto diferente no tempo para a recuperação. [Saiba mais](/sql/relational-databases/backup-restore/recovery-models-sql-server).

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| Não é possível respeitar a preferência de cópia de segurança do Grupo de Disponibilidade AlwaysOn do SQL, uma vez que alguns nós do Grupo de Disponibilidade não estão registados. | Os nós necessários para realizar backups não estão registados ou são inacessíveis. | <ul><li>Certifique-se de que todos os nós necessários para a realização de cópias de segurança desta base de dados estão registados e saudáveis e, em seguida, re-tentar a operação.</li><li>Altere a preferência de backup para o grupo de disponibilidade SQL Server Always On.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| O VM do servidor SQL está desligado e não está acessível ao serviço Azure Backup. | O VM está desligado. | Certifique-se de que a instância do SQL Server está a decorrer. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| O serviço Azure Backup utiliza o agente convidado Azure VM para fazer cópia de segurança, mas o agente convidado não está disponível no servidor alvo. | O agente convidado não está habilitado ou não é saudável. | [Instale manualmente o agente convidado VM.](../virtual-machines/extensions/agent-windows.md) |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| A intenção de proteção automática foi removida ou não é mais válida. | Quando ativa a proteção automática numa instância do SQL Server, os trabalhos de **Backup configuram** para todas as bases de dados nesse caso. Se desativar a proteção automática enquanto os trabalhos estão em funcionamento, os trabalhos **em progresso** são cancelados com este código de erro. | Volte a ativar a proteção automática para ajudar a proteger todas as bases de dados restantes. |

### <a name="clouddosabsolutelimitreached"></a>CloudDosAbsoluteLimitReached

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
A operação está bloqueada, uma vez que atingiu o limite do número de operações permitidas em 24 horas. | Quando se alcança o limite máximo permitido para uma operação num período de 24 horas, este erro aparece. <br> Por exemplo: Se atingiu o limite para o número de trabalhos de backup configurados que podem ser desencadeados por dia, e tentar configurar o backup de um novo item, verá este erro. | Normalmente, voltar a tentar a operação após 24 horas resolve este problema. No entanto, se o problema persistir, pode contactar o suporte da Microsoft para obter ajuda.

### <a name="clouddosabsolutelimitreachedwithretry"></a>CloudDosAbsoluteLimitReachedWithRetry

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
A operação está bloqueada, uma vez que o cofre atingiu o seu limite máximo para tais operações permitidas num período de 24 horas. | Quando se alcança o limite máximo permitido para uma operação num período de 24 horas, este erro aparece. Este erro aparece geralmente quando existem operações à escala, tais como modificar a política ou a auto-protecção. Ao contrário do caso do CloudDosAbsoluteLimitReached, não há muito que possa fazer para resolver este estado. De facto, o serviço de backup Azure irá voltar a tentar as operações internamente para todos os itens em questão.<br> Por exemplo: Se tiver um grande número de fontes de dados protegidas com uma política e tentar modificar essa política, irá desencadear postos de proteção de configuração para cada um dos itens protegidos e, por vezes, poderá atingir o limite máximo permitido para tais operações por dia.| O serviço de backup Azure tentará automaticamente esta operação após 24 horas.

### <a name="usererrorvminternetconnectivityissue"></a>UserErrorVMInternetConnectivityIssue

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
O VM não é capaz de contactar o serviço de Backup Azure devido a problemas de conectividade com a Internet. | O VM precisa de conectividade de saída para o Serviço de Backup Azure, Armazenamento Azure ou Serviços de Diretório Ativo Azure.| - Se utilizar o NSG para restringir a conectividade, então deve utilizar a etiqueta de serviço *AzureBackup* para permitir o acesso de saída ao Serviço de Backup Azure e, da mesma forma, para os serviços Azure *AD (AzureActiveDirectory)* e Azure Storage *(Storage).* Siga estes [passos](./backup-sql-server-database-azure-vms.md#nsg-tags) para conceder acesso.<br>- Certifique-se de que o DNS está a resolver os pontos finais do Azure.<br>- Verifique se o VM está por detrás de um equilibrador de carga que bloqueia o acesso à Internet. Ao atribuir IP público aos VMs, a descoberta funcionará.<br>- Verifique se não há firewall/antivírus/procuração que bloqueie chamadas para os três serviços-alvo acima.

## <a name="re-registration-failures"></a>Falhas de re-registo

Verifique se existem um ou mais dos seguintes sintomas antes de desencadear a operação de re-registo:

- Todas as operações (como cópia de segurança, restauro e cópia de segurança de configuração) estão a falhar no VM com um dos seguintes códigos de erro: **WorkloadExtensionNotReachable,** **UserErrorWorkloadExtensionNotInstalled,** **WorkloadExtensionNotPresent**, **WorkloadExtensionDidntDequeueMsg**.
- Se a área **de Estado de Backup** do item de backup mostrar Não é **alcançável,** exclua todas as outras causas que possam resultar no mesmo estado:

  - Falta de autorização para realizar operações relacionadas com cópias de segurança no VM.
  - Encerramento do VM, para que os reforços não possam ter lugar.
  - [Problemas de rede](#usererrorvminternetconnectivityissue)

   ![re-inscrição em VM](./media/backup-azure-sql-database/re-register-vm.png)

- No caso de um grupo de disponibilidade Always On, os backups começaram a falhar depois de alterar a preferência de backup ou após uma falha.

Estes sintomas podem surgir por uma ou mais das seguintes razões:

- Uma extensão foi eliminada ou desinstalada do portal.
- Foi desinstalada uma extensão do Painel de **Controlo** no VM ao abrigo **da Desinstalação ou Alteração de um Programa**.
- O VM foi restaurado no tempo através da restauração do disco no local.
- O VM foi desligado por um período prolongado, pelo que a configuração da extensão expirou.
- O VM foi eliminado, e outro VM foi criado com o mesmo nome e no mesmo grupo de recursos que o VM eliminado.
- Um dos nós do grupo de disponibilidade não recebeu a configuração completa de backup. Isto pode acontecer quando o grupo de disponibilidade está registado no cofre ou quando um novo nó é adicionado.

Nos cenários anteriores, recomendamos que desencadeie uma operação de re-registo no VM. Consulte [aqui](./backup-azure-sql-automation.md#enable-backup) as instruções sobre como executar esta tarefa no PowerShell.

## <a name="size-limit-for-files"></a>Limite de tamanho para ficheiros

O tamanho total das cordas dos ficheiros depende não só do número de ficheiros, mas também dos seus nomes e caminhos. Para cada ficheiro de base de dados, obtenha o nome do ficheiro lógico e o caminho físico. Pode utilizar esta consulta SQL:

```sql
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

Agora organize-os no seguinte formato:

```json
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

Eis um exemplo:

```json
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

Se o tamanho da cadeia do conteúdo exceder 20.000 bytes, os ficheiros de base de dados são armazenados de forma diferente. Durante a recuperação, não será capaz de definir o caminho do ficheiro alvo para restaurar. Os ficheiros serão restaurados para o caminho SQL predefinido fornecido pelo SQL Server.

### <a name="override-the-default-target-restore-file-path"></a>Anular o caminho do ficheiro de restauro do alvo predefinido

Pode anular o caminho do ficheiro de restauro do alvo durante a operação de restauro, colocando um ficheiro JSON que contenha o mapeamento do ficheiro de base de dados para a trajetória de restauro do alvo. Crie um `database_name.json` ficheiro e coloque-o no `C:\Program Files\Azure Workload Backup\bin\plugins\SQL*` local.

O conteúdo do ficheiro deve estar neste formato:

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

Eis um exemplo:

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

No conteúdo anterior, pode obter o nome lógico do ficheiro de base de dados utilizando a seguinte consulta SQL:

```sql
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
```

Este ficheiro deve ser colocado antes de ativar a operação de restauro.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre Azure Backup para VMs do sql server (pré-visualização pública), consulte [Azure Backup para SQL VMs](../azure-sql/virtual-machines/windows/backup-restore.md#azbackup).

---
title: Backup da base de dados do Servidor SQL de resolução de problemas
description: Informações de resolução de problemas para o backup das bases de dados do SQL Server em execução em VMs Azure com Backup Azure.
ms.topic: troubleshooting
ms.date: 06/18/2019
ms.openlocfilehash: 69cae196e7fad70d75fb12709e5bf0d618bbc81c
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77602318"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Backup da base de dados Do Servidor SQL de sessão de problemas utilizando a cópia de segurança do Azure

Este artigo fornece informações de resolução de problemas para bases de dados do SQL Server em execução em máquinas virtuais Azure.

Para obter mais informações sobre o processo de backup e limitações, consulte [a cópia de segurança do SQL Server em VMs Azure](backup-azure-sql-database.md#feature-consideration-and-limitations).

## <a name="sql-server-permissions"></a>Permissões do Servidor SQL

Para configurar a proteção de uma base de dados do SQL Server numa máquina virtual, tem de instalar a extensão **AzureBackupWindowsWorkload** nessa máquina virtual. Se obtém o erro **UserErrorSQLNoSysadminMembership,** significa que a sua instância do Servidor SQL não tem as permissões de backup necessárias. Para corrigir este erro, siga os passos nas [permissões VM definidas](backup-azure-sql-database.md#set-vm-permissions).

## <a name="troubleshoot-discover-and-configure-issues"></a>Problemas descobrem e configuram problemas
Depois de criar e configurar um cofre de Serviços de Recuperação, descobrir bases de dados e configurar cópias de segurança é um processo em duas etapas.<br>

![sql](./media/backup-azure-sql-database/sql.png)

Durante a configuração de backup, se o VM SQL e as suas instâncias não forem visíveis nos **DBs Discovery em VMs** e **Configurar Backup** (consulte a imagem acima) certifique-se de que:

### <a name="step-1-discovery-dbs-in-vms"></a>Passo 1: DBs de descoberta em VMs

- Se o VM não estiver listado na lista de VM descoberta e também não estiver registado para cópia de segurança SQL noutro cofre, siga os passos de backup do [Discovery SQL Server.](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#discover-sql-server-databases)

### <a name="step-2-configure-backup"></a>Passo 2: Configurar cópia de segurança

- Se o cofre em que o SQL VM está registado no mesmo cofre utilizado para proteger as bases de dados, siga os passos de [Backup configure.](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#configure-backup)

Se o SQL VM precisa de ser registado no novo cofre, então deve não estar registado no cofre antigo.  O não registo de um VM SQL do cofre requer que todas as fontes de dados protegidas sejam protegidas e, em seguida, pode eliminar os dados de backup. Apagar dados de apoio é uma operação destrutiva.  Depois de ter revisto e tomado todas as precauções para desregistar o VM SQL, em seguida, registe este mesmo VM com um novo cofre e tente novamente a operação de backup.



## <a name="error-messages"></a>Mensagens de erro

### <a name="backup-type-unsupported"></a>Tipo de cópia de segurança não suportado

| Gravidade | Descrição | Possíveis causas | Ação recomendada |
|---|---|---|---|
| Aviso | As definições atuais desta base de dados não suportam certos tipos de backup presentes na política associada. | <li>Apenas uma operação de backup de base de dados completa pode ser realizada na base de dados principal. Nem a cópia de segurança diferencial nem a cópia de segurança do registo de transações são possíveis. </li> <li>Qualquer base de dados do modelo de recuperação simples não permite a cópia de segurança dos registos de transações.</li> | Modifique as definições da base de dados de modo a que todos os tipos de backup da política sejam suportados. Ou, alterar a política atual para incluir apenas os tipos de backup suportados. Caso contrário, os tipos de backup não suportados serão ignorados durante o backup programado ou o trabalho de backup falhará para o backup a pedido.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| Esta base de dados SQL não suporta o tipo de cópia de segurança pedido | Ocorre quando o modelo de recuperação da base de dados não permite o tipo de cópia de segurança solicitada. O erro pode acontecer nas seguintes situações: <br/><ul><li>Uma base de dados que está a usar um modelo de recuperação simples não permite a cópia de segurança de registo.</li><li>As cópias de segurança diferenciais e de registo não são permitidas para uma base de dados principal.</li></ul>Para mais detalhes, consulte a documentação dos modelos de recuperação do [Servidor SQL.](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) | Se a cópia de segurança de registo falhar na base de dados no modelo de recuperação simples, experimente uma destas opções:<ul><li>Se a base de dados estiver em modo de recuperação simples, desative as cópias de segurança.</li><li>Utilize a documentação do [SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) para alterar o modelo de recuperação da base de dados para registo completo ou a granel. </li><li> Se não quiser alterar o modelo de recuperação, e tiver uma política padrão para fazer o backup de várias bases de dados que não podem ser alteradas, ignore o erro. Os seus backups completos e diferenciais funcionarão por horário. As cópias de segurança de registo serão ignoradas, o que é esperado neste caso.</li></ul>Se for uma base de dados principal e tiver configurado o diferencial ou a cópia de segurança de registo, utilize qualquer uma das seguintes etapas:<ul><li>Use o portal para alterar o calendário de política de backup para a base de dados principal, para o completo.</li><li>Se tiver uma política padrão para fazer o apoio a várias bases de dados que não podem ser alteradas, ignore o erro. O seu reforço completo funcionará por horário. As cópias de segurança diferenciais ou de registo não vão acontecer, o que é esperado neste caso.</li></ul> |
| A operação cancelada como operação conflituosa já estava a decorrer na mesma base de dados. | Consulte a entrada do [blog sobre backup e restaure as limitações](https://deep.data.blog/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database/) que funcionam simultaneamente.| Utilize o Estúdio de [Gestão de Servidores SQL (SSMS) para monitorizar os trabalhos](manage-monitor-sql-database-backup.md)de backup . Depois de a operação conflituosa falhar, reinicie a operação.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| A base de dados SQL não existe. | A base de dados foi apagada ou renomeada. | Verifique se a base de dados foi acidentalmente eliminada ou renomeada.<br/><br/> Se a base de dados tiver sido acidentalmente eliminada, para continuar as cópias de segurança, restaure a base de dados para a localização original.<br/><br/> Se apagou a base de dados e não precisar de cópias de segurança futuras, em seguida, no cofre dos Serviços de Recuperação, selecione Parar de **reserva** com dados de **backup** ou eliminar dados de **backup**. Para mais informações, consulte gerir e monitorizar bases de dados do [SQL Server .](manage-monitor-sql-database-backup.md)

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| A cadeia de registos está quebrada. | A base de dados ou o VM são apoiados através de outra solução de reserva, que trunca a cadeia de registos.|<ul><li>Verifique se outra solução de reserva ou script está a ser utilizada. Em caso afirmativo, pare a outra solução de reserva. </li><li>Se a cópia de segurança for uma cópia de segurança a pedido, inicie uma cópia de segurança completa para iniciar uma nova cadeia de registos. Para cópias de segurança de registo programadas, não é necessária qualquer ação porque o serviço de backup Azure irá automaticamente acionar uma cópia de segurança completa para corrigir este problema.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| O Azure Backup não é capaz de se ligar à instância SQL. | A Cópia de Segurança Azure não pode ligar-se à instância do Servidor SQL. | Utilize os detalhes adicionais no menu de erro do portal Azure para reduzir as causas da raiz. Consulte a resolução de problemas de [backup SQL](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) para corrigir o erro.<br/><ul><li>Se as definições SQL padrão não permitirem ligações remotas, altere as definições. Consulte os seguintes artigos para obter informações sobre a alteração das definições:<ul><li>[MSSQLSERVER_-1](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-1-database-engine-error?view=sql-server-ver15)</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Se houver problemas de login, utilize estes links para os corrigir:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| Falta a primeira cópia de segurança para esta fonte de dados. | Falta cópia de segurança para a base de dados. As cópias de segurança de log e diferencial são os pais de uma cópia de segurança completa, por isso certifique-se de que aceita cópias de segurança completas antes de desencadear backups diferenciais ou de registo. | Desencadeie um reforço completo a pedido.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| Não é possível aceitar a cópia de segurança, uma vez que o registo de transações para a fonte de dados está cheio. | O espaço de registo transacional da base de dados está cheio. | Para corrigir este problema, consulte a documentação do [Servidor SQL](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| Base de dados com o mesmo nome já existe no local-alvo | O destino de restauro alvo já tem uma base de dados com o mesmo nome.  | <ul><li>Mude o nome da base de dados do alvo.</li><li>Ou, use a opção de sobreutilização da força na página de restauro.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| O restauro falhou, porque não foi possível colocar a base de dados offline. | Enquanto estás a fazer um restauro, a base de dados do alvo tem de ser desligada. O Azure Backup não pode desativar estes dados. | Utilize os detalhes adicionais no menu de erro do portal Azure para reduzir as causas da raiz. Para obter mais informações, veja a [documentação do SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

### <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| Não é possível encontrar o certificado do servidor com impressão digital no alvo. | A base de dados principal na instância de destino não tem uma impressão digital de encriptação válida. | Importar a impressão digital do certificado válido utilizada na instância de origem, para a instância-alvo. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| A cópia de segurança do registo utilizada para recuperação contém alterações registadas em massa. Não pode ser utilizada para parar num ponto no tempo arbitrário de acordo com as diretrizes SQL. | Quando uma base de dados está em modo de recuperação a granel, os dados entre uma transação a granel e a próxima transação de registo não podem ser recuperados. | Escolha um ponto diferente no tempo para a recuperação. [Saiba mais](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server?view=sql-server-ver15).

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| Não é possível respeitar a preferência de cópia de segurança do Grupo de Disponibilidade AlwaysOn do SQL, uma vez que alguns nós do Grupo de Disponibilidade não estão registados. | Os nós necessários para executar cópias de segurança não estão registados ou são inacessíveis. | <ul><li>Certifique-se de que todos os nós necessários para executar cópias de segurança desta base de dados estão registados e saudáveis e, em seguida, tente novamente a operação.</li><li>Altere a preferência de cópia de segurança para o grupo de disponibilidade do SQL Server Always On.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| O VM do servidor SQL é desligado e não é acessível ao serviço de backup Azure. | O VM está desligado. | Certifique-se de que a instância do Servidor SQL está a funcionar. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| O serviço De backup Azure utiliza o agente convidado Azure VM para fazer backup, mas o agente convidado não está disponível no servidor alvo. | O agente convidado não está ativado ou não é saudável. | [Instale manualmente o agente convidado VM.](../virtual-machines/extensions/agent-windows.md) |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| A proteção automática Intenção foi removida ou não é mais válida. | Quando ativa a proteção automática numa instância do Servidor SQL, os trabalhos de **backup configuram** todas as bases de dados nesse caso. Se desativar a auto-protecção enquanto os postos de trabalho estão em funcionamento, os postos de trabalho **em curso** são cancelados com este código de erro. | Ativar a proteção automática mais uma vez para ajudar a proteger todas as bases de dados restantes. |

### <a name="clouddosabsolutelimitreached"></a>CloudDosAbsoluteLimitReached

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
A operação está bloqueada, uma vez que atingiu o limite de operações permitidas em 24 horas. | Quando atingiu o limite máximo admissível para uma operação num espaço de 24 horas, este erro surge. <br> Por exemplo: Se atingir o limite para o número de trabalhos de backup configurados que podem ser desencadeados por dia, e tentar configurar a cópia de segurança num novo item, verá este erro. | Normalmente, tentar a operação após 24 horas resolve este problema. No entanto, se o problema persistir, pode contactar o suporte da Microsoft para obter ajuda.

### <a name="clouddosabsolutelimitreachedwithretry"></a>CloudDosAbsoluteLimitReachedWithRetry

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
A operação está bloqueada, uma vez que o cofre atingiu o seu limite máximo para tais operações permitidas num espaço de 24 horas. | Quando atingiu o limite máximo admissível para uma operação num espaço de 24 horas, este erro surge. Este erro geralmente ocorre quando há operações à escala, tais como modificar a política ou a auto-protecção. Ao contrário do que acontece com cloudDosAbsoluteLimitReached, não há muito que possa fazer para resolver este estado, na verdade, o serviço de backup Azure irá rejulgar as operações internamente para todos os itens em questão.<br> Por exemplo: Se tiver um grande número de fontes de dados protegidas com uma política e tentar modificar essa política, irá desencadear trabalhos de proteção configurados para cada um dos itens protegidos e, por vezes, pode atingir o limite máximo permitido para tais operações por dia.| O serviço de backup Azure irá automaticamente voltar a tentar esta operação após 24 horas.

### <a name="usererrorvminternetconnectivityissue"></a>UserErrorVMInternetConnectivityIssue

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
O VM não é capaz de contactar o serviço Azure Backup devido a problemas de conectividade na Internet. | O VM necessita de conectividade de saída para o Serviço de Backup Azure, armazenamento Azure ou serviços de Diretório Ativo Azure.| - Se utilizar o NSG para restringir a conectividade, deverá utilizar a etiqueta de serviço AzureBackup para permitir o acesso de saída ao Serviço de Backup Azure, ao Azure Storage ou aos serviços de Diretório Ativo Azure. Siga estes [passos](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#allow-access-using-nsg-tags) para conceder acesso.<br>- Certifique-se de que o DNS está a resolver os pontos finais do Azure.<br>- Verifique se o VM está por detrás de um equilibrista de carga que bloqueia o acesso à Internet. Ao atribuir IP público aos VMs, a descoberta funcionará.<br>- Verifique se não existe firewall/antivírus/proxy que esteja a bloquear chamadas para os três serviços-alvo acima.


## <a name="re-registration-failures"></a>Falhas de reregisto

Verifique se há um ou mais dos seguintes sintomas antes de desencadear a operação de reregisto:

* Todas as operações (tais como backup, restauro e cópia de segurança configurada) estão a falhar no VM com um dos seguintes códigos de erro: **WorkloadExtensionNotReachable,** **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent,** **WorkloadExtensionDidntDequeueMsg**.
* Se a área de Estado de **Reserva** para o item de reserva estiver a mostrar **não alcançável,** exclua todas as outras causas que possam resultar no mesmo estado:

  * Falta de autorização para realizar operações relacionadas com backup no VM.
  * Encerramento do VM, por isso não podem ocorrer reforços.
  * Problemas de rede.

   ![re-registar vM](./media/backup-azure-sql-database/re-register-vm.png)



* No caso de um grupo de disponibilidade Always On, os backups começaram a falhar depois de ter alterado a preferência de backup ou após uma falha.

Estes sintomas podem surgir por uma ou mais das seguintes razões:

* Uma extensão foi eliminada ou desinstalada a partir do portal.
* Uma extensão foi desinstalada do Painel de **Controlo** no VM em **Desinstalar ou Alterar um Programa**.
* O VM foi restaurado no tempo através da restauração do disco no local.
* O VM foi desligado por um período prolongado, pelo que a configuração da extensão expirou.
* O VM foi eliminado, e outro VM foi criado com o mesmo nome e no mesmo grupo de recursos que o VM eliminado.
* Um dos nós do grupo de disponibilidade não recebeu a configuração completa de backup. Isto pode acontecer quando o grupo de disponibilidade está registado no cofre ou quando um novo nó é adicionado.

Nos cenários anteriores, recomendamos que desencadeie uma nova operação de registo no VM. Consulte [aqui](https://docs.microsoft.com/azure/backup/backup-azure-sql-automation#enable-backup) as instruções sobre como executar esta tarefa no PowerShell.

## <a name="size-limit-for-files"></a>Limite de tamanho para ficheiros

O tamanho total das cordas dos ficheiros depende não só do número de ficheiros, mas também dos seus nomes e caminhos. Para cada ficheiro de base de dados, obtenha o nome lógico do ficheiro e o caminho físico. Pode utilizar esta consulta SQL:

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

Se o tamanho da cadeia do conteúdo exceder 20.000 bytes, os ficheiros de base de dados são armazenados de forma diferente. Durante a recuperação, não poderá definir o caminho do ficheiro alvo para restaurar. Os ficheiros serão restaurados ao caminho SQL predefinido fornecido pelo SQL Server.

### <a name="override-the-default-target-restore-file-path"></a>Anular a trajetória de ficheiro de restauro do alvo padrão

Pode substituir o caminho de ficheiro de restauro do alvo durante a operação de restauro, colocando um ficheiro JSON que contém o mapeamento do ficheiro base de dados para o caminho de restauro do alvo. Crie um ficheiro `database_name.json` e coloque-o no local *C:\Program Files\Azure Workload Backup\bin\plugins\SQL*.

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

No conteúdo anterior, pode obter o nome lógico do ficheiro base de dados utilizando a seguinte consulta SQL:

```sql
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```

Este ficheiro deve ser colocado antes de acionar a operação de restauro.

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o Backup Azure para VMs de servidor SQL (pré-visualização pública), consulte [o Backup Azure para VMs SQL](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).

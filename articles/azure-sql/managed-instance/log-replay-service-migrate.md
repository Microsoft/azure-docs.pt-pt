---
title: Migrar bases de dados para SQL Managed Instance usando o Serviço de Reprodução de Registos
description: Saiba como migrar bases de dados do SQL Server para SQL Managed Instance usando o Serviço de Reprodução de Registos
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 02/23/2021
ms.openlocfilehash: 73963763716d7e18b757b5ade8998f23cc589fdb
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661363"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-using-log-replay-service"></a>Migrar bases de dados do SQL Server para SQL Managed Instance usando o Serviço de Reprodução de Registos
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo explica como configurar manualmente a migração da base de dados do SQL Server 2008-2019 para a SQL Managed Instance usando o Log Replay Service (LRS). Este é um serviço de nuvem ativado para Instância Gerida com base na tecnologia de envio de registos sql server. O LRS deve ser utilizado nos casos em que o Serviço de Migração de Dados (DMS) não pode ser utilizado, quando é necessário mais controlo, ou quando existe pouca tolerância para o tempo de inatividade.

## <a name="when-to-use-log-replay-service"></a>Quando utilizar o Serviço de Reprodução de Registos

Nos casos em que [o Azure DMS](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance) não possa ser utilizado para migração, o serviço de nuvem LRS pode ser utilizado diretamente com powerShell, cmdlets CLI ou API, para construir e orquestrar manualmente migrações de bases de dados para SQL Managed Instance. 

É melhor considerar a utilização do serviço de nuvem LRS em alguns dos seguintes casos:
- É necessário mais controlo para o seu projeto de migração de bases de dados
- Existe um pouco de tolerância para o tempo de inatividade no corte migratório
- DMS executável não pode ser instalado no seu ambiente
- DMS executável não tem acesso a backups de bases de dados
- Não há acesso ao host OS, ou nenhum privilégio de Administrador
- Incapaz de abrir portas de rede do seu ambiente para Azure

> [!NOTE]
> A forma automatizada recomendada de migrar bases de dados do SQL Server para o SQL Managed Instance está a utilizar o Azure DMS. Este serviço está a utilizar o mesmo serviço de nuvem LRS na parte de trás com o envio de registos no modo NORECOVERY. Deve considerar a utilização manual do LRS para orquestrar migrações em casos em que o Azure DMS não suporta totalmente os seus cenários.

## <a name="how-does-it-work"></a>Como funciona

A construção de uma solução personalizada utilizando LRS para migrar bases de dados para a nuvem requer várias etapas de orquestração mostradas no diagrama e descritas na tabela abaixo.

A migração consiste em fazer cópias de dados completas no SQL Server com o CHECKSUM ativado e copiar ficheiros de backup para o Azure Blob Storage. O LRS é utilizado para restaurar ficheiros de backup do Azure Blob Storage para SQL Managed Instance. O Azure Blob Storage é usado como um armazenamento intermediário entre o SQL Server e o SQL Managed Instance.

O LRS monitorizará o Azure Blob Storage para qualquer novo diferencial, ou registará cópias de segurança adicionadas após a restauração da cópia de segurança completa, e irá restaurar automaticamente quaisquer novos ficheiros adicionados. O progresso dos ficheiros de backup que estão a ser restaurados em SQL Managed Instance pode ser monitorizado usando o serviço, e o processo também pode ser abortado se necessário.

O LRS não requer uma convenção específica de nomeação de ficheiros de cópia de segurança, uma vez que digitaliza todos os ficheiros colocados no Azure Blob Storage e constrói a cadeia de backup a partir apenas da leitura dos cabeçalhos de ficheiro. As bases de dados estão em estado de "restauração" durante o processo de migração, uma vez que são restauradas no modo [NORECOVERY,](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=sql-server-ver15#comparison-of-recovery-and-norecovery) e não podem ser utilizadas para leitura ou escrita até que o processo de migração esteja totalmente concluído. 

Ao migrar várias bases de dados, as cópias de segurança de cada base de dados devem ser colocadas numa pasta separada no Azure Blob Storage. O LRS tem de ser iniciado separadamente para cada base de dados e devem ser especificados caminhos diferentes para separar as pastas de armazenamento Azure Blob. 

O LRS pode ser iniciado em modo auto-completa ou contínuo. Quando iniciada no modo de preenchamento automático, a migração completa-se automaticamente quando o último nome do ficheiro de cópia de segurança especificado tiver sido restaurado. Quando iniciado em modo contínuo, o serviço irá restaurar continuamente quaisquer novos ficheiros de backup adicionados, e a migração será concluída apenas na redução manual. Recomenda-se que o corte manual seja executado apenas após a cópia de segurança final da cauda de log-tail ter sido tomada e mostrada como restaurado em SQL Managed Instance. O passo final de corte fará com que a base de dados fique on-line e disponível para utilização de leitura e escrita em SQL Managed Instance.

Uma vez interrompido o LRS, quer automaticamente, quer em corte manual, o processo de restauro não pode ser retomado para uma base de dados que foi introduzida online em SQL Managed Instance. Para restaurar ficheiros de cópia de segurança adicionais uma vez que a migração foi concluída através de autocomplete, ou manualmente em corte, a base de dados precisa de ser eliminada e toda a cadeia de backup precisa de ser restaurada do zero reiniciando o LRS.

  ![Passos de orquestração do Serviço de Reprodução de Registo explicados para SQL Managed Instance](./media/log-replay-service-migrate/log-replay-service-conceptual.png)

| Operação | Detalhes |
| :----------------------------- | :------------------------- |
| **1. Copiar cópias de segurança da base de dados do SQL Server para o Azure Blob Storage**. | - Copiar cópias completas, diferenciais e registar backups do SQL Server para o Azure Blob Storage Container utilizando [a Azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10), ou [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). <br />- Utilize nomes de ficheiros, uma vez que o LRS não requer uma convenção específica de nomeação de ficheiros.<br />- Na migração de várias bases de dados, é necessária uma pasta separada para cada base de dados. |
| **2. Inicie o serviço LRS na nuvem**. | - O serviço pode ser iniciado com uma escolha de cmdlets: <br /> PowerShell [start-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_start cmdlets](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start). <br /> - Inicie o LRS separadamente para cada base de dados diferente que indique uma pasta de backup diferente no Azure Blob Storage. <br />- Uma vez iniciado, o serviço irá retirar cópias de segurança do recipiente de armazenamento Azure Blob e começar a restaurá-los em SQL Managed Instance.<br /> - Caso o LRS tenha sido iniciado em modo contínuo, uma vez que todas as cópias de segurança inicialmente carregadas sejam restauradas, o serviço irá observar quaisquer novos ficheiros carregados para a pasta e aplicará continuamente registos com base na cadeia LSN, até que o serviço seja interrompido. |
| **2.1. Acompanhar o progresso da operação**. | - O progresso da operação de restauro pode ser monitorizado com uma escolha de ou cmdlets: <br /> PowerShell [get-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_show cmdlets](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show). |
| **2.2. Parar\abortar a operação se necessário**. | - No caso de o processo de migração ter de ser abortado, a operação pode ser interrompida com uma escolha de cmdlets: <br /> PowerShell [stop-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_stop](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop) cmdlets. <br /><br />- Isto resultará na supressão da base de dados restaurada em SQL Managed Instance. <br />- Uma vez parado, o LRS não pode ser retomado para uma base de dados. O processo de migração tem de ser reiniciado do zero. |
| **3. Corte na nuvem quando estiver pronto**. | - Uma vez que todas as cópias de segurança tenham sido restauradas para a SQL Managed Instance, complete o cutover iniciando o funcionamento completo do LRS com uma escolha de cmdlets: <br />PowerShell [complete-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_complete](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete) cmdlets. <br /><br />- Isto fará com que o serviço LRS seja interrompido e a base de dados fique online para ser lida e escrita em SQL Managed Instance.<br /> - Reponte a cadeia de ligação de aplicação do SQL Server para a SQL Managed Instance. |

## <a name="requirements-for-getting-started"></a>Requisitos para começar

### <a name="sql-server-side"></a>Lado do servidor SQL
- SQL Server 2008-2019
- Cópia de segurança completa das bases de dados (um ou vários ficheiros)
- Cópia de segurança diferencial (um ou vários ficheiros)
- Registar cópia de segurança (não dividida para ficheiro de registo de transações)
- **O CHECKSUM deve ser ativado** para cópias de segurança (obrigatórias)

### <a name="azure-side"></a>Lado azul
- PowerShell Az.SQL versão do módulo 2.16.0, ou superior[(instalar,](https://www.powershellgallery.com/packages/Az.Sql/)ou utilizar a Azure [Cloud Shell)](https://docs.microsoft.com/azure/cloud-shell/)
- Versão CLI 2.19.0, ou superior[(instalação)](https://docs.microsoft.com/cli/azure/install-azure-cli)
- Recipiente de armazenamento Azure Blob abastado
- Ficha de segurança SAS com **leitura** e **lista** apenas permissões geradas para o recipiente de armazenamento de bolhas

### <a name="migrating-multiple-databases"></a>Migração de várias bases de dados
- Os ficheiros de cópia de segurança para diferentes bases de dados devem ser colocados em pastas separadas no Armazenamento Azure Blob.
- O LRS tem de ser iniciado separadamente para cada base de dados que indique uma pasta apropriada no Armazenamento Azure Blob.
- O LRS pode suportar até 100 processos de restauro simultâneos por cada exemplo gerido sql único.

### <a name="azure-rbac-permissions-required"></a>Permissões Azure RBAC necessárias
A execução do LRS através dos clientes fornecidos requer uma das seguintes funções da Azure:
- Função do Proprietário de Assinatura, ou
- [Papel de contribuinte de instância gerida,](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) ou
- Função personalizada com a seguinte permissão:
  - `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>Melhores práticas

São altamente recomendadas as melhores práticas:
- Executar [o Assistente de Migração de Dados](https://docs.microsoft.com/sql/dma/dma-overview) para validar as suas bases de dados está pronto para ser migrado para a SQL Managed Instance. 
- Divida cópias de segurança completas e diferenciais em vários ficheiros, em vez de um único ficheiro.
- Ativar a compressão de backup.
- Utilize a Cloud Shell para executar scripts, uma vez que será sempre atualizado para os mais recentes cmdlets lançados.
- Planeia concluir a migração dentro de 47 horas desde que o serviço LRS foi iniciado. Este é um período de graça que impede patches de software geridos pelo sistema uma vez iniciado o LRS.

> [!IMPORTANT]
> - A base de dados restaurada utilizando o LRS não pode ser utilizada até que o processo de migração esteja concluído. Isto porque a tecnologia subjacente é restaurada no modo NORECOVERY.
> - O modo de restauro STANDBY que permite o acesso apenas à leitura das bases de dados durante a migração não é suportado pelo LRS devido às diferenças de versão entre sql Managed Instance e in-market SQL Servers.
> - Uma vez concluída a migração através do auto-completo, ou sobre o corte manual, o processo de migração é finalizado, uma vez que o LRS não suporta o currículo de restauro.

## <a name="steps-to-execute"></a>Passos a executar

### <a name="make-backups-on-the-sql-server"></a>Faça cópias de segurança no SqL Server

As cópias de segurança no SQL Server podem ser feitas com uma das duas opções seguintes:

- Cópia de segurança para o armazenamento de discos locais e, em seguida, faça upload de ficheiros para O Armazenamento Azure Blob, caso o seu ambiente seja restritivo de cópia de segurança direta para o Armazenamento Azure Blob.
- Backup diretamente para Azure Blob Storage com opção "TO URL" em T-SQL, caso o seu ambiente e procedimentos de segurança lhe permitam fazê-lo. 

Desemote bases de dados que deseja migrar para o modo de recuperação completo para permitir cópias de segurança de registo.

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery model.
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

Para fazer manualmente a cópia de segurança completa, difusa e registar a cópia de segurança da sua base de dados no armazenamento local, utilize os scripts T-SQL fornecidos abaixo. Certifique-se de que a opção CHECKSUM está ativada, uma vez que este é um requisito obrigatório para o LRS.

```SQL
-- Example on how to make full database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_full_14_43.bak',
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example on how to make differential database backup to the locak disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_diff_14_44.bak',
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example on how to make the log backup
BACKUP LOG [SampleDB]
TO DISK='C:\BACKUP\SampleDB_log_14_45.bak',
WITH CHECKSUM
GO
```

Os ficheiros enviados até ao armazenamento local terão de ser enviados para o Armazenamento Azure Blob. Caso a sua política corporativa o permita, a forma alternativa de fazer backups diretamente para o Azure Blob Storage é documentada no seguinte tutorial: Use o [serviço de armazenamento Azure Blob com o SQL Server](https://docs.microsoft.com/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage). Se utilizar esta abordagem alternativa, certifique-se de que todas as cópias de segurança são feitas com a opção CHECKSUM ativada.

### <a name="copy-backups-from-sql-server-to-azure-blob-storage"></a>Cópia de backups do SQL Server para Azure Blob Storage

Algumas das seguintes abordagens podem ser utilizadas para enviar cópias de segurança para o armazenamento de bolhas em bases de dados migratórias para casos geridos utilizando LRS:
- Utilizando a funcionalidade DE BACKUP NATIVA SQL [Server PARA URL.](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)
- Utilizando [a Azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10), ou [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer) para enviar cópias de segurança para um recipiente de bolhas.
- Utilização do Explorador de Armazenamento no portal Azure.

### <a name="create-azure-blob-and-sas-authentication-token"></a>Criar ficha de autenticação Azure Blob e SAS

O Azure Blob Storage é usado como um armazenamento intermediário para ficheiros de backup entre o SQL Server e o SQL Managed Instance. Siga estes passos para criar o recipiente de armazenamento Azure Blob:

1. [Criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)
2. [Creta um recipiente de bolha](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) dentro da conta de armazenamento

Uma vez criado um recipiente blob, gere um token de autenticação SAS com permissões de Leitura e Lista apenas seguindo estes passos:

1. Conta de armazenamento de acesso usando portal Azure
2. Navegue para o Explorador de Armazenamento
3. Expandir recipientes blob
4. Clique direito no recipiente blob
5. Selecione Obter Assinatura de Acesso Partilhado
6. Selecione o prazo de validade do símbolo. Certifique-se de que o token é válido durante a duração da sua migração.
    - Note que o fuso horário do token e o seu SQL Managed Instance podem desajustar. Certifique-se de que o token SAS tem a validade adequada, tendo em conta os fusos horários. Se possível, defina o fuso horário para uma hora mais cedo e mais tarde da sua janela de migração planeada.
8. Certifique-se de que apenas as permissões de Leitura e Lista são selecionadas
9. Clique em criar
10. Copie o símbolo depois do ponto de interrogação "?" e para a frente. O token SAS está tipicamente a começar com "sv=2020-10" no URI para utilização no seu código.

> [!IMPORTANT]
> - As permissões para o token SAS para o armazenamento de blob Azure têm de ser lidas e enumeradas. Em caso de quaisquer outras permissões concedidas para o token de autenticação SAS, o serviço LRS inicial falhará. Estes requisitos de segurança são por desígnio.
> - O símbolo deve ter a validade adequada do tempo. Certifique-se de que os fusos horários entre o símbolo e a instância gerida são tomados em consideração.
> - Certifique-se de que o token é copiado a partir de "sv=2020-10..." até ao fim da cadeia.

### <a name="log-in-to-azure-and-select-subscription"></a>Faça login no Azure e selecione subscrição

Utilize o seguinte cmdlet PowerShell para iniciar sessão no Azure:

```powershell
Login-AzAccount
```

Selecione a subscrição apropriada onde reside a sua SQL Managed Instance utilizando o seguinte cmdlet PowerShell:

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>Iniciar a migração

A migração é iniciada por iniciar o serviço LRS. O serviço pode ser iniciado em modo auto-completa ou contínuo. Quando iniciada no modo de preenchamento automático, a migração completa-se automaticamente quando o último ficheiro de cópia de segurança especificado tiver sido restaurado. Esta opção requer que o comando inicial especifique o nome de ficheiro do último ficheiro de backup. Quando o LRS for iniciado em modo contínuo, o serviço irá restaurar continuamente quaisquer novos ficheiros de cópia de segurança adicionados, e a migração será concluída apenas na redução manual. 

### <a name="start-lrs-in-autocomplete-mode"></a>Iniciar LRS no modo auto-completa

Para iniciar o serviço LRS no modo auto-completa, utilize os seguintes comandos PowerShell ou CLI. Especifique o último nome do ficheiro de cópia de segurança com o parâmetro -LastBackupName. Ao restaurar o último nome do ficheiro de cópia de segurança especificado, o serviço iniciará automaticamente uma redução.

Iniciar LRS no modo autocompleto - Exemplo PowerShell:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoCompleteRestore `
    -LastBackupName "last_backup.bak"
```

Iniciar LRS no modo autocompleto - Exemplo CLI:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>Iniciar LRS em modo contínuo

Iniciar LRS em modo contínuo - Exemplo PowerShell:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Iniciar LRS em modo contínuo - Exemplo CLI:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

> [!IMPORTANT]
> Uma vez iniciado o LRS, quaisquer patches de software geridos pelo sistema serão interrompidos durante as próximas 47 horas. Ao passar esta janela, o próximo patch de software automatizado irá parar automaticamente o LRS em curso. Nesse caso, a migração não pode ser retomada e tem de ser reiniciada do zero. 

## <a name="monitor-the-migration-progress"></a>Monitorizar o progresso da migração

Para monitorizar o progresso da operação de migração, utilize o seguinte comando PowerShell:

```PowerShell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Para monitorizar o progresso da operação de migração, utilize o seguinte comando CLI:

```CLI
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>Parar a migração

No caso de precisar de parar a migração, utilize os seguintes cmdlets. Parar a migração eliminará a base de dados de restauro em SQL Managed Instance devido ao qual não será possível retomar a migração.

Para parar o processo de migração, utilize o seguinte comando PowerShell:

```PowerShell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Para parar o processo de migração, utilize o seguinte comando CLI:

```CLI
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>Complete a migração (modo contínuo)

Caso o LRS seja iniciado em modo contínuo, uma vez que tenha assegurado que todas as cópias de segurança foram restauradas, o início da redução completará a migração. Após a conclusão do corte, a base de dados será migrada e pronta para ler e escrever acesso.

Para completar o processo de migração no modo contínuo LRS, utilize o seguinte comando PowerShell:

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

Para completar o processo de migração no modo contínuo LRS, utilize o seguinte comando CLI:

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="troubleshooting"></a>Resolução de problemas

Assim que iniciar o LRS, utilize os cmdlets de monitorização (obter-azsqlinstancedatabaselogreplay ou az_sql_midb_log_replay_show) para ver o estado da operação. Se, após algum tempo, o LRS não começar com um erro, verifique se há algumas das questões mais comuns:
- A cópia de segurança da base de dados do SQL Server foi feita utilizando a opção **CHECKSUM?**
- As permissões na lista de **símbolos** SAS **são** apenas para o serviço LRS?
- O símbolo sas para LRS foi copiado a partir do ponto de interrogação "?" com conteúdo a começar semelhante a este "sv=2020-02-10..."? 
- O tempo **de validade** do símbolo SAS é aplicável para o intervalo de tempo de início e conclusão da migração? Note que pode haver incompatibilidades devido aos **diferentes fusos horários utilizados** para a SQL Managed Instance e o token SAS. Tente regenerar o token SAS com o alargamento da validade simbólica da janela de tempo antes e depois da data atual.
- O nome da base de dados, o nome do grupo de recursos e o nome de instância gerido são corretamente escritos?
- Se o LRS foi iniciado no modo autocompleto, foi especificado um nome de ficheiro válido para o último ficheiro de cópia de segurança especificado?

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [o Servidor SQL migrar para o sql de instância gerida.](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md)
- Saiba mais sobre [as diferenças entre o SQL Server e o Azure SQL Managed Instance](transact-sql-tsql-differences-sql-server.md).
- Saiba mais sobre [as melhores práticas para custos e cargas de trabalho de tamanho migradas para Azure.](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)

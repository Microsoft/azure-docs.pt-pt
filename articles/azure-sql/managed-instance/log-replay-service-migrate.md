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
ms.date: 02/17/2021
ms.openlocfilehash: 7892b1fe0fcad77d1fde8b44f4a8745b5c7dd334
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654420"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-using-log-replay-service"></a>Migrar bases de dados do SQL Server para SQL Managed Instance usando o Serviço de Reprodução de Registos
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo explica como configurar manualmente a migração da base de dados do SQL Server 2008-2019 para a SQL Managed Instance usando o Log Replay Service (LRS). Este é um serviço de nuvem ativado para instância gerida com base na tecnologia de envio de registos SQL Server em nenhum modo de recuperação. O LRS deve ser utilizado nos casos em que o Serviço de Migração de Dados (DMS) não pode ser utilizado, quando é necessário mais controlo ou quando existe pouca tolerância para o tempo de inatividade.

## <a name="when-to-use-log-replay-service"></a>Quando utilizar o Serviço de Reprodução de Registos

Nos casos em que [o Azure DMS](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance) não possa ser utilizado para migração, o serviço de nuvem LRS pode ser utilizado diretamente com powerShell, cmdlets CLI ou API, para construir e orquestrar manualmente migrações de bases de dados para exemplos geridos pelo SQL. 

É melhor considerar a utilização do serviço de nuvem LRS em alguns dos seguintes casos:
- É necessário mais controlo para o seu projeto de migração de bases de dados
- Existe um pouco de tolerância para o tempo de inatividade no corte migratório
- DMS executável não pode ser instalado no seu ambiente
- DMS executável não tem acesso a backups de bases de dados
- Não há acesso ao host OS, ou nenhum privilégio de Administrador

> [!NOTE]
> A forma automatizada recomendada de migrar bases de dados do SQL Server para o SQL Managed Instance está a utilizar o Azure DMS. Este serviço está a utilizar o mesmo serviço de nuvem LRS na parte de trás com o envio de registos em modo de não recuperação. Deve considerar a utilização manual do LRS para orquestrar migrações em casos em que o Azure DMS não suporta totalmente os seus cenários.

## <a name="how-does-it-work"></a>Como funciona

A construção de uma solução personalizada utilizando lRS para migrar uma base de dados para a nuvem requer vários passos de orquestração mostrados no diagrama e delineados na tabela abaixo.

A migração implica fazer cópias de dados completas no SQL Server e copiar ficheiros de backup para o armazenamento do Azure Blob. O LRS é utilizado para restaurar ficheiros de backup do armazenamento Azure Blob para a 22 22 instance gerida. O armazenamento Azure Blob é usado como um armazenamento intermediário entre o SQL Server e o SQL Managed Instance.

O LRS monitorizará o armazenamento do Azure Blob para qualquer novo diferencial, ou registará cópias de segurança adicionadas após a restauração da cópia de segurança completa, e irá restaurar automaticamente quaisquer novos ficheiros adicionados. O progresso dos ficheiros de backup que estão a ser restaurados em casos geridos pelo SQL pode ser monitorizado através do serviço, e o processo também pode ser abortado se necessário. As bases de dados que estão a ser restauradas durante o processo de migração estarão em modo de restauração e não podem ser utilizadas para ler ou escrever até que o processo esteja concluído.

O LRS pode ser iniciado em modo auto-completa ou contínuo. Quando iniciada no modo de preenchamento automático, a migração completa-se automaticamente quando o último ficheiro de cópia de segurança especificado tiver sido restaurado. Quando iniciado em modo contínuo, o serviço irá restaurar continuamente quaisquer novos ficheiros de backup adicionados, e a migração será concluída apenas na redução manual. O passo final de corte irá disponibilizar bases de dados para leitura e utilização de escrita em SQL Managed Instance. 

  ![Passos de orquestração do Serviço de Reprodução de Registo explicados para SQL Managed Instance](./media/log-replay-service-migrate/log-replay-service-conceptual.png)

| Operação | Detalhes |
| :----------------------------- | :------------------------- |
| **1. Copiar cópias de segurança da base de dados do SQL Server para o armazenamento Azure Blob**. | - Copiar cópias completas, diferenciais e registar backups do SQL Server para o armazenamento Azure Blob utilizando [a Azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) ou [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). <br />- Na migração de várias bases de dados, é necessária uma pasta separada para cada base de dados. |
| **2. Inicie o serviço LRS na nuvem**. | - O serviço pode ser iniciado com uma escolha de cmdlets: <br /> PowerShell [start-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_start cmdlets](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start). <br /><br />- Uma vez iniciado, o serviço irá retirar cópias de segurança do armazenamento Azure Blob e começar a restaurá-los em SQL Managed Instance. <br /> - Uma vez restauradas as cópias de segurança inicialmente carregadas, o serviço irá observar quaisquer novos ficheiros carregados para a pasta e aplicará continuamente registos baseados na cadeia LSN, até que o serviço seja interrompido. |
| **2.1. Acompanhar o progresso da operação**. | - O progresso da operação de restauro pode ser monitorizado com uma escolha de ou cmdlets: <br /> PowerShell [get-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_show cmdlets](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show). |
| **2.2. Parar\abortar a operação se necessário**. | - No caso de o processo de migração ter de ser abortado, a operação pode ser interrompida com uma escolha de cmdlets: <br /> PowerShell [stop-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_stop](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop) cmdlets. <br /><br />- Isto resultará na supressão da base de dados ser restaurada em SQL Managed Instance. <br />- Uma vez parado, o LRS não pode ser continuado para uma base de dados. O processo de migração tem de ser reiniciado do zero. |
| **3. Corte na nuvem quando estiver pronto**. | - Uma vez que todas as cópias de segurança tenham sido restauradas para a SQL Managed Instance, complete o cutover iniciando o funcionamento completo do LRS com uma escolha de chamada API, ou cmdlets: <br />PowerShell [complete-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_complete](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete) cmdlets. <br /><br />- Isto fará com que o serviço LRS seja interrompido e a base de dados sobre Instâncias Geridas será recuperada. <br />- Reponte a cadeia de ligação de aplicação do SQL Server para a SQL Managed Instance. <br />- Na base de dados de conclusão de operação está disponível para operações de R/W na nuvem. |

## <a name="requirements-for-getting-started"></a>Requisitos para começar

### <a name="sql-server-side"></a>Lado do servidor SQL
- SQL Server 2008-2019
- Cópia de segurança completa das bases de dados (um ou vários ficheiros)
- Cópia de segurança diferencial (um ou vários ficheiros)
- Registar cópia de segurança (não dividida para ficheiro de registo de transações)
- **O CHECKSUM deve ser ativado** como obrigatório

### <a name="azure-side"></a>Lado azul
-   PowerShell Az.SQL versão do módulo 2.16.0, ou superior[(instalar,](https://www.powershellgallery.com/packages/Az.Sql/)ou utilizar a Azure [Cloud Shell)](https://docs.microsoft.com/azure/cloud-shell/)
-   Versão CLI 2.19.0, ou superior[(instalação)](https://docs.microsoft.com/cli/azure/install-azure-cli)
-   Armazenamento Azure Blob abastado
-   Ficha de segurança SAS com **leitura** e **lista** apenas permissões geradas para o armazenamento de bolhas

## <a name="best-practices"></a>Melhores práticas

São altamente recomendadas as melhores práticas:
- Executar [o Assistente de Migração de Dados](https://docs.microsoft.com/sql/dma/dma-overview) para validar as suas bases de dados não terá problemas em migrar para a SQL Managed Instance. 
- Divida cópias de segurança completas e diferenciais em vários ficheiros, em vez de um único ficheiro.
- Ativar a compressão de backup.
- Utilize a Cloud Shell para executar scripts, uma vez que será sempre atualizado para os mais recentes cmdlets lançados.
- Planeia concluir a migração dentro de 47 horas desde que o serviço LRS foi iniciado.

> [!IMPORTANT]
> - A base de dados restaurada utilizando o LRS não pode ser utilizada até que o processo de migração esteja concluído. Isto porque a tecnologia subjacente é o envio de registos em nenhum modo de recuperação.
> - O modo de espera para o envio de registos não é suportado pelo LRS devido às diferenças de versão entre a SQL Managed Instance e a versão mais recente do SQL Server no mercado.

## <a name="steps-to-execute"></a>Passos a executar

## <a name="copy-backups-from-sql-server-to-azure-blob-storage"></a>Cópia de backups do SQL Server para O armazenamento de Azure Blob

As duas abordagens que se seguem podem ser utilizadas para copiar cópias de segurança para o armazenamento de bolhas em bases de dados migratórias para Instâncias Geridas utilizando LRS:
- Utilizando a funcionalidade DE BACKUP NATIVA SQL [Server PARA URL.](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)
- Copiar as cópias de segurança para o Blob Container utilizando [a Azcopy,](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)ou [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer). 

## <a name="create-azure-blob-and-sas-authentication-token"></a>Criar ficha de autenticação Azure Blob e SAS

O armazenamento Azure Blob é usado como um armazenamento intermediário para ficheiros de backup entre o SQL Server e o SQL Managed Instance. Siga estes passos para criar o recipiente de armazenamento Azure Blob:

1. [Criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)
2. [Creta um recipiente de bolha](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) dentro da conta de armazenamento

Uma vez criado um recipiente blob, gere um token de autenticação SAS com permissões de Leitura e Lista apenas seguindo estes passos:

1. Conta de armazenamento de acesso usando portal Azure
2. Navegue para o Explorador de Armazenamento
3. Expandir recipientes blob
4. Clique direito no recipiente blob
5. Selecione Obter Assinatura de Acesso Partilhado
6. Selecione o prazo de validade do símbolo. Certifique-se de que o token é válido durante a duração da sua migração.
7. Certifique-se de que apenas as permissões de Leitura e Lista são selecionadas
8. Clique em criar
9. Copie o token começando por "sv=" no URI para utilização no seu código

> [!IMPORTANT]
> As permissões para o token SAS para o armazenamento da Azure Blob têm de ser apenas lidas e enumeradas. Em caso de quaisquer outras permissões concedidas para o token de autenticação SAS, o serviço LRS inicial falhará. Estes requisitos de segurança são por desígnio.

## <a name="log-in-to-azure-and-select-subscription"></a>Faça login no Azure e selecione subscrição

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

```powershell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoComplete -LastBackupName "last_backup.bak"
```

Iniciar LRS no modo autocompleto - Exemplo CLI:

```cli
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>Iniciar LRS em modo contínuo

Iniciar LRS em modo contínuo - Exemplo PowerShell:

```powershell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Iniciar LRS em modo contínuo - Exemplo CLI:

```cli
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

> [!IMPORTANT]
> Uma vez iniciado o LRS, quaisquer patches de software geridos pelo sistema serão interrompidos durante as próximas 47 horas. Ao passar esta janela, o próximo patch de software automatizado irá parar automaticamente o LRS em curso. Nesse caso, a migração não pode ser retomada e tem de ser reiniciada do zero. 

## <a name="monitor-the-migration-progress"></a>Monitorizar o progresso da migração

Para monitorizar o progresso da operação de migração, utilize o seguinte comando PowerShell:

```powershell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Para monitorizar o progresso da operação de migração, utilize o seguinte comando CLI:

```cli
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>Parar a migração

No caso de precisar de parar a migração, utilize os seguintes cmdlets. Parar a migração eliminará a base de dados de restauro em caso gerido pelo SQL, devido ao qual não será possível retomar a migração.

Para parar o processo de migração, utilize o seguinte comando PowerShell:

```powershell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Para parar o processo de migração, utilize o seguinte comando CLI:

```cli
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>Complete a migração (modo contínuo)

Caso o LRS seja iniciado em modo contínuo, uma vez que tenha assegurado que todas as cópias de segurança foram restauradas, o início da redução completará a migração. Após a conclusão do corte, a base de dados será migrada e pronta para ler e escrever acesso.

Para completar o processo de migração no modo contínuo LRS, utilize o seguinte comando PowerShell:

```powershell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" -LastBackupName "last_backup.bak"
```

Para completar o processo de migração no modo contínuo LRS, utilize o seguinte comando CLI:

```cli
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [o Servidor SQL migrar para o sql de instância gerida.](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md)
- Saiba mais sobre [as diferenças entre o SQL Server e o Azure SQL Managed Instance](transact-sql-tsql-differences-sql-server.md).
- Saiba mais sobre [as melhores práticas para custos e cargas de trabalho de tamanho migradas para Azure.](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)

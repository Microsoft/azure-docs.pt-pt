---
title: Importar um arquivo BACPAC para criar um banco de dados
description: Crie uma base de dados do SQL newAzure ao importar um ficheiro BACPAC.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/20/2019
ms.openlocfilehash: 07949f0aac1d18b88eb3ac59a6e7ad91d6865fcd
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166316"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>Início rápido: importar um arquivo BACPAC para um banco de dados no banco de dados SQL do Azure

Você pode importar um banco de dados SQL Server para um banco de dados no banco de dados SQL do Azure usando um arquivo [BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) . Você pode importar os dados de um arquivo `BACPAC` armazenado no armazenamento de BLOBs do Azure (somente armazenamento Standard) ou do armazenamento local em um local. Para maximizar a velocidade de importação fornecendo recursos mais e mais rápidos, dimensione seu banco de dados para uma camada de serviço mais alta e o tamanho de computação durante o processo de importação. Em seguida, você pode reduzir verticalmente depois que a importação for bem-sucedida.

> [!NOTE]
> Nível de compatibilidade da base de dados importados baseia-se no nível de compatibilidade da base de dados origem.
> [!IMPORTANT]
> Depois de importar a base de dados, pode optar por operar a base de dados em seu nível de compatibilidade atual (nível 100 para a base de dados AdventureWorks2008R2) ou num nível superior. Para obter mais informações sobre as implicações e as opções para o funcionamento de uma base de dados a um nível de compatibilidade específico, veja [ALTERAR Nível de Compatibilidade de BASE DE DADOS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). Veja também [ALTERAR CONFIGURAÇÃO DO ÂMBITO DA BASE DE DADOS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) para obter informações sobre as definições adicionais do nível da base de dados relacionadas com os níveis de compatibilidade.

## <a name="import-from-a-bacpac-file-in-the-azure-portal"></a>Importar a partir de um ficheiro BACPAC no portal do Azure

Assista a este vídeo para ver como importar de um arquivo BACPAC no portal do Azure ou Continue lendo abaixo:

> [!VIDEO hhttps://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

O [portal do Azure](https://portal.azure.com) *só* dá suporte à criação de um único banco de dados no banco de dados SQL do Azure e *somente* de um arquivo BACPAC armazenado no armazenamento de BLOBs do Azure.

Atualmente, não há suporte para migrar um banco de dados para uma [instância gerenciada](sql-database-managed-instance.md) de um arquivo BACPAC usando o Azure PowerShell. Em vez disso, use SQL Server Management Studio ou SqlPackage.

> [!NOTE]
> Os computadores que processam solicitações de importação/exportação enviadas por meio do portal do Azure ou do PowerShell precisam armazenar o arquivo BACPAC, bem como arquivos temporários gerados pela estrutura de aplicativo da camada de dados (DacFX). O espaço em disco necessário varia significativamente entre os bancos de dados com o mesmo tamanho e pode exigir espaço em disco de até 3 vezes o tamanho do banco de dados. Os computadores que executam a solicitação de importação/exportação só têm 450GB espaço em disco local. Como resultado, algumas solicitações podem falhar com o erro `There is not enough space on the disk`. Nesse caso, a solução alternativa é executar SqlPackage. exe em um computador com espaço em disco local suficiente. Incentivamos o uso do [SqlPackage](#import-from-a-bacpac-file-using-sqlpackage) para importar/exportar bancos de dados maiores que 150 GB para evitar esse problema.
 
1. Para importar de um arquivo BACPAC para um novo banco de dados individual usando o portal do Azure, abra a página servidor de banco de dados apropriada e, em seguida, na barra de ferramentas, selecione **importar banco de dados**.  

   ![Import1 do banco de dados](./media/sql-database-import/import1.png)

2. Selecione a conta de armazenamento e o contêiner para o arquivo BACPAC e, em seguida, selecione o arquivo BACPAC do qual importar.
3. Especifique o novo tamanho de base de dados (normalmente, a mesma como origem) e fornecer credenciais do SQL Server de destino. Para obter uma lista de valores possíveis para um novo banco de dados SQL do Azure, consulte [criar banco de dados](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).

   ![Import2 do banco de dados](./media/sql-database-import/import2.png)

4. Clique em **OK**.

5. Para monitorar o progresso de uma importação, abra a página do servidor do banco de dados e, em **configurações**, selecione **histórico de importação/exportação**. Quando for bem-sucedido, a importação tem um **concluído** estado.

   ![Status de importação do banco de dados](./media/sql-database-import/import-status.png)

6. Para verificar se o banco de dados está ativo no servidor de banco de dados, selecione bancos de dados **SQL** e verifique se o novo banco de dados está **online**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>Importar a partir de um ficheiro BACPAC com SqlPackage

Para importar um banco de dados SQL Server usando o utilitário de linha de comando [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) , consulte [importar parâmetros e propriedades](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). O SqlPackage tem as últimas [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e [SQL Server Data Tools para o Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Também pode transferir a versão mais recente [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) do Centro de download da Microsoft.

Para escala e desempenho, é recomendável usar SqlPackage na maioria dos ambientes de produção em vez de usar o portal do Azure. Para obter um blog SQL Server equipe de consultoria ao cliente sobre a migração usando arquivos `BACPAC`, consulte [migrando do SQL Server para o banco de dados SQL do Azure usando arquivos BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

O comando SqlPackage a seguir importa o banco de dados **banco AdventureWorks2008R2** do armazenamento local para um servidor de banco de dados SQL do Azure chamado **mynewserver20170403**. Ele cria uma nova base de dados chamado **myMigratedDatabase** com um **Premium** escalão de serviço e um **P6** objetivo de serviço. Altere estes valores conforme adequado para o seu ambiente.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<your_server_admin_account_user_id>;Password=<your_server_admin_account_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Para se conectar a um servidor de banco de dados SQL que gerencia um banco de dados individual por trás de um firewall corporativo, o firewall deve ter a porta 1433 aberta. Para se conectar a uma instância gerenciada, você deve ter uma [conexão ponto a site](sql-database-managed-instance-configure-p2s.md) ou uma conexão de rota expressa.
>

Este exemplo mostra como importar uma base de dados com SqlPackage com o Active Directory Universal Authentication.

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-into-a-single-database-from-a-bacpac-file-using-powershell"></a>Importar para um banco de dados individual de um arquivo BACPAC usando o PowerShell

> [!NOTE]
> Atualmente, [uma instância gerenciada](sql-database-managed-instance.md) não dá suporte à migração de um banco de dados para um banco de dados de instância de um arquivo BACPAC usando Azure PowerShell. Para importar para uma instância gerenciada, use SQL Server Management Studio ou SqlPackage.

> [!NOTE]
> As máquinas que processam solicitações de importação/exportação enviadas por meio do portal ou do PowerShell precisam armazenar o arquivo bacpac, bem como arquivos temporários gerados pela estrutura de aplicativo da camada de dados (DacFX). O espaço em disco necessário varia significativamente entre os bancos de dados com o mesmo tamanho e pode levar até três vezes do tamanho do banco de dados. Os computadores que executam a solicitação de importação/exportação só têm 450GB espaço em disco local. Como resultado, algumas solicitações podem falhar com o erro "não há espaço suficiente no disco". Nesse caso, a solução alternativa é executar SqlPackage. exe em um computador com espaço em disco local suficiente. Ao importar/exportar bancos de dados maiores do que 150 GB, use [SqlPackage](#import-from-a-bacpac-file-using-sqlpackage) para evitar esse problema.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

Use o cmdlet [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) para enviar uma solicitação de importação de banco de dados para o serviço de banco de dados SQL do Azure. Dependendo do tamanho da base de dados, a importação pode demorar algum tempo a concluir.

 ```powershell
 $importRequest = New-AzSqlDatabaseImport 
    -ResourceGroupName "<your_resource_group>" `
    -ServerName "<your_server>" `
    -DatabaseName "<your_database>" `
    -DatabaseMaxSizeBytes "<database_size_in_bytes>" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey -ResourceGroupName "<your_resource_group>" -StorageAccountName "<your_storage_account").Value[0] `
    -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "<your_server_admin_account_user_id>" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "<your_server_admin_account_password>" -AsPlainText -Force)

 ```

 Você pode usar o cmdlet [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) para verificar o progresso da importação. Executar o cmdlet imediatamente após a solicitação normalmente devolve **Estado: em curso**. A importação estiver concluída, quando vir **Estado: concluída com êxito**.

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
> Para obter outro exemplo de script, consulte [importar uma base de dados de um ficheiro BACPAC](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="limitations"></a>Limitações

Importar uma base de dados no conjunto elástico não é suportada. Você pode importar dados em um único banco de dado e, em seguida, movê-lo para um pool elástico.

## <a name="import-using-wizards"></a>Importar com assistentes

Também pode utilizar estes assistentes.

- [Importar Assistente de aplicação de camada de dados no SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [Importação do SQL Server e o Assistente de exportação](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Passos seguintes

- Para saber como ligar e consultar uma base de dados importados do SQL, veja [início rápido: Azure SQL Database: Utilize o SQL Server Management Studio para ligar e consultar dados](sql-database-connect-query-ssms.md).
- Para saber mais sobre a migração com ficheiros BACPAC num blogue da Equipa de Aconselhamento ao Cliente do SQL Server, consulte [Migrating from SQL Server to Azure SQL Database using BACPAC Files (Migrar a partir do SQL Server para a Base de Dados SQL do Azure com Ficheiros BACPAC)](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- Para uma discussão sobre o inteiro do SQL Server da base de dados do processo de migração, incluindo recomendações de desempenho, consulte [migração de base de dados do SQL Server para a base de dados do Azure SQL](sql-database-single-database-migrate.md).
- Para saber como gerir e partilhar as chaves de armazenamento e acesso partilhado assinaturas de forma segura, consulte [guia de segurança de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).

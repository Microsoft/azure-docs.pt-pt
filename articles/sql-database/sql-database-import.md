---
title: Importar um ficheiro BACPAC para criar uma base de dados SQL do Azure | Documentos da Microsoft
description: Crie uma base de dados do SQL newAzure ao importar um ficheiro BACPAC.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/18/2019
ms.openlocfilehash: 06c74aa85bda13ccd6849056ccc031ae6f1c12c2
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57315559"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>Início rápido: Importar um ficheiro BACPAC para uma base de dados na base de dados do Azure SQL

Pode importar uma base de dados do SQL Server para uma base de dados na base de dados do Azure SQL com uma [BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) ficheiro. Pode importar dados a partir de um `BACPAC` ficheiros armazenados no armazenamento de Blobs do Azure (apenas no armazenamento standard) ou do armazenamento local numa localização no local. Para maximizar velocidade de importação, fornecendo recursos mais e mais rapidamente, dimensionar a sua base de dados para um escalão de serviço superior e tamanho de computação durante o processo de importação. Em seguida, pode reduzir verticalmente quando a importação for concluída com êxito.

> [!NOTE]
> Nível de compatibilidade da base de dados importados baseia-se no nível de compatibilidade da base de dados origem.
> [!IMPORTANT]
> Depois de importar a base de dados, pode optar por operar a base de dados em seu nível de compatibilidade atual (nível 100 para a base de dados AdventureWorks2008R2) ou num nível superior. Para obter mais informações sobre as implicações e as opções para o funcionamento de uma base de dados a um nível de compatibilidade específico, veja [ALTERAR Nível de Compatibilidade de BASE DE DADOS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). Veja também [ALTERAR CONFIGURAÇÃO DO ÂMBITO DA BASE DE DADOS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) para obter informações sobre as definições adicionais do nível da base de dados relacionadas com os níveis de compatibilidade.

## <a name="import-from-a-bacpac-file-in-the-azure-portal"></a>Importar a partir de um ficheiro BACPAC no portal do Azure

O [portal do Azure](https://portal.azure.com) *apenas* suporta a criação de uma base de dados na base de dados do Azure SQL e *apenas* de um ficheiro BACPAC armazenado no armazenamento de Blobs do Azure.

> [!NOTE]
> [Uma instância gerida](sql-database-managed-instance.md) não suporta atualmente a migrar uma base de dados num banco de dados de instância de um ficheiro BACPAC, com o portal do Azure. Para importar para uma instância gerida, utilize o SQL Server Management Studio ou SQLPackage.

1. Para importar-se de um ficheiro BACPAC para uma nova base de dados com o portal do Azure, abra a página de servidor de banco de dados apropriadas e, em seguida, na barra de ferramentas, selecione **importar base de dados**.  

   ![Import1 de base de dados](./media/sql-database-import/import1.png)

2. Selecione a conta de armazenamento e o contentor para o ficheiro BACPAC e, em seguida, selecione o ficheiro BACPAC partir da qual importar.
3. Especifique o novo tamanho de base de dados (normalmente, a mesma como origem) e fornecer credenciais do SQL Server de destino. Para obter uma lista de valores possíveis para uma nova base de dados SQL do Azure, consulte [Create Database](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).

   ![Import2 de base de dados](./media/sql-database-import/import2.png)

4. Clique em **OK**.

5. Para monitorizar o progresso de uma importação, abra a página de servidor da base de dados e, em **configurações**, selecione **importar/exportar histórico**. Quando for bem-sucedido, a importação tem um **concluído** estado.

   ![Estado da importação de base de dados](./media/sql-database-import/import-status.png)

6. Para verificar a base de dados está em direto no servidor de base de dados, selecione **bases de dados SQL** e certifique-se de que a nova base de dados é **Online**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>Importar a partir de um ficheiro BACPAC com SqlPackage

Para importar uma base de dados do SQL Server com o [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) utilitário de linha de comandos, veja [importar parâmetros e as propriedades](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). SqlPackage tem a versão mais recente [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e [SQL Server Data Tools para Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Também pode transferir a versão mais recente [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) do Centro de download da Microsoft.

Para dimensionamento e desempenho, recomendamos que com SqlPackage na maioria dos ambientes de produção, em vez de utilizar o portal do Azure. Para um blog da Equipe Consultiva para SQL Server clientes sobre como migrar utilizando `BACPAC` arquivos, consulte [migrando do SQL Server para o Azure SQL Database using BACPAC ficheiros](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Para dimensionamento e desempenho, recomendamos que utilize SqlPackage na maioria dos ambientes de produção. Para saber mais sobre a migração com ficheiros BACPAC num blogue da Equipa de Aconselhamento ao Cliente do SQL Server, consulte [Migrating from SQL Server to Azure SQL Database using BACPAC Files (Migrar a partir do SQL Server para a Base de Dados SQL do Azure com Ficheiros BACPAC)](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

O seguinte comando SqlPackage importa os **AdventureWorks2008R2** base de dados do armazenamento local para um servidor de base de dados do Azure SQL chamado **mynewserver20170403**. Ele cria uma nova base de dados chamado **myMigratedDatabase** com um **Premium** escalão de serviço e um **P6** objetivo de serviço. Altere estes valores conforme adequado para o seu ambiente.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<your_server_admin_account_user_id>;Password=<your_server_admin_account_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Para ligar a um servidor de base de dados SQL gerir uma base de dados através de uma firewall empresarial, a firewall tem de ter a porta 1433 aberto. Para ligar a uma instância gerida, tem de ter uma [ligação de ponto a site](sql-database-managed-instance-configure-p2s.md) ou uma ligação de expressroute.
>

Este exemplo mostra como importar uma base de dados com SqlPackage com o Active Directory Universal Authentication.

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-into-a-single-database-from-a-bacpac-file-using-powershell"></a>Importar para uma base de dados de um ficheiro BACPAC, com o PowerShell

> [!NOTE]
> [Uma instância gerida](sql-database-managed-instance.md) não suporta atualmente a migrar uma base de dados num banco de dados de instância de um ficheiro BACPAC, com o Azure PowerShell]. Para importar para uma instância gerida, utilize o SQL Server Management Studio ou SQLPackage.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utilize o [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) cmdlet para submeter um pedido de base de dados de importação para o serviço de base de dados do Azure SQL. Dependendo do tamanho da base de dados, a importação pode demorar algum tempo a concluir.

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

 Pode utilizar o [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) cmdlet para verificar o progresso da importação. Executar o cmdlet imediatamente após a solicitação normalmente devolve **Estado: InProgress**. A importação estiver concluída, quando vir **Estado: Foi efetuada com êxito**.

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
Para obter outro exemplo de script, consulte [importar uma base de dados de um ficheiro BACPAC](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="limitations"></a>Limitações

Importar uma base de dados no conjunto elástico não é suportada. Pode importar dados para uma base de dados e, em seguida, mover a base de dados para um conjunto elástico.

## <a name="import-using-wizards"></a>Importar com assistentes

Também pode utilizar estes assistentes.

- [Importar Assistente de aplicação de camada de dados no SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [Importação do SQL Server e o Assistente de exportação](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Passos Seguintes

- Para saber como ligar e consultar uma base de dados importados do SQL, veja [início rápido: Base de Dados SQL do Azure: Utilizar o SQL Server Management Studio para ligar e consultar dados](sql-database-connect-query-ssms.md).
- Para saber mais sobre a migração com ficheiros BACPAC num blogue da Equipa de Aconselhamento ao Cliente do SQL Server, consulte [Migrating from SQL Server to Azure SQL Database using BACPAC Files (Migrar a partir do SQL Server para a Base de Dados SQL do Azure com Ficheiros BACPAC)](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- Para uma discussão sobre o inteiro do SQL Server da base de dados do processo de migração, incluindo recomendações de desempenho, consulte [migração de base de dados do SQL Server para a base de dados do Azure SQL](sql-database-single-database-migrate.md).
- Para saber como gerir e partilhar as chaves de armazenamento e acesso partilhado assinaturas de forma segura, consulte [guia de segurança de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).

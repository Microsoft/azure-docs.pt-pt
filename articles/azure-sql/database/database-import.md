---
title: Importar um ficheiro BACPAC para criar uma base de dados na Base de Dados Azure SQL
description: Crie uma nova base de dados na Base de Dados Azure SQL ou na Azure SQL Managed Instance a partir de um ficheiro BACPAC.
services: sql-database
ms.service: sql-db-mi
ms.subservice: migrate
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/20/2019
ms.openlocfilehash: 08aaec23b0edc0e797d26d4b51081f6daa5b5c19
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92671226"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Quickstart: Importe um ficheiro BACPAC para uma base de dados na Base de Dados Azure SQL ou na Azure SQL Gerenciada Instância
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Pode importar uma base de dados do SQL Server para a Base de Dados Azure SQL ou para a SQL Gerenciada usando um ficheiro [BACPAC.](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) Pode importar os dados de um ficheiro BACPAC no armazenamento de Blobs do Azure (apenas armazenamento padrão) ou do armazenamento local numa localização no local. Para maximizar a velocidade de importação ao fornecer mais recursos e mais rápidos, dimensione a base de dados para um escalão de serviço mais elevado e tamanho da computação durante o processo de importação. Em seguida, pode reduzir verticalmente depois de a importação ser bem-sucedida.

> [!NOTE]
> O nível de compatibilidade da base de dados importada baseia-se no nível de compatibilidade da base de dados de origem.

> [!IMPORTANT]
> Depois de importar a sua base de dados, pode optar por operar a base de dados ao nível atual de compatibilidade (nível 100 para a base de dados AdventureWorks2008R2) ou a um nível mais elevado. Para obter mais informações sobre as implicações e as opções para o funcionamento de uma base de dados a um nível de compatibilidade específico, veja [ALTERAR Nível de Compatibilidade de BASE DE DADOS](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). Veja também [ALTERAR CONFIGURAÇÃO DO ÂMBITO DA BASE DE DADOS](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) para obter informações sobre as definições adicionais do nível da base de dados relacionadas com os níveis de compatibilidade.

## <a name="using-azure-portal"></a>Com o Portal do Azure

Veja este vídeo para ver como importar a partir de um ficheiro BACPAC no portal Azure ou continuar a ler abaixo:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

O [portal Azure](https://portal.azure.com) *suporta apenas* a criação de uma única base de dados na Base de Dados Azure SQL e *apenas* a partir de um ficheiro BACPAC armazenado no armazenamento Azure Blob.

Para migrar uma base de dados para uma [Instância Gerida Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md) a partir de um ficheiro BACPAC, utilize o SQL Server Management Studio ou o SQLPackage, utilizando o portal Azure ou Azure PowerShell não está atualmente suportado.

> [!NOTE]
> As máquinas que processam pedidos de importação/exportação apresentados através do portal Azure ou da PowerShell precisam de armazenar o ficheiro BACPAC, bem como ficheiros temporários gerados pelo Data-Tier Application Framework (DacFX). O espaço em disco necessário varia significativamente entre bases de dados com o mesmo tamanho e pode exigir espaço em disco até 3 vezes o tamanho da base de dados. As máquinas que executam o pedido de importação/exportação têm apenas 450GB de espaço em disco local. Como resultado, alguns pedidos podem falhar com o erro `There is not enough space on the disk` . Neste caso, a solução é para executar sqlpackage.exe numa máquina com espaço suficiente em disco local. Encorajamos a utilização da SqlPackage para importar/exportar bases de dados superiores a 150GB para evitar este problema.

1. Para importar de um ficheiro BACPAC para uma nova base de dados única utilizando o portal Azure, abra a página do servidor apropriada e, em seguida, na barra de ferramentas, selecione **Import database** .  

   ![Importação de base de dados1](./media/database-import/sql-server-import-database.png)

1. Selecione a conta de armazenamento e o recipiente para o ficheiro BACPAC e, em seguida, selecione o ficheiro BACPAC a partir do qual importa.

1. Especifique o novo tamanho da base de dados (geralmente o mesmo que a origem) e forneça as credenciais do servidor SQL de destino. Para obter uma lista de valores possíveis para uma nova base de dados na Base de Dados Azure SQL, consulte [a Base de Dados Create](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).

   ![Importação de base de dados2](./media/database-import/sql-server-import-database-settings.png)

1. Clique em **OK** .

1. Para monitorizar o progresso de uma importação, abra a página do servidor da base de dados e, em **Definições,** selecione **o histórico de Importação/Exportação** . Quando bem sucedida, a importação tem um estado **preenchido.**

   ![Estado de importação de base de dados](./media/database-import/sql-server-import-database-history.png)

1. Para verificar se a base de dados está ao vivo no servidor, selecione **bases de dados SQL** e verifique se a nova base de dados está **Online** .

## <a name="using-sqlpackage"></a>Usando SqlPackage

Para importar uma base de dados SQL Server utilizando o utilitário de linha de comando [SqlPackage,](/sql/tools/sqlpackage) consulte [parâmetros e propriedades de importação](/sql/tools/sqlpackage#import-parameters-and-properties). [O SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e [as Ferramentas de Dados do Servidor SQL para Estúdio Visual](/sql/ssdt/download-sql-server-data-tools-ssdt) incluem SqlPackage. Também pode baixar o mais recente [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) do centro de descarregamento da Microsoft.

Para escala e desempenho, recomendamos a utilização do SqlPackage na maioria dos ambientes de produção em vez de utilizar o portal Azure. Para um blog da SqL Server Customer Advisory Team sobre migração através `BACPAC` de ficheiros, consulte [migrar do SQL Server para a Azure SQL Database utilizando ficheiros BACPAC](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files).

O seguinte comando SqlPackage importa a base de dados **AdventureWorks2008R2** do armazenamento local para um servidor lógico SQL nomeado **mynewserver20170403** . Cria uma nova base de dados chamada **myMigratedDatabase** com um nível de serviço **Premium** e um Objetivo de Serviço **P6.** Altere estes valores conforme apropriado para o seu ambiente.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Para ligar à Base de Dados Azure SQL por detrás de uma firewall corporativa, a firewall deve ter a porta 1433 aberta. Para ligar à SQL Managed Instance, tem de ter uma [ligação ponto-a-local](../managed-instance/point-to-site-p2s-configure.md) ou uma ligação de rota expressa.

Este exemplo mostra como importar uma base de dados usando SqlPackage com Autenticação Universal do Diretório Ativo.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>Com o PowerShell

> [!NOTE]
> [Uma SqL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) não suporta atualmente a migração de uma base de dados para uma base de dados de casos a partir de um ficheiro BACPAC utilizando a Azure PowerShell. Para importar para uma SQL Managed Instance, use o SQL Server Management Studio ou o SQLPackage.

> [!NOTE]
> As máquinas que processam pedidos de importação/exportação apresentados através do portal ou powershell precisam de armazenar o ficheiro bacpac, bem como ficheiros temporários gerados pelo Data-Tier Application Framework (DacFX). O espaço do disco necessário varia significativamente entre DBs com o mesmo tamanho e pode levar até 3 vezes o tamanho da base de dados. As máquinas que executam o pedido de importação/exportação têm apenas 450GB de espaço em disco local. Como resultado, alguns pedidos podem falhar com o erro "Não há espaço suficiente no disco". Neste caso, a solução é para executar sqlpackage.exe numa máquina com espaço suficiente em disco local. Ao importar/exportar bases de dados superiores a 150GB, utilize a SqlPackage para evitar este problema.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager (RM) ainda está suportado, mas todo o desenvolvimento futuro é para o módulo Az.Sql. O módulo AzureRM continuará a receber correções de erros até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para obter mais informações sobre a sua compatibilidade, consulte [a introdução do novo módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

Utilize o [cmdlet New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) para apresentar um pedido de base de dados de importação à Azure. Dependendo do tamanho da base de dados, a importação pode demorar algum tempo a ser concluída.

```powershell
$importRequest = New-AzSqlDatabaseImport -ResourceGroupName "<resourceGroupName>" `
    -ServerName "<serverName>" -DatabaseName "<databaseName>" `
    -DatabaseMaxSizeBytes "<databaseSizeInBytes>" -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey `
        -ResourceGroupName "<resourceGroupName>" -StorageAccountName "<storageAccountName>").Value[0] `
        -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
        -Edition "Standard" -ServiceObjectiveName "P6" `
        -AdministratorLogin "<userId>" `
        -AdministratorLoginPassword $(ConvertTo-SecureString -String "<password>" -AsPlainText -Force)
```

Pode utilizar o [cmdlet Get-AzSqlDataImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) para verificar o progresso da importação. Executar o cmdlet imediatamente após a retoe do pedido normalmente devolvi `Status: InProgress` . A importação fica completa quando se `Status: Succeeded` vê.

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink

[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress") {
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}

[Console]::WriteLine("")
$importStatus
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Utilize o comando [de importação az-sql-db](/cli/azure/sql/db#az-sql-db-import) para apresentar um pedido de base de dados de importação à Azure. Dependendo do tamanho da base de dados, a importação pode demorar algum tempo a ser concluída.

```azurecli
# get the storage account key
az storage account keys list --resource-group "<resourceGroup>" --account-name "<storageAccount>"

az sql db import --resource-group "<resourceGroup>" --server "<server>" --name "<database>" `
    --storage-key-type "StorageAccessKey" --storage-key "<storageAccountKey>" `
    --storage-uri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -u "<userId>" -p "<password>"
```

* * *

> [!TIP]
> Para outro exemplo de script, consulte [Importar uma base de dados a partir de um ficheiro BACPAC](scripts/import-from-bacpac-powershell.md).

## <a name="limitations"></a>Limitações

- A importação para uma base de dados num conjunto elástico não é suportada. Pode importar dados para uma única base de dados e, em seguida, mover a base de dados para um conjunto elástico.
- O Serviço de Exportação de Importações não funciona quando o acesso aos serviços Azure está definido para OFF. No entanto, pode contornar o problema executando manualmente sqlpackage.exe a partir de um VM Azure ou realizando a exportação diretamente no seu código utilizando a API DACFx.
- A Importação não suporta especificar uma redundância de armazenamento de backup enquanto cria uma nova base de dados e cria com o despedimento de armazenamento de backup geo-redundante padrão. Para uma solução alternativa, primeiro crie uma base de dados vazia com a redundância de armazenamento de backup desejada usando o portal Azure ou PowerShell e, em seguida, importe o BACPAC para esta base de dados vazia. 

> [!NOTE]
> Azure SQL Database Configurable Backup Storage Despedimento está atualmente disponível em pré-visualização pública apenas na região do Sudeste Asiático Azure.

## <a name="import-using-wizards"></a>Importar usando assistentes

Também pode utilizar estes assistentes.

- [Assistente de aplicação de nível de dados de importação no SQL Server Management Studio](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [Sql Server Import and Export Wizard](/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Passos seguintes

- Para saber como ligar e consultar uma base de dados na Base de Dados Azure SQL, consulte [Quickstart: Azure SQL Database: Use SQL Server Management Studio para ligar e consultar dados](connect-query-ssms.md).
- Para saber mais sobre a migração com ficheiros BACPAC num blogue da Equipa de Aconselhamento ao Cliente do SQL Server, consulte [Migrating from SQL Server to Azure SQL Database using BACPAC Files (Migrar a partir do SQL Server para a Base de Dados SQL do Azure com Ficheiros BACPAC)](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- Para uma discussão de todo o processo de migração da base de dados do SQL Server, incluindo recomendações de desempenho, consulte [a migração da base de dados do SQL Server para a Base de Dados Azure SQL](migrate-to-database-from-sql-server.md).
- Para aprender a gerir e partilhar as chaves de armazenamento e as assinaturas de acesso partilhadas de forma segura, consulte o [Guia de Segurança do Armazenamento Azure](../../storage/blobs/security-recommendations.md).
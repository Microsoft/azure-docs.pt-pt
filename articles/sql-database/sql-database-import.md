---
title: Importar um ficheiro BACPAC para criar uma base de dados
description: Crie uma nova base de dados Azure SQL importando um ficheiro BACPAC.
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
ms.openlocfilehash: 05698596f966f879da1affc58af0122d08d519ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256240"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>Quickstart: Importar um ficheiro BACPAC para uma base de dados em Azure SQL Database

Pode importar uma base de dados do SQL Server para uma base de dados em Azure SQL Database utilizando um ficheiro [BACPAC.](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) Pode importar os dados `BACPAC` de um ficheiro armazenado no armazenamento do Azure Blob (apenas no armazenamento padrão) ou do armazenamento local num local. Para maximizar a velocidade de importação, fornecendo recursos cada vez mais rápidos, dimensione a sua base de dados para um nível de serviço mais elevado e calcule o tamanho durante o processo de importação. Em seguida, pode reduzir a escala depois de a importação ser bem sucedida.

> [!NOTE]
> O nível de compatibilidade da base de dados importada baseia-se no nível de compatibilidade da base de dados de origem.

> [!IMPORTANT]
> Depois de importar a sua base de dados, pode optar por operar a base de dados ao nível atual de compatibilidade (nível 100 para a base de dados AdventureWorks2008R2) ou a um nível mais elevado. Para obter mais informações sobre as implicações e as opções para o funcionamento de uma base de dados a um nível de compatibilidade específico, veja [ALTERAR Nível de Compatibilidade de BASE DE DADOS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). Veja também [ALTERAR CONFIGURAÇÃO DO ÂMBITO DA BASE DE DADOS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) para obter informações sobre as definições adicionais do nível da base de dados relacionadas com os níveis de compatibilidade.

## <a name="using-azure-portal"></a>Com o Portal do Azure

Veja este vídeo para ver como importar a partir de um ficheiro BACPAC no portal Azure ou continuar a ler abaixo:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

O [portal Azure](https://portal.azure.com) *apenas* suporta a criação de uma única base de dados na Base de Dados Azure SQL e *apenas* a partir de um ficheiro BACPAC armazenado no armazenamento da Blob Azure.

A migração de uma base de dados para uma [instância gerida a](sql-database-managed-instance.md) partir de um ficheiro BACPAC utilizando o Azure PowerShell não é atualmente suportada. Utilize o Estúdio de Gestão de Servidores SQL ou o SQLPackage.

> [!NOTE]
> As máquinas que processam pedidos de importação/exportação apresentados através do portal Azure ou da PowerShell precisam de armazenar o ficheiro BACPAC, bem como ficheiros temporários gerados pelo Quadro de Aplicação de Nível de Dados (DacFX). O espaço do disco necessário varia significativamente entre bases de dados com o mesmo tamanho e pode requerer espaço de disco até 3 vezes o tamanho da base de dados. As máquinas que executam o pedido de importação/exportação têm apenas 450GB de espaço de disco local. Como resultado, alguns pedidos podem `There is not enough space on the disk`falhar com o erro . Neste caso, a slocalização é executar sqlpackage.exe em uma máquina com espaço suficiente para discos locais. Encorajamos a utilização do SqlPackage para importar/exportar bases de dados superiores a 150GB para evitar este problema.

1. Para importar de um ficheiro BACPAC para uma nova base de dados única utilizando o portal Azure, abra a página de servidor de base de dados apropriada e, em seguida, na barra de ferramentas, selecione base de **dados de importação**.  

   ![Importação de bases de dados1](./media/sql-database-import/sql-server-import-database.png)

1. Selecione a conta de armazenamento e o recipiente para o ficheiro BACPAC e, em seguida, selecione o ficheiro BACPAC a partir do qual importar.

1. Especifique o novo tamanho da base de dados (geralmente o mesmo que a origem) e forneça as credenciais do Servidor SQL de destino. Para obter uma lista de valores possíveis para uma nova base de dados Azure SQL, consulte [Create Database](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).

   ![Importação de bases de dados2](./media/sql-database-import/sql-server-import-database-settings.png)

1. Clique em **OK**.

1. Para monitorizar o progresso de uma importação, abra a página do servidor da base de dados e, em **Definições,** selecione o histórico de **importação/exportação**. Quando bem sucedida, a importação tem um estatuto **completo.**

   ![Estado de importação de bases de dados](./media/sql-database-import/sql-server-import-database-history.png)

1. Para verificar se a base de dados está ao vivo no servidor de base de dados, selecione bases de **dados SQL** e verifique se a nova base de dados está **Online**.

## <a name="using-sqlpackage"></a>Usando o SqlPackage

Para importar uma base de dados do SQL Server utilizando o utilitário da linha de comando [SqlPackage,](https://docs.microsoft.com/sql/tools/sqlpackage) consulte [os parâmetros e propriedades de importação](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). O SqlPackage tem o mais recente Estúdio de Gestão de [Servidores SQL](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e [ferramentas de dados do Servidor SQL para estúdio visual](https://msdn.microsoft.com/library/mt204009.aspx). Também pode descarregar o mais recente [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) do centro de descarregamento da Microsoft.

Para escala e desempenho, recomendamos a utilização do SqlPackage na maioria dos ambientes de produção em vez de utilizar o portal Azure. Para um blog da Equipa de Aconselhamento ao `BACPAC` Cliente do SQL Server sobre a migração usando ficheiros, consulte [a migração do SQL Server para a Base de Dados SQL azure utilizando ficheiros BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

O seguinte comando SqlPackage importa a base de dados **AdventureWorks2008R2** do armazenamento local para um servidor de base de dados Azure SQL chamado **mynewserver20170403**. Cria uma nova base de dados chamada **myMigratedDatabase** com um nível de serviço **Premium** e um Objetivo de Serviço **P6.** Altere estes valores conforme apropriado para o seu ambiente.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Para se ligar a um servidor de base de dados SQL que gere uma única base de dados por detrás de uma firewall corporativa, a firewall deve ter a porta 1433 aberta. Para se ligar a uma instância gerida, deve ter uma [ligação ponto-a-local](sql-database-managed-instance-configure-p2s.md) ou uma ligação de rota expressa.

Este exemplo mostra como importar uma base de dados usando o SqlPackage com autenticação universal de diretório ativo.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>Com o PowerShell

> [!NOTE]
> [Uma instância gerida](sql-database-managed-instance.md) não suporta atualmente a migração de uma base de dados para uma base de dados de instâncias a partir de um ficheiro BACPAC utilizando o Azure PowerShell. Para importar para uma instância gerida, utilize o SQL Server Management Studio ou o SQLPackage.

> [!NOTE]
> As máquinas que processam pedidos de importação/exportação apresentados através do portal ou da Powershell precisam de armazenar o ficheiro bacpac, bem como ficheiros temporários gerados pelo Quadro de Aplicação de Nível de Dados (DacFX). O espaço do disco necessário varia significativamente entre DBs com o mesmo tamanho e pode demorar até 3 vezes o tamanho da base de dados. As máquinas que executam o pedido de importação/exportação têm apenas 450GB de espaço de disco local. Como resultado, alguns pedidos podem falhar com o erro "Não há espaço suficiente no disco". Neste caso, a slocalização é executar sqlpackage.exe em uma máquina com espaço suficiente para discos locais. Ao importar/exportar bases de dados superiores a 150GB, utilize o SqlPackage para evitar este problema.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager (RM) ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. O módulo AzureRM continuará a receber correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para mais informações sobre a sua compatibilidade, consulte [A introdução do novo módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

Utilize o [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) cmdlet para submeter um pedido de base de dados de importação ao serviço de base de dados Azure SQL. Dependendo do tamanho da base de dados, a importação pode demorar algum tempo a ser concluída.

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

Pode utilizar o [CmDLet Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) para verificar o progresso da importação. Executar o cmdlet imediatamente após `Status: InProgress`a devolução do pedido. A importação fica completa `Status: Succeeded`quando se vê.

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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Utilize o comando [az-sql-db-import](/cli/azure/sql/db#az-sql-db-import) para submeter um pedido de base de dados de importação ao serviço de base de dados Azure SQL. Dependendo do tamanho da base de dados, a importação pode demorar algum tempo a ser concluída.

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
> Para outro exemplo de guião, consulte [importar uma base de dados de um ficheiro BACPAC](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="limitations"></a>Limitações

Importar para uma base de dados em piscina elástica não é suportado. Pode importar dados numa única base de dados e, em seguida, mover a base de dados para um pool elástico.

## <a name="import-using-wizards"></a>Importar usando feiticeiros

Também pode usar estes assistentes.

- [Importar assistente de aplicação de nível de dados no Estúdio de Gestão de Servidores SQL](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- Assistente de [Importação e Exportação do Servidor SQL.](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard)

## <a name="next-steps"></a>Passos seguintes

- Para aprender a ligar e consultar uma base de dados SQL importada, consulte [Quickstart: Azure SQL Database: Use SQL Server Management Studio para ligar e consultar dados](sql-database-connect-query-ssms.md).
- Para saber mais sobre a migração com ficheiros BACPAC num blogue da Equipa de Aconselhamento ao Cliente do SQL Server, consulte [Migrating from SQL Server to Azure SQL Database using BACPAC Files (Migrar a partir do SQL Server para a Base de Dados SQL do Azure com Ficheiros BACPAC)](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- Para uma discussão de todo o processo de migração da base de dados SQL Server, incluindo recomendações de desempenho, consulte a migração da base de [dados Do Servidor SQL para a Base de Dados Azure SQL](sql-database-single-database-migrate.md).
- Para saber gerir e partilhar chaves de armazenamento e assinaturas de acesso partilhadas de forma segura, consulte o Guia de Segurança do [Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).

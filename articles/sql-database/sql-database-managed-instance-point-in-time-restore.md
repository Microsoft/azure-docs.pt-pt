---
title: Instância gerenciada – restauração pontual
description: Restaurar um banco de dados SQL em uma instância gerenciada para um ponto anterior no tempo.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: b106b1da5d012309e8d92c8e9555ee3982602e12
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707653"
---
# <a name="restore-a-sql-database-in-a-managed-instance-to-a-previous-point-in-time"></a>Restaurar um banco de dados SQL em uma instância gerenciada para um ponto anterior no tempo

Use a restauração pontual (PITR) para criar um banco de dados como uma cópia de outro banco de dados de algum tempo no passado. Este artigo descreve como fazer uma restauração pontual de um banco de dados em uma instância gerenciada do banco de dados SQL do Azure.

A restauração pontual é útil em cenários de recuperação, como incidentes causados por erros, dados carregados incorretamente ou exclusão de dados cruciais. Você também pode usá-lo simplesmente para teste ou auditoria. Os arquivos de backup são mantidos por 7 a 35 dias, dependendo das configurações do banco de dados.

A restauração pontual pode:

- Restaurar um banco de dados de um banco de dados existente.
- Restaurar um banco de dados de um banco de dados excluído.

Para uma instância gerenciada, a restauração pontual também pode:

- Restaure um banco de dados para a mesma instância gerenciada.
- Restaurar um banco de dados para outra instância gerenciada.

> [!NOTE]
> A restauração pontual de uma instância gerenciada inteira não é possível. Este artigo explica apenas o que é possível: restauração pontual de um banco de dados hospedado em uma instância gerenciada.

## <a name="limitations"></a>Limitações

Quando você estiver restaurando de uma instância gerenciada para outra, ambas as instâncias deverão estar na mesma assinatura e região. A restauração entre regiões e entre assinaturas não tem suporte no momento.

> [!WARNING]
> Esteja ciente do tamanho do armazenamento da instância gerenciada. Dependendo do tamanho dos dados a serem restaurados, você poderá ficar sem armazenamento de instância. Se não houver espaço suficiente para os dados restaurados, use uma abordagem diferente.

A tabela a seguir mostra cenários de restauração pontual para instâncias gerenciadas:

|           |Restaurar o banco de BD existente para a mesma instância gerenciada| Restaurar o banco de BD existente para outra instância gerenciada|Restaurar o DB removido para a mesma instância gerenciada|Restaurar o BD removido para outra instância gerenciada|
|:----------|:----------|:----------|:----------|:----------|
|**Portal do Azure**| Sim|Não |Não|Não|
|**CLI do Azure**|Sim |Sim |Não|Não|
|**PowerShell**| Sim|Sim |Sim|Sim|

## <a name="restore-an-existing-database"></a>Restaurar um banco de dados existente

Restaure um banco de dados existente para a mesma instância usando o portal do Azure, o PowerShell ou o CLI do Azure. Para restaurar um banco de dados para outra instância, use o PowerShell ou o CLI do Azure para que você possa especificar as propriedades para a instância gerenciada de destino e o grupo de recursos. Se você não especificar esses parâmetros, o banco de dados será restaurado para a instância existente por padrão. Atualmente, o portal do Azure não dá suporte à restauração para outra instância.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. Vá para a instância gerenciada e selecione o banco de dados que você deseja restaurar.
3. Selecione **restaurar** na página do banco de dados:

    ![Restaurar um banco de dados usando o portal do Azure](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

4. Na página **restaurar** , selecione o ponto para a data e a hora em que você deseja restaurar o banco de dados.
5. Selecione **confirmar** para restaurar o banco de dados. Essa ação inicia o processo de restauração, que cria um novo banco de dados e popula-o com o banco de dados original no momento especificado. Para obter mais informações sobre o processo de recuperação, consulte [tempo de recuperação](sql-database-recovery-using-backups.md#recovery-time).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Se você ainda não tiver Azure PowerShell instalado, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Para restaurar o banco de dados usando o PowerShell, especifique os valores para os parâmetros no comando a seguir. Em seguida, execute o comando:

```powershell-interactive
$subscriptionId = "<Subscription ID>"
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source-database>"
$pointInTime = "2018-06-27T08:51:39.3882806Z"
$targetDatabase = "<Name of new database to be created>"

Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
```

Para restaurar o banco de dados para outra instância gerenciada, especifique também os nomes do grupo de recursos de destino e da instância gerenciada:  

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
                              -TargetResourceGroupName $targetResourceGroupName `
                              -TargetInstanceName $targetInstanceName 
```

Para obter detalhes, consulte [Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase).

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Se você ainda não tiver o CLI do Azure instalado, consulte [instalar o CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

Para restaurar o banco de dados usando o CLI do Azure, especifique os valores para os parâmetros no comando a seguir. Em seguida, execute o comando:

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

Para restaurar o banco de dados para outra instância gerenciada, especifique também os nomes do grupo de recursos de destino e da instância gerenciada:  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Para obter uma explicação detalhada dos parâmetros disponíveis, consulte a [documentação da CLI para restaurar um banco de dados em uma instância gerenciada](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore).

---

## <a name="restore-a-deleted-database"></a>Restaurar uma base de dados eliminada

A restauração de um banco de dados excluído só pode ser feita usando o PowerShell. O banco de dados pode ser restaurado para a mesma instância ou outra instância.

Para restaurar um banco de dados excluído usando o PowerShell, especifique os valores para os parâmetros no comando a seguir. Em seguida, execute o comando:

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$deletedDatabaseName = "<Source database name>"

$deleted_db = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
            -InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName 

$pointInTime = "2018-06-27T08:51:39.3882806Z"
$properties = New-Object System.Object
$properties | Add-Member -type NoteProperty -name CreateMode -Value "PointInTimeRestore"
$properties | Add-Member -type NoteProperty -name RestorePointInTime -Value $pointInTime
$properties | Add-Member -type NoteProperty -name RestorableDroppedDatabaseId -Value $deleted_db.Id
```

Para restaurar o banco de dados excluído para outra instância, altere os nomes do grupo de recursos e da instância gerenciada. Além disso, verifique se o parâmetro Location corresponde ao local do grupo de recursos e à instância gerenciada.

```powershell-interactive
$resourceGroupName = "<Second resource group name>"
$managedInstanceName = "<Second managed instance name>"

$location = "West Europe"

$restoredDBName = "WorldWideImportersPITR"
$resource_id = "subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$managedInstanceName/databases/$restoredDBName"

New-AzResource -Location $location -Properties $properties `
        -ResourceId $resource_id -ApiVersion "2017-03-01-preview" -Force
```

## <a name="overwrite-an-existing-database"></a>Substituir um banco de dados existente

Para substituir um banco de dados existente, você deve:

1. Remova o banco de dados existente que você deseja substituir.
2. Renomeie o banco de dados restaurado point-in-time para o nome do banco de dados que você removeu.

### <a name="drop-the-original-database"></a>Remover o banco de dados original

Você pode remover o banco de dados usando o portal do Azure, o PowerShell ou o CLI do Azure.

Você também pode remover o banco de dados conectando-se diretamente à instância gerenciada, iniciando SQL Server Management Studio (SSMS) e executando o seguinte comando Transact-SQL (T-SQL):

```sql
DROP DATABASE WorldWideImporters;
```

Use um dos seguintes métodos para se conectar ao banco de dados na instância gerenciada:

- [SSMS/Azure Data Studio por meio de uma máquina virtual do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Ponto a site](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Ponto de extremidade público](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

No portal do Azure, selecione o banco de dados da instância gerenciada e, em seguida, selecione **excluir**.

   ![Excluir um banco de dados usando o portal do Azure](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Use o seguinte comando do PowerShell para remover um banco de dados existente de uma instância gerenciada:

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Use o seguinte comando CLI do Azure para remover um banco de dados existente de uma instância gerenciada:

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Alterar o novo nome do banco de dados para corresponder ao nome original do banco de dados

Conecte-se diretamente à instância gerenciada e inicie SQL Server Management Studio. Em seguida, execute a seguinte consulta Transact-SQL (T-SQL). A consulta irá alterar o nome do banco de dados restaurado para o do banco de dados removido que você pretende substituir.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

Use um dos seguintes métodos para se conectar ao banco de dados na instância gerenciada:

- [Máquina virtual do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Ponto a site](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Ponto de extremidade público](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [backups automatizados](sql-database-automated-backups.md).

---
title: Instância gerenciada do banco de dados SQL – restauração pontual | Microsoft Docs
description: Como restaurar um banco de dados em uma instância gerenciada do SQL para um ponto anterior no tempo.
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
ms.openlocfilehash: 67f13d16dcf6bbe4fa13fe3a6e78d3e4d61e1999
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70862138"
---
# <a name="restore-a-sql-managed-instance-database-to-a-previous-point-in-time"></a>Restaurar um banco de dados de instância gerenciada do SQL para um ponto anterior no tempo

A restauração pontual (PITR) permite que você crie um banco de dados como uma cópia de outro banco de dados em algum momento no passado. Este artigo descreve como executar uma restauração pontual de um banco de dados em uma instância gerenciada.

A restauração pontual pode ser usada em cenários de recuperação, como incidentes causados por erros, dados carregados incorretamente, exclusão de dados cruciais e outros problemas, bem como simplesmente para fins de teste ou auditoria. Dependendo das configurações do banco de dados, os arquivos de backup são mantidos por um período entre 7 e 35 dias.

A restauração pontual pode ser usada para:

- Restaurar um banco de dados de um banco de dados existente.
- Restaurar um banco de dados de um banco de dados excluído.

Além disso, com uma instância gerenciada, a restauração pontual pode ser usada para: 

- Restaure um banco de dados para a mesma instância gerenciada.
- Restaurar um banco de dados para outra instância gerenciada.


> [!NOTE]
> A restauração pontual de uma instância gerenciada inteira não é possível. O que é possível e explicado neste artigo é a restauração pontual de um banco de dados hospedado em uma instância gerenciada.


## <a name="limitations"></a>Limitações

Ao restaurar para outra instância gerenciada, ambas as instâncias devem estar na mesma assinatura e região. Não há suporte atualmente para restaurações entre regiões e de assinatura cruzada.

> [!WARNING]
> Tenha cuidado com o tamanho de armazenamento da sua instância gerenciada – dependendo do tamanho da restauração de dados, você pode ficar sem armazenamento de instância. Se não houver espaço suficiente para os dados restaurados, use uma abordagem alternativa.

A tabela a seguir mostra cenários de recuperação pontual para a instância gerenciada:

|           |Restaurar banco de BD existente| Restaurar banco de BD existente|Restaurar banco de BD removido| Restaurar banco de BD removido|
|:----------|:----------|:----------|:----------|:----------|
|Destino| Mesmo MI|Outra MI |Mesmo MI|Outra MI |
|Portal do Azure| Sim|Não |Não|Não|
|CLI do Azure|Sim |Sim |Não|Não|
|PowerShell| Sim|Sim |Sim|Sim|


## <a name="restore-existing-database"></a>Restaurar banco de dados existente

Restaure um banco de dados existente para a mesma instância usando o portal do Azure, o PowerShell ou o CLI do Azure. Restaure um banco de dados para outra instância usando o PowerShell ou CLI do Azure especificando a instância gerenciada de destino e as propriedades do grupo de recursos. Se esses parâmetros não forem especificados, por padrão, o banco de dados será restaurado para a instância existente. A restauração para outra instância não tem suporte no momento por meio do portal do Azure. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 
1. Navegue até a instância gerenciada e selecione o banco de dados que deseja restaurar. 
1. Selecione **restaurar** na página do banco de dados. 

    ![Restaurar banco de dados existente](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

1. Na página **restaurar** , selecione o ponto para a data e a hora no histórico no qual você deseja restaurar o banco de dados.
1. Selecione **confirmar** para restaurar o banco de dados. Isso inicia o processo de restauração, que cria um novo banco de dados e é populado com a data do banco de dados original no momento desejado. Para obter mais informações sobre o processo de recuperação, consulte [tempo de recuperação](sql-database-recovery-using-backups.md#recovery-time). 

1. Localizar instância gerenciada
1. Selecione o banco de dados que você deseja restaurar
1. Na tela banco de dados, clique em restaurar ação
1. Na tela restaurar, selecione a data e hora do ponto no histórico para o qual você está restaurando o banco de dados
1. Após a confirmação, o processo de restauração será iniciado e, dependendo do tamanho do banco de dados, o novo banco de dados será criado e preenchido com a data do banco de dado original no momento desejado. Para duração do processo de restauração, verifique a recuperação usando o artigo backups.


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Se você ainda não tiver Azure PowerShell instalado, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Para restaurar o banco de dados usando o PowerShell, atualize os parâmetros com seus valores e execute o seguinte comando:

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

Para restaurar o banco de dados para outra instância gerenciada, defina o nome do grupo de recursos de destino e o nome da instância gerenciada de destino.  

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

Para restaurar o banco de dados usando o CLI do Azure, atualize os parâmetros com seus valores e execute o seguinte comando:


```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```


Para restaurar o banco de dados para outra instância gerenciada, defina o nome do grupo de recursos de destino e o nome da instância gerenciada de destino.  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Para obter uma explicação detalhada dos parâmetros disponíveis, consulte [CLI de instância gerenciada](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore). 

---

## <a name="restore-a-deleted-database"></a>Restaurar uma base de dados eliminada 
 
A restauração de um banco de dados excluído só pode ser feita com o PowerShell. O banco de dados pode ser restaurado para a mesma instância ou outra instância. 

Para restaurar um banco de dados excluído usando o PowerShell, atualize os parâmetros com seus valores e execute o seguinte comando:

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

Para restaurar o banco de dados excluído para outra instância, altere o nome do grupo de recursos e o nome da instância gerenciada.

O parâmetro Location deve corresponder ao local do grupo de recursos e à instância gerenciada.

```powershell-interactive
$resourceGroupName = "<Second resource group name>"
$managedInstanceName = "<Second managed instance name>"

$location = "West Europe"

$restoredDBName = "WorldWideImportersPITR"
$resource_id = "subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$managedInstanceName/databases/$restoredDBName"

New-AzResource -Location $location -Properties $properties `
        -ResourceId $resource_id -ApiVersion "2017-03-01-preview" -Force
```

## <a name="overwrite-existing-database"></a>Substituir banco de dados existente 
 
Para substituir um banco de dados existente, você também deve:

1. Remova o banco de dados existente que você deseja substituir.
1. Renomeie o banco de dados restaurado pontual para o nome do banco de dados que foi Descartado. 


### <a name="drop-original-database"></a>REMOVER banco de dados original 
 
O descarte do banco de dados pode ser feito com o portal do Azure, o PowerShell ou o CLI do Azure. 

Você também pode remover o banco de dados conectando-se à instância gerenciada diretamente, iniciando SQL Server Management Studio (SSMS) e executando o comando Transact-SQL (T-SQL) abaixo.

```sql
DROP DATABASE WorldWideImporters;
```

Use um dos seguintes métodos para se conectar ao banco de dados de instância gerenciada: 

- [Máquina virtual do SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Ponto a site](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Ponto final público](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

No portal do Azure, selecione o banco de dados da instância gerenciada e selecione **excluir**.

   ![Restaurar banco de dados existente](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

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


### <a name="alter-new-database-name-to-original"></a>ALTERAR novo nome do banco de dados para original

Conecte-se diretamente à instância gerenciada, inicie o SQL Server Management Studio e execute a seguinte consulta Transact-SQL (T-SQL) para alterar o nome do banco de dados restaurado para o do banco de dados removido que você pretende substituir. 


```sql
ALTER WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```


Use um dos seguintes métodos para se conectar ao banco de dados de instância gerenciada: 

- [Máquina virtual do SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Ponto a site](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Ponto final público](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [retenção de longo prazo](sql-database-long-term-retention.md) e [backups automatizados](sql-database-automated-backups.md). 

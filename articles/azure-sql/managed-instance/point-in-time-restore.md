---
title: Restauro pontual (PITR)
titleSuffix: Azure SQL Managed Instance
description: Restaurar uma base de dados em Azure SQL Gestd instance para um ponto anterior no tempo.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: 4c116b378c72d87641157fc453d65e46be9f43ec
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787172"
---
# <a name="restore-a-database-in-azure-sql-managed-instance-to-a-previous-point-in-time"></a>Restaurar uma base de dados em Azure SQL Gestd Instance para um ponto anterior no tempo
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Utilize a restauração pontual (PITR) para criar uma base de dados como cópia de outra base de dados de algum tempo no passado. Este artigo descreve como fazer uma restauração pontual de uma base de dados em Azure SQL Managed Instance.

A restauração pontual é útil em cenários de recuperação, tais como incidentes causados por erros, dados carregados incorretamente ou eliminação de dados cruciais. Também pode usá-lo simplesmente para testar ou auditar. Os ficheiros de cópia de segurança são mantidos durante 7 a 35 dias, dependendo das definições da base de dados.

A restauração pontual pode restaurar uma base de dados:

- a partir de uma base de dados existente.
- a partir de uma base de dados eliminada.
- para a mesma SQL Managed Instance, ou para outra SQL Managed Instance. 

## <a name="limitations"></a>Limitações

A restauração pontual da SQL Managed Instance tem as seguintes limitações:

- Quando você está restaurando de um caso de SQL Managed Instance para outro, ambos os casos devem estar na mesma subscrição e região. A restauração de cross-region e cross-subscrição não é suportada atualmente.
- Não é possível restaurar o ponto a tempo de toda uma sql Gestd Instance. Este artigo explica apenas o que é possível: restauro pontual de uma base de dados que está hospedada em SQL Managed Instance.

> [!WARNING]
> Esteja ciente do tamanho de armazenamento da sua SQL Managed Instance. Dependendo do tamanho dos dados a restaurar, poderá ficar sem armazenamento de exemplo. Se não houver espaço suficiente para os dados restaurados, use uma abordagem diferente.

A tabela a seguir mostra cenários de restauro pontual para a ocorrência gerida do SQL:

|           |Restaurar a DB existente no mesmo caso de SQL Managed Instance| Restaurar o DB existente para outra sql gestditua instância|Restauro caiu DB para a mesma SQL Managed Instance|Restauro deixou cair DB para outra SqL Managed Instance|
|:----------|:----------|:----------|:----------|:----------|
|**Portal do Azure**| Yes|No |Yes|No|
|**CLI do Azure**|Yes |Yes |No|No|
|**PowerShell**| Yes|Yes |Yes|Yes|

## <a name="restore-an-existing-database"></a>Restaurar uma base de dados existente

Restaurar uma base de dados existente na mesma SQL Managed Instance utilizando o portal Azure, PowerShell ou o Azure CLI. Para restaurar uma base de dados para outra SqL Managed Instance, utilize o PowerShell ou o Azure CLI para que possa especificar as propriedades para o target SQL Managed Instance e grupo de recursos. Se não especificar estes parâmetros, a base de dados será restaurada para a padrão da 22AL Existente. O portal Azure não suporta atualmente restaurar para outra SQL Managed Instance.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. Vá ao seu SQL Managed Instance e selecione a base de dados que pretende restaurar.
3. Selecione **Restaurar** na página da base de dados:

    ![Restaurar uma base de dados utilizando o portal Azure](./media/point-in-time-restore/restore-database-to-mi.png)

4. Na página **'Restaurar',** selecione o ponto para a data e hora a que pretende restaurar a base de dados.
5. **Selecione Confirmar** para restaurar a sua base de dados. Esta ação inicia o processo de restauro, que cria uma nova base de dados e povoa-a com dados da base de dados original no ponto especificado no tempo. Para obter mais informações sobre o processo de recuperação, consulte o [tempo de recuperação.](../database/recovery-using-backups.md#recovery-time)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Se ainda não tiver o Azure PowerShell instalado, consulte [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps).

Para restaurar a base de dados utilizando o PowerShell, especifique os seus valores para os parâmetros no comando seguinte. Então, executar o comando:

```powershell-interactive
$subscriptionId = "<Subscription ID>"
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
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

Para restaurar a base de dados para outra sql Managed Instance, especificar também os nomes do grupo de recursos-alvo e o alvo SQL Managed Instance:  

```powershell-interactive
$targetResourceGroupName = "<Resource group of target SQL Managed Instance>"
$targetInstanceName = "<Target SQL Managed Instance name>"

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
                              -TargetResourceGroupName $targetResourceGroupName `
                              -TargetInstanceName $targetInstanceName 
```

Para mais informações, consulte [a Base de Dados Restore-AzSqlInstance](/powershell/module/az.sql/restore-azsqlinstancedatabase).

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Se ainda não tiver o Azure CLI instalado, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

Para restaurar a base de dados utilizando o CLI Azure, especifique os seus valores para os parâmetros no comando seguinte. Então, executar o comando:

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

Para restaurar a base de dados para outra sql Gestd Instance, especificar também os nomes do grupo de recursos-alvo e da SQL Managed Instance:  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Para obter uma explicação detalhada dos parâmetros disponíveis, consulte a documentação do [CLI para restaurar uma base de dados numa 22.ºC.](/cli/azure/sql/midb#az_sql_midb_restore)

---

## <a name="restore-a-deleted-database"></a>Restaurar uma base de dados eliminada

Restaurar uma base de dados eliminada pode ser feito utilizando o portal PowerShell ou Azure. Para restaurar uma base de dados eliminada na mesma instância, utilize o portal Azure ou o PowerShell. Para restaurar uma base de dados eliminada para outro caso, utilize o PowerShell. 

### <a name="portal"></a>Portal 


Para recuperar uma base de dados gerida utilizando o portal Azure, abra a página de visão geral do SQL Managed Instance e selecione **bases de dados eliminadas**. Escolha uma base de dados eliminada que pretenda restaurar e digite o nome para a nova base de dados que será criada com dados restaurados a partir da cópia de segurança.

  ![Screenshot da restauração eliminada base de dados de instâncias Azure SQL](./media/point-in-time-restore/restore-deleted-sql-managed-instance-annotated.png)

.. /.. /sql-base de dados

### <a name="powershell"></a>PowerShell

Para restaurar uma base de dados na mesma instância, atualize os valores dos parâmetros e, em seguida, execute o seguinte comando PowerShell: 

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$deletedDatabaseName = "<Source database name>"
$targetDatabaseName = "<target database name>"

$deletedDatabase = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
-InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName

Restore-AzSqlinstanceDatabase -FromPointInTimeBackup -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName
```

Para restaurar a base de dados para outra sql Managed Instance, especificar também os nomes do grupo de recursos-alvo e o alvo SQL Managed Instance:

```powershell-interactive
$targetResourceGroupName = "<Resource group of target SQL Managed Instance>"
$targetInstanceName = "<Target SQL Managed Instance name>"

Restore-AzSqlinstanceDatabase -FromPointInTimeBackup -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName `
   -TargetResourceGroupName $targetResourceGroupName `
   -TargetInstanceName $targetInstanceName 
```

## <a name="overwrite-an-existing-database"></a>Sobrepor uma base de dados existente

Para substituir uma base de dados existente, deve:

1. Largue a base de dados existente que pretende substituir.
2. Mude o nome da base de dados restaurada no tempo para o nome da base de dados que deixou cair.

### <a name="drop-the-original-database"></a>Deixe cair a base de dados original

Pode deixar cair a base de dados utilizando o portal Azure, PowerShell ou o Azure CLI.

Também pode deixar cair a base de dados ligando-se diretamente ao SQL Managed Instance, iniciando o SQL Server Management Studio (SSMS) e executando o seguinte comando Transact-SQL (T-SQL):

```sql
DROP DATABASE WorldWideImporters;
```

Utilize um dos seguintes métodos para ligar à sua base de dados na SqL Managed Instance:

- [Estúdio de Dados SSMS/Azure através de uma máquina virtual Azure](./connect-vm-instance-configure.md)
- [Ponto a site](./point-to-site-p2s-configure.md)
- [Ponto final público](./public-endpoint-configure.md)

# <a name="portal"></a>[Portal](#tab/azure-portal)

No portal Azure, selecione a base de dados a partir da SqL Managed Instance e, em seguida, **selecione Delete**.

   ![Eliminar uma base de dados utilizando o portal Azure](./media/point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Utilize o seguinte comando PowerShell para lançar uma base de dados existente a partir de uma sql Gestd Instance:

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Utilize o seguinte comando Azure CLI para lançar uma base de dados existente a partir de uma sql Managed Instance:

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Altere o novo nome da base de dados para corresponder ao nome original da base de dados

Ligue-se diretamente ao SQL Managed Instance e inicie o SQL Server Management Studio. Em seguida, executar a seguinte consulta Transact-SQL (T-SQL). A consulta alterará o nome da base de dados restaurada para a base de dados que pretende substituir.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

Utilize um dos seguintes métodos para ligar à sua base de dados em SQL Managed Instance:

- [Máquina virtual Azure](./connect-vm-instance-configure.md)
- [Ponto a site](./point-to-site-p2s-configure.md)
- [Ponto final público](./public-endpoint-configure.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais [sobre cópias de segurança automatizadas.](../database/automated-backups-overview.md)

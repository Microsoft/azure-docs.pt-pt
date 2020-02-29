---
title: Instância gerida - Restauro ponto-a-tempo (PITR)
description: Restaurar uma base de dados SQL num caso gerido até um ponto anterior no tempo.
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
ms.openlocfilehash: 27f465e6864d0ff639e825c8a816d86648bd8853
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197526"
---
# <a name="restore-a-sql-database-in-a-managed-instance-to-a-previous-point-in-time"></a>Restaurar uma base de dados SQL num caso gerido até um ponto anterior no tempo

Utilize a restauração ponto-a-hora (PITR) para criar uma base de dados como cópia de outra base de dados de algum tempo no passado. Este artigo descreve como fazer uma restauração pontual de uma base de dados numa base de dados Azure SQL gerida.

A restauração pontual é útil em cenários de recuperação, tais como incidentes causados por erros, dados carregados incorretamente ou eliminação de dados cruciais. Também pode utilizá-lo apenas para testes ou auditorias. Os ficheiros de backup são mantidos durante 7 a 35 dias, dependendo das definições da sua base de dados.

A restauração pontual pode restaurar uma base de dados:

- de uma base de dados existente.
- de uma base de dados apagada.
- para a mesma instância gerida, ou para outra instância gerida. 

## <a name="limitations"></a>Limitações

O tempo de restabelecimento de um caso gerido tem as seguintes limitações:

- Quando se está a restaurar de um caso gerido para outro, ambos os casos devem estar na mesma subscrição e região. A tualmente, a restauração transversal e a subscrição cruzada não são suportadas.
- A restauração pontual de toda uma instância gerida não é possível. Este artigo explica apenas o que é possível: restauro pontual de uma base de dados que está alojada numa instância gerida.

> [!WARNING]
> Esteja ciente do tamanho do armazenamento da sua instância gerida. Dependendo do tamanho dos dados a restaurar, pode ficar sem armazenamento de instâncias. Se não houver espaço suficiente para os dados restaurados, use uma abordagem diferente.

O quadro seguinte mostra cenários de restauro pontual para casos geridos:

|           |Restaurar o DB existente para a mesma instância gerida| Restaurar o DB existente para outra instância gerida|Restaurar caiu DB para a mesma instância gerida|Restaurar caiu DB para outra instância gerida|
|:----------|:----------|:----------|:----------|:----------|
|**Portal do Azure**| Sim|Não |Sim|Não|
|**CLI do Azure**|Sim |Sim |Não|Não|
|**PowerShell**| Sim|Sim |Sim|Sim|

## <a name="restore-an-existing-database"></a>Restaurar uma base de dados existente

Restaurar uma base de dados existente na mesma instância utilizando o portal Azure, PowerShell ou o Azure CLI. Para restaurar uma base de dados para outra instância, utilize o PowerShell ou o Azure CLI para que possa especificar as propriedades para a instância gerida pelo alvo e pelo grupo de recursos. Se não especificar estes parâmetros, a base de dados será restaurada à instância existente por padrão. O portal Azure não suporta restaurar para outro caso.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. Vá à sua instância de gestão e selecione a base de dados que pretende restaurar.
3. Selecione **Restaurar** na página de base de dados:

    ![Restaurar uma base de dados utilizando o portal Azure](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

4. Na página **Restaurar,** selecione o ponto para a data e hora a que pretende restaurar a base de dados.
5. Selecione **Confirmar** para restaurar a sua base de dados. Esta ação inicia o processo de restauro, que cria uma nova base de dados e o povoa com dados da base de dados original no ponto de tempo especificado. Para mais informações sobre o processo de recuperação, consulte o [tempo de recuperação.](sql-database-recovery-using-backups.md#recovery-time)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Se ainda não tiver o Azure PowerShell instalado, consulte [Instalar o módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps).

Para restaurar a base de dados utilizando o PowerShell, especifique os seus valores para os parâmetros no seguinte comando. Em seguida, executar o comando:

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

Para restaurar a base de dados em outra instância gerida, especifique também os nomes do grupo de recursos-alvo e da instância gerida pelo alvo:  

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

Para mais detalhes, consulte [Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase).

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Se ainda não tiver o Azure CLI instalado, consulte [Instalar o Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Para restaurar a base de dados utilizando o CLI Azure, especifique os seus valores para os parâmetros no seguinte comando. Em seguida, executar o comando:

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

Para restaurar a base de dados em outra instância gerida, especifique também os nomes do grupo de recursos-alvo e a instância gerida:  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Para obter uma explicação detalhada dos parâmetros disponíveis, consulte a [documentação do CLI para restaurar uma base de dados numa instância gerida](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore).

---

## <a name="restore-a-deleted-database"></a>Restaurar uma base de dados eliminada

Restaurar uma base de dados eliminada pode ser feito utilizando o portal PowerShell ou Azure. Para restaurar uma base de dados eliminada na mesma instância, utilize o portal Azure ou o PowerShell. Para restaurar uma base de dados eliminada para outra instância, utilize o PowerShell. 

### <a name="portal"></a>Portal 


Para recuperar uma base de dados gerida utilizando o portal Azure, abra a página de visão geral da instância gerida e selecione bases de **dados Eliminadas**. Escolha uma base de dados eliminada que pretenda restaurar e escreva o nome para a nova base de dados que será criada com os dados restaurados a partir da cópia de segurança.

  ![Screenshot da base de dados de instância sql do Restauro eliminada](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="powershell"></a>PowerShell

Para restaurar uma base de dados na mesma instância, atualize os valores do parâmetro e, em seguida, execute o seguinte comando PowerShell: 

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$deletedDatabaseName = "<Source database name>"
$targetDatabaseName = "<target database name>"

$deletedDatabase = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
-InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName
```

Para restaurar a base de dados em outra instância gerida, especifique também os nomes do grupo de recursos-alvo e da instância gerida pelo alvo:

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
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
2. Mude o nome da base de dados restaurada no ponto de tempo para o nome da base de dados que deixou cair.

### <a name="drop-the-original-database"></a>Largue a base de dados original

Pode deixar cair a base de dados utilizando o portal Azure, PowerShell ou o Azure CLI.

Também pode deixar cair a base de dados ligando-se diretamente à instância gerida, iniciando o Estúdio de Gestão de Servidores SQL (SSMS) e executando o seguinte comando Transact-SQL (T-SQL):

```sql
DROP DATABASE WorldWideImporters;
```

Utilize um dos seguintes métodos para se ligar à sua base de dados na instância gerida:

- [Estúdio de Dados SSMS/Azure através de uma máquina virtual Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Ponto-a-local](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Ponto final público](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portal"></a>[Portal](#tab/azure-portal)

No portal Azure, selecione a base de dados a partir da instância gerida e, em seguida, selecione **Delete**.

   ![Eliminar uma base de dados utilizando o portal Azure](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Utilize o seguinte comando PowerShell para deixar cair uma base de dados existente a partir de uma instância gerida:

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Utilize o seguinte comando Azure CLI para retirar uma base de dados existente de uma instância gerida:

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Altere o novo nome da base de dados para combinar com o nome original da base de dados

Conecte-se diretamente à instância gerida e inicie o Estúdio de Gestão de Servidores SQL. Em seguida, executar a seguinte consulta Transact-SQL (T-SQL). A consulta irá alterar o nome da base de dados restaurada para a base de dados que pretende substituir.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

Utilize um dos seguintes métodos para se ligar à sua base de dados na instância gerida:

- [Máquina virtual azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Ponto-a-local](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Ponto final público](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [cópias de segurança automatizadas.](sql-database-automated-backups.md)

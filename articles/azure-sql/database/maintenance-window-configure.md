---
title: Janela de manutenção configurada (Pré-visualização)
description: Saiba como definir o momento em que a manutenção planeada deve ser realizada nas suas bases de dados Azure SQL, piscinas elásticas e bases de dados de instâncias geridas.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/23/2021
ms.openlocfilehash: 8688458d85084f3d3dab4678fa91ed827a337739
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047356"
---
# <a name="configure-maintenance-window-preview"></a>Janela de manutenção configurada (Pré-visualização)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Configure a janela de [manutenção (Pré-visualização)](maintenance-window.md) para uma base de dados Azure SQL, piscina elástica ou base de dados Azure SQL Gestd Instance durante a criação de recursos, ou a qualquer momento após a criação de um recurso. 

A janela de manutenção *predefinida do Sistema* é das 17:00 às 8:00 horas diárias (hora local da região de Azure o recurso está localizado) para evitar interrupções de horas de ponta. Se a janela de manutenção *predefinitiva do Sistema* não for a melhor altura, selecione uma das outras janelas de manutenção disponíveis.

A capacidade de mudar para uma janela de manutenção diferente não está disponível para todos os níveis de serviço ou em todas as regiões. Para obter mais informações sobre a disponibilidade, consulte [a disponibilidade da janela manutenção.](maintenance-window.md#availability)

> [!Important]
> A janela de manutenção configurante é uma operação assíncrona de longa duração, semelhante à alteração do nível de serviço do recurso Azure SQL. O recurso está disponível durante a operação, exceto uma reconfiguração curta que ocorre no final da operação e que normalmente dura até 8 segundos, mesmo em caso de transações interrompidas de longa duração. Para minimizar o impacto da reconfiguração, deverá efetuar a operação fora das horas de ponta.

## <a name="configure-maintenance-window-during-database-creation"></a>Configure janela de manutenção durante a criação de base de dados 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para configurar a janela de manutenção quando criar uma base de dados, piscina elástica ou instância gerida, defina a janela de **manutenção** desejada na página **de definições Adicionais.** 

## <a name="set-the-maintenance-window-while-creating-a-single-database-or-elastic-pool"></a>Deslome a janela de manutenção enquanto cria uma única base de dados ou piscina elástica

Para obter informações passo a passo sobre a criação de uma nova base de dados ou piscina, consulte [criar uma base de dados única Azure SQL Database](single-database-create-quickstart.md).

   :::image type="content" source="media/maintenance-window-configure/additional-settings.png" alt-text="Criar separador de definições adicionais de base de dados":::


## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Definir a janela de manutenção enquanto cria uma instância gerida

Para obter informações passo a passo sobre a criação de um novo caso gerido, consulte [Criar um Exemplo Gerido Azure SQL](../managed-instance/instance-create-quickstart.md).

   :::image type="content" source="media/maintenance-window-configure/additional-settings-mi.png" alt-text="Criar separador de definições adicionais de instância gerida":::




# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Os exemplos a seguir mostram como configurar a janela de manutenção utilizando a Azure PowerShell. Pode [instalar a Azure PowerShell,](/powershell/azure/install-az-ps)ou utilizar a Azure Cloud Shell.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode lançar cloud Shell num separador de navegador indo para [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.


## <a name="discover-available-maintenance-windows"></a>Descubra janelas de manutenção disponíveis

Ao definir a janela de manutenção, cada região tem as suas próprias opções de janela de manutenção que correspondem ao sol-tempo para a região onde se encontra a base de dados ou piscina. 

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>Descubra a Base de Dados SQL e janelas elásticas de manutenção da piscina 

O exemplo a seguir devolve as *janelas* de manutenção disponíveis para a região leste2 utilizando o [cmdlet Get-AzMaintenanceP publicconfiguration.](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) Para bases de dados e piscinas elásticas, definido `MaintenanceScope` para `SQLDB` .

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLDB"}
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>Descubra janelas de manutenção de instância gerida sql 

O exemplo a seguir devolve as *janelas* de manutenção disponíveis para a região leste2 utilizando o [cmdlet Get-AzMaintenanceP publicconfiguration.](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) Para casos geridos, definido `MaintenanceScope` para `SQLManagedInstance` .

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLManagedInstance"}
   ```


## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>Deslote a janela de manutenção enquanto cria uma única base de dados

O exemplo a seguir cria uma nova base de dados e define a janela de manutenção utilizando o cmdlet [New-AzSqlDatabase.](/powershell/module/az.sql/new-azsqldatabase) O `-MaintenanceConfigurationId` valor deve ser definido para um valor válido para a região da sua base de dados. Para obter valores válidos para a sua região, consulte [as janelas de manutenção disponíveis](#discover-available-maintenance-windows).


   ```powershell-interactive
    # Set variables for your database
    $resourceGroupName = "your_resource_group_name"
    $serverName = "your_server_name"
    $databaseName = "your_db_name"
    
    # Set selected maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_1"

    Write-host "Creating a gen5 2 vCore database with maintenance window ${maintenanceConfig} ..."
    $database = New-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -ComputeGeneration Gen5 `
      -VCore 2 `
      -MaintenanceConfigurationId $maintenanceConfig
    $database
   ```



## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>Coloque a janela de manutenção enquanto cria uma piscina elástica

O exemplo a seguir cria uma nova piscina elástica e define a janela de manutenção utilizando o cmdlet [New-AzSqlElasticPool.](/powershell/module/az.sql/new-azsqlelasticpool) A janela de manutenção está definida na piscina elástica, por isso todas as bases de dados da piscina têm o horário da janela de manutenção da piscina. O `-MaintenanceConfigurationId` deve ser definido para um valor válido para a região da sua piscina. Para obter valores válidos para a sua região, consulte [as janelas de manutenção disponíveis](#discover-available-maintenance-windows).


   ```powershell-interactive
    # Set variables for your pool
    $resourceGroupName = "your_resource_group_name"
    $serverName = "your_server_name"
    $poolName = "your_pool_name"
    
    # Set selected maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_2"

    Write-host "Creating a Standard 50 pool with maintenance window ${maintenanceConfig} ..."
    $pool = New-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName `
      -Edition "Standard" `
      -Dtu 50 `
      -DatabaseDtuMin 10 `
      -DatabaseDtuMax 20 `
      -MaintenanceConfigurationId $maintenanceConfig
    $pool
   ```

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Definir a janela de manutenção enquanto cria uma instância gerida

O exemplo a seguir cria uma nova instância gerida e define a janela de manutenção utilizando o cmdlet [New-AzSqlInstance.](/powershell/module/az.sql/new-azsqlinstance) A janela de manutenção está definida no caso, por isso todas as bases de dados no caso têm o horário da janela de manutenção do caso. Para `-MaintenanceConfigurationId` , o Bolo De *ManutençãoConfig Deve* ser um valor válido para a região do seu exemplo. Para obter valores válidos para a sua região, consulte [as janelas de manutenção disponíveis](#discover-available-maintenance-windows).


   ```powershell
   New-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -Location "your_mi_location" `
     -SubnetId /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

Os exemplos a seguir mostram como configurar a janela de manutenção utilizando o Azure CLI. Pode [instalar o Azure CLI,](/cli/azure/install-azure-cli)ou utilizar a Azure Cloud Shell. 

A configuração da janela de manutenção com o Azure CLI só está disponível para a SQL Managed Instance.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode lançar cloud Shell num separador de navegador indo para [https://shell.azure.com/cli](https://shell.azure.com/cli) . Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

## <a name="discover-available-maintenance-windows"></a>Descubra janelas de manutenção disponíveis

Ao definir a janela de manutenção, cada região tem as suas próprias opções de janela de manutenção que correspondem ao sol-tempo para a região onde se encontra a base de dados ou piscina.

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>Descubra a Base de Dados SQL e janelas elásticas de manutenção da piscina

O exemplo a seguir devolve as janelas de manutenção disponíveis para a região *leste22* utilizando o comando da [lista de configuração pública de manutenção az.](/cli/azure/ext/maintenance/maintenance/public-configuration#ext_maintenance_az_maintenance_public_configuration_list) Para bases de dados e piscinas elásticas, definido `maintenanceScope` para `SQLDB` .

   ```azurecli
   location="eastus2"

   az maintenance public-configuration list --query "[?location=='$location'&&contains(maintenanceScope,'SQLDB')]"
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>Descubra janelas de manutenção de instância gerida sql

O exemplo a seguir devolve as janelas de manutenção disponíveis para a região *leste22* utilizando o comando da [lista de configuração pública de manutenção az.](/cli/azure/ext/maintenance/maintenance/public-configuration#ext_maintenance_az_maintenance_public_configuration_list) Para casos geridos, definido `maintenanceScope` para `SQLManagedInstance` .

   ```azurecli
   az maintenance public-configuration list --query "[?location=='eastus2'&&contains(maintenanceScope,'SQLManagedInstance')]"
   ```

## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>Deslote a janela de manutenção enquanto cria uma única base de dados

O exemplo a seguir cria uma nova base de dados e define a janela de manutenção utilizando o comando [az sql db create.](/cli/azure/sql/db#az_sql_db_create) O `--maint-config-id` `-m` (ou) deve ser definido como um valor válido para a região da sua base de dados. Para obter valores válidos para a sua região, consulte [as janelas de manutenção disponíveis](#discover-available-maintenance-windows).


   ```azurecli
    # Set variables for your database
    resourceGroupName="your_resource_group_name"
    serverName="your_server_name"
    databaseName="your_db_name"

    # Set selected maintenance window
    maintenanceConfig="SQL_EastUS2_DB_1"

    # Create database
    az sql db create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2 \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>Coloque a janela de manutenção enquanto cria uma piscina elástica

O exemplo a seguir cria uma nova piscina elástica e define a janela de manutenção utilizando a [piscina elástica az sql criar](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create) cmdlet. A janela de manutenção está definida na piscina elástica, por isso todas as bases de dados da piscina têm o horário da janela de manutenção da piscina. O `--maint-config-id` `-m` (ou) deve ser definido como um valor válido para a região da sua piscina. Para obter valores válidos para a sua região, consulte [as janelas de manutenção disponíveis](#discover-available-maintenance-windows).


   ```azurecli
    # Set variables for your pool
    resourceGroupName="your_resource_group_name"
    serverName="your_server_name"
    poolName="your_pool_name"

    # Set selected maintenance window
    maintenanceConfig="SQL_EastUS2_DB_2"

    # Create elastic pool
    az sql elastic-pool create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2 \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Definir a janela de manutenção enquanto cria uma instância gerida

O exemplo a seguir cria uma nova instância gerida e define a janela de manutenção utilizando [az sql mi create](/cli/azure/sql/mi#az_sql_mi_create). A janela de manutenção está definida no caso, por isso todas as bases de dados no caso têm o horário da janela de manutenção do caso. *ManutençãoConfigName* deve ser um valor válido para a região do seu caso. Para obter valores válidos para a sua região, consulte [as janelas de manutenção disponíveis](#discover-available-maintenance-windows).

   ```azurecli
   az sql mi create -g mygroup -n myinstance -l mylocation -i -u myusername -p mypassword --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}
   ```

-----

## <a name="configure-maintenance-window-for-existing-databases"></a>Janela de manutenção de configuração para bases de dados existentes


Ao aplicar uma seleção de janelas de manutenção numa base de dados, pode ser experimentada uma breve reconfiguração (vários segundos) em alguns casos, uma vez que o Azure aplica as alterações necessárias.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Os seguintes passos definem a janela de manutenção numa base de dados existente, piscina elástica ou instância gerida utilizando o portal Azure:


## <a name="set-the-maintenance-window-for-an-existing-database-or-elastic-pool"></a>Deslote a janela de manutenção para uma base de dados ou piscina elástica existente

1. Navegue para a base de dados SQL ou piscina elástica para a seguinte atendo a janela de manutenção.
1. No menu **Definições** **selecione Manutenção** e, em seguida, selecione a janela de manutenção desejada.

   :::image type="content" source="media/maintenance-window-configure/maintenance.png" alt-text="Página de manutenção da base de dados SQL":::


## <a name="set-the-maintenance-window-for-an-existing-managed-instance"></a>Definir a janela de manutenção para uma instância gerida existente

1. Navegue para a instância gerida para a forma como pretende definir a janela de manutenção.
1. No menu **Definições** **selecione Manutenção** e, em seguida, selecione a janela de manutenção desejada.

   :::image type="content" source="media/maintenance-window-configure/maintenance-mi.png" alt-text="SQL geriu página de manutenção de caso":::



# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="set-the-maintenance-window-for-an-existing-database"></a>Deslote a janela de manutenção para uma base de dados existente

O exemplo a seguir define a janela de manutenção numa base de dados existente utilizando o [cmdlet Set-AzSqlDatabase.](/powershell/module/az.sql/set-azsqldatabase) O `-MaintenanceConfigurationId` valor deve ser definido para um valor válido para a região da sua base de dados. Para obter valores válidos para a sua região, consulte [as janelas de manutenção disponíveis](#discover-available-maintenance-windows).

   ```powershell-interactive
    # Select different maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_2"

    Write-host "Changing database maintenance window to ${maintenanceConfig} ..."
    $database = Set-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -MaintenanceConfigurationId $maintenanceConfig
    $database
   ```

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>Coloque a janela de manutenção numa piscina elástica existente

O exemplo a seguir define a janela de manutenção de uma piscina elástica existente utilizando o [cmdlet Set-AzSqlElasticPool.](/powershell/module/az.sql/set-azsqlelasticpool) É importante ter certeza de que o `$maintenanceConfig` valor é um valor válido para a região da sua piscina.  Para obter valores válidos para uma região, consulte [as janelas de manutenção disponíveis](#discover-available-maintenance-windows).

   ```powershell-interactive
    # Select different maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_1"
    
    Write-host "Changing pool maintenance window to ${maintenanceConfig} ..."
    $pool = Set-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName `
      -MaintenanceConfigurationId $maintenanceConfig
    $pool
   ```



## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>Coloque a janela de manutenção num caso gerido existente

O exemplo a seguir define a janela de manutenção num caso gerido existente utilizando o [cmdlet Set-AzSqlInstance.](/powershell/module/az.sql/set-azsqlinstance) É importante ter certeza de que o `$maintenanceConfig` valor deve ser um valor válido para a região do seu exemplo.  Para obter valores válidos para uma região, consulte [as janelas de manutenção disponíveis](#discover-available-maintenance-windows).


   ```powershell-interactive
   Set-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

Os exemplos a seguir mostram como configurar a janela de manutenção utilizando o Azure CLI. Pode [instalar o Azure CLI,](/cli/azure/install-azure-cli)ou utilizar a Azure Cloud Shell.

## <a name="set-the-maintenance-window-for-an-existing-database"></a>Deslote a janela de manutenção para uma base de dados existente

O exemplo a seguir define a janela de manutenção numa base de dados existente utilizando o comando [de atualização az sql db.](/cli/azure/sql/db#az_sql_db_update) O `--maint-config-id` `-m` (ou) deve ser definido como um valor válido para a região da sua base de dados. Para obter valores válidos para a sua região, consulte [as janelas de manutenção disponíveis](#discover-available-maintenance-windows).

   ```azurecli
    # Select different maintenance window
    maintenanceConfig="SQL_EastUS2_DB_2"

    # Update database
    az sql db update \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>Coloque a janela de manutenção numa piscina elástica existente

O exemplo a seguir define a janela de manutenção numa piscina elástica existente utilizando o comando [de atualização de piscina elástica az sql.](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) É importante ter certeza de que o `maintenanceConfig` valor é um valor válido para a região da sua piscina.  Para obter valores válidos para uma região, consulte [as janelas de manutenção disponíveis](#discover-available-maintenance-windows).

   ```azurecli
    # Select different maintenance window
    maintenanceConfig="SQL_EastUS2_DB_1"

    # Update pool
    az sql elastic-pool update \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>Coloque a janela de manutenção num caso gerido existente

O exemplo a seguir define a janela de manutenção utilizando [a atualização az sql mi](/cli/azure/sql/mi#az_sql_mi_update). A janela de manutenção está definida no caso, por isso todas as bases de dados no caso têm o horário da janela de manutenção do caso. Para `-MaintenanceConfigurationId` , o Bolo De *ManutençãoConfig Deve* ser um valor válido para a região do seu exemplo. Para obter valores válidos para a sua região, consulte [as janelas de manutenção disponíveis](#discover-available-maintenance-windows).

   ```azurecli
   az sql mi update -g mygroup  -n myinstance -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MainteanceConfigName}
   ```

-----

## <a name="cleanup-resources"></a>Recursos de limpeza

Certifique-se de apagar recursos desnecessários depois de terminar com eles para evitar acusações desnecessárias.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navegue para a base de dados SQL ou piscina elástica que já não precisa.
1. No menu **'Visão Geral',** selecione a opção para eliminar o recurso.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   # Delete database
   Remove-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName

   # Delete elastic pool
   Remove-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

   ```azurecli
   az sql db delete \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName

   az sql elastic-pool delete \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName
   ```

-----

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre a janela de manutenção, consulte [a janela manutenção (Pré-visualização)](maintenance-window.md).
- Para mais informações, consulte [a janela de manutenção FAQ.](maintenance-window-faq.yml)
- Para saber sobre a otimização do desempenho, consulte [monitorização e afinação de desempenho na Base de Dados Azure SQL e na Azure SQL Managed Instance](monitor-tune-overview.md).

---
title: Guia de instruções de pools de instâncias (visualização)
description: Este artigo descreve como criar e gerenciar pools de instâncias do banco de dados SQL do Azure (visualização).
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: 13c58ddf5f51e5b63d2dbe425b3ec795e21dabb8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73810341"
---
# <a name="azure-sql-database-instance-pools-preview-how-to-guide"></a>Guia de instruções do banco de dados SQL do Azure (visualização)

Este artigo fornece detalhes sobre como criar e gerenciar [pools de instância](sql-database-instance-pools.md).

## <a name="instance-pool-operations"></a>Operações de pool de instâncias

A tabela a seguir mostra as operações disponíveis relacionadas a pools de instância e sua disponibilidade no portal do Azure e no PowerShell.

|Comando|Portal do Azure|PowerShell|
|:---|:---|:---|
|Criar conjunto de instância|Não|Sim|
|Atualizar o pool de instâncias (número limitado de propriedades)|Não |Sim |
|Verificar uso e propriedades do pool de instâncias|Não|Sim |
|Excluir pool de instâncias|Não|Sim|
|Criar instância gerenciada dentro do pool de instâncias|Não|Sim|
|Atualizar o uso de recursos de instância gerenciada|Sim |Sim|
|Verificar o uso e as propriedades da instância gerenciada|Sim|Sim|
|Excluir instância gerenciada do pool|Sim|Sim|
|Criar um banco de dados na instância gerenciada colocada no pool|Sim|Sim|
|Excluir um banco de dados da instância gerenciada|Sim|Sim|

Comandos disponíveis do [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/)

|Cmdlet |Descrição |
|:---|:---|
|[New-AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | Cria um pool de instâncias do banco de dados SQL do Azure. |
|[Get-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Retorna informações sobre o pool de instâncias do SQL do Azure. |
|[Set-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | Define propriedades para um pool de instâncias do banco de dados SQL do Azure. |
|[Remove-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | Remove um pool de instâncias do banco de dados SQL do Azure. |
|[Get-AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Retorna informações sobre o uso do pool de instâncias do SQL do Azure. |


Para usar o PowerShell, [Instale a versão mais recente do PowerShell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell-core)e siga as instruções para [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Para operações relacionadas a instâncias dentro de pools e instâncias únicas, use os [comandos de instância gerenciada](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances)padrão, mas a propriedade de *nome do pool de instâncias* deve ser preenchida ao usar esses comandos para uma instância em um pool.

## <a name="how-to-deploy-managed-instances-into-pools"></a>Como implantar instâncias gerenciadas em pools

O processo de implantação de uma instância em um pool consiste nas duas etapas a seguir:

1. Implantação de pool de instância única. Essa é uma operação de execução demorada, em que a duração é a mesma que a implantação de uma [única instância criada em uma sub-rede vazia](sql-database-managed-instance.md#managed-instance-management-operations).

2. Implantação de instância repetitiva em um pool de instâncias. O parâmetro do pool de instâncias deve ser especificado explicitamente como parte dessa operação. Essa é uma operação relativamente rápida que normalmente leva até 5 minutos.

Na visualização pública, as duas etapas só têm suporte usando o PowerShell e modelos do Resource Manager. A experiência de portal do Azure não está disponível no momento.

Depois que uma instância gerenciada é implantada em um pool, você *pode* usar a Portal do Azure para alterar suas propriedades na página tipo de preço.


## <a name="create-an-instance-pool"></a>Criar um pool de instâncias

Para criar um pool de instâncias:

1. [Crie uma rede virtual com uma sub-rede](#create-a-virtual-network-with-a-subnet).
2. [Crie um pool de instâncias](#create-an-instance-pool).


### <a name="create-a-virtual-network-with-a-subnet"></a>Criar uma rede virtual com uma sub-rede 

Para posicionar vários pools de instância dentro da mesma rede virtual, consulte os seguintes artigos:

- [Determine o tamanho da sub-rede VNet para uma instância gerenciada do banco de dados SQL do Azure](sql-database-managed-instance-determine-size-vnet-subnet.md).
- Crie uma nova rede virtual e sub-rede usando o [modelo de portal do Azure](sql-database-managed-instance-create-vnet-subnet.md) ou siga as instruções para [preparar uma rede virtual existente](sql-database-managed-instance-configure-vnet-subnet.md).
 


### <a name="create-an-instance-pool"></a>Criar um pool de instâncias 

Depois de concluir as etapas anteriores, você estará pronto para criar um pool de instâncias.

As seguintes restrições se aplicam a pools de instância:

- Somente Uso Geral e Gen5 estão disponíveis em visualização pública.
- O nome do pool pode conter apenas letras minúsculas, números e hífens e não pode começar com um hífen.
- Para obter a ID de sub-rede, use `Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork`.
- Se você quiser usar AHB (Benefício Híbrido do Azure), ele será aplicado no nível do pool de instâncias. Você pode definir o tipo de licença durante a criação do pool ou atualizá-lo a qualquer momento após a criação.

> [!IMPORTANT]
> A implantação de um pool de instâncias é uma operação de execução demorada que leva aproximadamente 4,5 horas.

Para criar um pool de instâncias:

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId "/subscriptions/subscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/miPoolVirtualNetwork/subnets/miPoolSubnet" `
  -LicenseType "LicenseIncluded" `
  -VCore 80 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> Como a implantação de um pool de instâncias é uma operação de execução demorada, você precisa aguardar até que seja concluída antes de executar qualquer uma das etapas a seguir neste artigo.

## <a name="create-a-managed-instance-inside-the-pool"></a>Criar uma instância gerenciada dentro do pool 

Após a implantação bem-sucedida do pool de instâncias, é hora de criar uma instância dentro dela.

Para criar uma instância gerenciada, execute o seguinte comando:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 2 -StorageSizeInGB 256
```

A implantação de uma instância dentro de um pool leva alguns minutos. Depois que a primeira instância tiver sido criada, instâncias adicionais poderão ser criadas:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database-inside-an-instance"></a>Criar um banco de dados dentro de uma instância 

Para criar e gerenciar bancos de dados em uma instância gerenciada que esteja dentro de um pool, use os comandos de instância única.

Para criar um banco de dados dentro de uma instância gerenciada:

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-instance-pool-usage"></a>Obter uso do pool de instância 
 
Para obter uma lista de instâncias dentro de um pool:

```powershell
$instancePool | Get-AzSqlInstance
```


Para obter o uso de recursos de pool:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


Para obter uma visão geral do uso detalhado do pool e das instâncias dentro dele:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

Para listar os bancos de dados em uma instância:

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> Há um limite de 100 bancos de dados por pool (não por instância).


## <a name="scale-a-managed-instance-inside-a-pool"></a>Dimensionar uma instância gerenciada dentro de um pool 


Depois de preencher uma instância gerenciada com bancos de dados, você pode atingir limites de instância em relação ao armazenamento ou desempenho. Nesse caso, se o uso do pool não tiver sido excedido, você poderá dimensionar sua instância.
Dimensionar uma instância gerenciada dentro de um pool é uma operação que leva alguns minutos. O pré-requisito para o dimensionamento está disponível vCores e armazenamento no nível do pool de instâncias.

Para atualizar o número de vCores e o tamanho do armazenamento:

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


Para atualizar apenas o tamanho do armazenamento:

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```



## <a name="connect-to-a-managed-instance-inside-a-pool"></a>Conectar-se a uma instância gerenciada dentro de um pool

Para se conectar a uma instância gerenciada em um pool, as duas etapas a seguir são necessárias:

1. [Habilite o ponto de extremidade público para a instância](#enable-the-public-endpoint-for-the-instance).
2. [Adicione uma regra de entrada ao grupo de segurança de rede (NSG)](#add-an-inbound-rule-to-the-network-security-group).

Depois que as duas etapas forem concluídas, você poderá se conectar à instância usando um endereço de ponto de extremidade público, uma porta e credenciais fornecidas durante a criação da instância. 

### <a name="enable-the-public-endpoint-for-the-instance"></a>Habilitar o ponto de extremidade público para a instância

A habilitação do ponto de extremidade público para uma instância pode ser feita por meio do portal do Azure ou usando o seguinte comando do PowerShell:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Esse parâmetro também pode ser definido durante a criação da instância.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Adicionar uma regra de entrada ao grupo de segurança de rede 

Essa etapa pode ser feita por meio do portal do Azure ou usando comandos do PowerShell e pode ser feita a qualquer momento após a sub-rede estar preparada para a instância gerenciada.

Para obter detalhes, consulte [Permitir tráfego de ponto de extremidade público no grupo de segurança de rede](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).


## <a name="move-an-existing-single-instance-inside-an-instance-pool"></a>Mover uma instância única existente dentro de um pool de instâncias 
 
Mover instâncias para dentro e fora de um pool é uma das limitações de visualização pública. Uma solução alternativa que pode ser usada depende da restauração pontual de bancos de dados de uma instância fora de um pool para uma instância que já está em um pool. 

Ambas as instâncias devem estar na mesma assinatura e região. A restauração entre regiões e entre assinaturas não tem suporte no momento.

Esse processo tem um período de tempo de inatividade.

Para mover bancos de dados existentes:

1. Pausar cargas de trabalho na instância gerenciada da qual você está migrando.
2. Gere scripts para criar bancos de dados do sistema e executá-los na instância que está dentro do pool de instâncias.
3. Faça uma restauração pontual de cada banco de dados da instância única para a instância no pool.

    ```powershell
    $resourceGroupName = "my resource group name"
    $managedInstanceName = "my managed instance name"
    $databaseName = "my source database name"
    $pointInTime = "2019-08-21T08:51:39.3882806Z"
    $targetDatabase = "name of the new database that will be created"
    $targetResourceGroupName = "resource group of instance pool"
    $targetInstanceName = "pool instance name"
       
    Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
      -ResourceGroupName $resourceGroupName `
      -InstanceName $managedInstanceName `
      -Name $databaseName `
      -PointInTime $pointInTime `
      -TargetInstanceDatabaseName $targetDatabase `
      -TargetResourceGroupName $targetResourceGroupName `
      -TargetInstanceName $targetInstanceName
    ```

4. Aponte seu aplicativo para a nova instância e retome suas cargas de trabalho.

Se houver vários bancos de dados, repita o processo para cada um.


## <a name="next-steps"></a>Passos seguintes

- Para obter uma lista de recursos e comparação, consulte [recursos comuns do SQL](sql-database-features.md).
- Para obter mais informações sobre a configuração de VNet, consulte [configuração de vnet de instância gerenciada](sql-database-managed-instance-connectivity-architecture.md).
- Para obter um início rápido que cria uma instância gerenciada e restaura um banco de dados de um arquivo de backup, consulte [criar uma instância gerenciada](sql-database-managed-instance-get-started.md).
- Para obter um tutorial usando o serviço de migração de banco de dados do Azure (DMS) para migração, consulte [migração de instância gerenciada usando DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Para monitoramento avançado do desempenho do banco de dados de instância gerenciada com inteligência de solução de problemas interna, consulte [monitorar o banco de dados SQL do Azure usando análise de SQL do Azure](../azure-monitor/insights/azure-sql.md).
- Para obter informações sobre preços, consulte [preços da instância gerenciada do banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).

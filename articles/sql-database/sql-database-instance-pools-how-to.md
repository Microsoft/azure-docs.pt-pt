---
title: Por exemplo piscinas como guiar (pré-visualização)
description: Este artigo descreve como criar e gerir piscinas de instâncias de dados Azure SQL (pré-visualização).
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
ms.openlocfilehash: 4a27165d929cc9bc5f18e372f7f108887e466e43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299367"
---
# <a name="azure-sql-database-instance-pools-preview-how-to-guide"></a>Piscinas de instância sql de base de dados Azure (pré-visualização) como guiar

Este artigo fornece detalhes sobre como criar e gerir piscinas de [instâncias.](sql-database-instance-pools.md)

## <a name="instance-pool-operations"></a>Operações de piscina de exemplo

O quadro seguinte mostra as operações disponíveis relacionadas com piscinas de exemplo e a sua disponibilidade no portal Azure e powerShell.

|Comando|Portal do Azure|PowerShell|
|:---|:---|:---|
|Criar conjunto de instância|Não|Sim|
|Atualização piscina de instâncias (número limitado de propriedades)|Não |Sim |
|Verifique o uso e propriedades da piscina de instâncias|Não|Sim |
|Eliminar piscina de instâncias|Não|Sim|
|Criar instância gerida dentro da piscina de instância|Não|Sim|
|Atualizar utilização de recursos de instância gerida|Sim |Sim|
|Verifique o uso e propriedades de instância gerida|Sim|Sim|
|Eliminar instância gerida da piscina|Sim|Sim|
|Criar uma base de dados em instância gerida colocada na piscina|Sim|Sim|
|Eliminar uma base de dados de instância gerida|Sim|Sim|

Comandos [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/) disponíveis

|Cmdlet |Descrição |
|:---|:---|
|[New-AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | Cria um pool de instâncias de base de dados Azure SQL. |
|[Get-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Devolve informações sobre a piscina de instâncias Azure SQL. |
|[Set-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | Define propriedades para um pool de instância sql Azure. |
|[Remover-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | Remove uma piscina de instância sql Azure. |
|[Get-AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Devolve informações sobre o uso da piscina de instânciaS SQL Azure. |


Para utilizar o PowerShell, [instale a versão mais recente do PowerShell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell)e siga as instruções para [instalar o módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps).

Para operações relacionadas com casos tanto dentro de piscinas como em instâncias individuais, utilize os comandos de [instância geridos](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances)padrão, mas a propriedade de nome de *piscina deve* ser povoada quando utilizar estes comandos, por exemplo, numa piscina.

## <a name="how-to-deploy-managed-instances-into-pools"></a>Como implementar instâncias geridas em piscinas

O processo de implantação de uma instância numa piscina consiste nos dois seguintes passos:

1. Implantação de piscina de instância única. Trata-se de uma operação de longo curso, em que a duração é a mesma que a implantação de uma [única instância criada numa subrede vazia](sql-database-managed-instance.md#managed-instance-management-operations).

2. Implantação de instâncias repetitivas em uma piscina de exemplo. O parâmetro da piscina de exemplo deve ser explicitamente especificado como parte desta operação. Esta é uma operação relativamente rápida que normalmente demora até 5 minutos.

Na pré-visualização pública, ambos os passos são suportados apenas usando modelos powerShell e Resource Manager. A experiência do portal Azure não está disponível atualmente.

Depois de uma instância gerida ser implantada para uma piscina, você *pode* usar o portal Azure para alterar as suas propriedades na página de nível de preços.


## <a name="create-an-instance-pool"></a>Criar uma piscina de instâncias

Para criar uma piscina de instâncias:

1. [Criar uma rede virtual com uma sub-rede.](#create-a-virtual-network-with-a-subnet)
2. [Crie uma piscina](#create-an-instance-pool)de instâncias.


### <a name="create-a-virtual-network-with-a-subnet"></a>Criar uma rede virtual com uma subnet 

Para colocar várias piscinas de instâncias dentro da mesma rede virtual, consulte os seguintes artigos:

- Determine o tamanho da subnet VNet para uma instância gerida pela Base de [Dados Azure SQL](sql-database-managed-instance-determine-size-vnet-subnet.md).
- Crie uma nova rede virtual e sub-rede utilizando o modelo do [portal Azure](sql-database-managed-instance-create-vnet-subnet.md) ou siga as instruções para [preparar uma rede virtual existente](sql-database-managed-instance-configure-vnet-subnet.md).
 


### <a name="create-an-instance-pool"></a>Criar uma piscina de instâncias 

Depois de completar os passos anteriores, está pronto para criar uma piscina de instâncias.

As seguintes restrições aplicam-se a piscinas de exemplo:

- Apenas O Propósito Geral e a Gen5 estão disponíveis em pré-visualização pública.
- O nome da piscina pode conter apenas minúsculas, números e hífen, e não pode começar com um hífen.
- Se quiser utilizar o AHB (Azure Hybrid Benefit), é aplicado ao nível da piscina. Pode definir o tipo de licença durante a criação da piscina ou atualizá-lo a qualquer momento após a criação.

> [!IMPORTANT]
> A implantação de uma piscina de instâncias é uma operação de longa duração que demora aproximadamente 4,5 horas.

Para obter parâmetros de rede:

```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name "miPoolVirtualNetwork" -ResourceGroupName "myResourceGroup"
$subnet = Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork
```

Para criar uma piscina de instâncias:

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId $subnet.Id `
  -LicenseType "LicenseIncluded" `
  -VCore 80 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> Como a implantação de uma piscina de instâncias é uma operação de longa duração, você precisa esperar até que termine antes de executar qualquer um dos seguintes passos neste artigo.

## <a name="create-a-managed-instance-inside-the-pool"></a>Criar um caso gerido dentro da piscina 

Após a implementação bem sucedida da piscina de instância, é hora de criar uma instância dentro dela.

Para criar uma instância gerida, execute o seguinte comando:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 2 -StorageSizeInGB 256
```

Implementar uma instância dentro de uma piscina leva alguns minutos. Após a criação da primeira instância, podem ser criadas instâncias adicionais:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database-inside-an-instance"></a>Criar uma base de dados dentro de uma instância 

Para criar e gerir bases de dados num caso gerido dentro de uma piscina, use os comandos de instância única.

Para criar uma base de dados dentro de uma instância gerida:

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-instance-pool-usage"></a>Obtenha o uso da piscina de exemplo 
 
Para obter uma lista de instâncias dentro de uma piscina:

```powershell
$instancePool | Get-AzSqlInstance
```


Para obter o uso de recursos de piscina:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


Para obter uma visão geral detalhada da piscina e instâncias no seu interior:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

Para enumerar as bases de dados num caso:

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> Existe um limite de 100 bases de dados por piscina (não por exemplo).


## <a name="scale-a-managed-instance-inside-a-pool"></a>Escala uma instância gerida dentro de uma piscina 


Depois de povoar uma instância gerida com bases de dados, pode atingir limites de instância no que diz respeito ao armazenamento ou desempenho. Nesse caso, se o uso da piscina não tiver sido ultrapassado, pode escalar a sua instância.
Escalar um caso gerido dentro de uma piscina é uma operação que leva alguns minutos. O pré-requisito para a escala está disponível vCores e armazenamento no nível da piscina por exemplo.

Para atualizar o número de vCores e tamanho de armazenamento:

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


Para atualizar apenas o tamanho do armazenamento:

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```



## <a name="connect-to-a-managed-instance-inside-a-pool"></a>Ligue-se a um caso gerido dentro de uma piscina

Para se ligar a uma instância gerida numa piscina, são necessários os seguintes dois passos:

1. [Ativar o ponto final do público, por exemplo.](#enable-the-public-endpoint-for-the-instance)
2. Adicione uma regra de entrada ao grupo de [segurança da rede (NSG)](#add-an-inbound-rule-to-the-network-security-group).

Depois de ambos os passos estarem completos, pode ligar-se à instância utilizando um endereço de ponto final público, porta e credenciais fornecidas durante a criação de exemplos. 

### <a name="enable-the-public-endpoint-for-the-instance"></a>Permitir o ponto final público, por exemplo

Permitir o ponto final do público, por exemplo, pode ser feito através do portal Azure ou utilizando o seguinte comando PowerShell:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Este parâmetro também pode ser definido durante a criação de exemplos.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Adicione uma regra de entrada ao grupo de segurança da rede 

Este passo pode ser feito através do portal Azure ou utilizando comandos PowerShell, e pode ser feito a qualquer momento após a subnet a configuração para a instância gerida.

Para mais detalhes, consulte Permitir o [tráfego de pontos finais públicos no grupo de segurança da rede](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).


## <a name="move-an-existing-single-instance-inside-an-instance-pool"></a>Mover uma instância única existente dentro de uma piscina de instância 
 
Mover instâncias dentro e fora de uma piscina é uma das limitações de pré-visualização pública. Uma saliência que pode ser usada depende da restauração pontual de bases de dados de um caso fora de uma piscina para um caso que já está em uma piscina. 

Ambos os casos devem estar na mesma subscrição e região. Atualmente, a restauração transversal e a restauração por assinatura cruzada não são suportadas.

Este processo tem um período de inatividade.

Para mover bases de dados existentes:

1. Pausa na carga de trabalho no caso gerido de onde está a migrar.
2. Gere scripts para criar bases de dados do sistema e executá-las na instância que está dentro da piscina de instâncias.
3. Faça uma restauração pontual de cada base de dados, desde a instância única até à ocorrência na piscina.

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

4. Aponte a sua aplicação para a nova instância e retome as suas cargas de trabalho.

Se houver várias bases de dados, repita o processo para cada base de dados.


## <a name="next-steps"></a>Passos seguintes

- Para obter uma lista de funcionalidades e comparação, consulte [as características comuns da SQL](sql-database-features.md).
- Para obter mais informações sobre a configuração vNet, consulte a [configuração VNet de instância gerida](sql-database-managed-instance-connectivity-architecture.md).
- Para um arranque rápido que cria uma instância gerida e restaura uma base de dados a partir de um ficheiro de cópia de segurança, consulte [criar uma instância gerida](sql-database-managed-instance-get-started.md).
- Para um tutorial utilizando o Serviço de Migração de Bases de Dados Azure (DMS) para migração, consulte a migração por [exemplo gerida utilizando DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Para uma monitorização avançada do desempenho da base de dados de instâncias geridas com inteligência incorporada de resolução de problemas, consulte a Base de [Dados Monitor Azure SQL utilizando o Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Para obter informações sobre preços, consulte a Base de [Dados SQL gerida por preços](https://azure.microsoft.com/pricing/details/sql-database/managed/)de instância .

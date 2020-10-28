---
title: Implementar a SQL Managed Instance para uma piscina de exemplo
titleSuffix: Azure SQL Managed Instance
description: Este artigo descreve como criar e gerir piscinas Azure SQL Managed Instance (pré-visualização).
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 09/05/2019
ms.openlocfilehash: 732a9bab018103321a9a3a759b31495bd67a209e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793114"
---
# <a name="deploy-azure-sql-managed-instance-to-an-instance-pool"></a>Implementar Azure SQL Gestd Instance para uma piscina de instância
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo fornece detalhes sobre como criar uma [piscina de instância](instance-pools-overview.md) e implementar Azure SQL Managed Instance para ele. 

## <a name="instance-pool-operations"></a>Caso operações de piscina

A tabela a seguir mostra as operações disponíveis relacionadas com os pools de exemplo e a sua disponibilidade no portal Azure e no PowerShell.

|Comando|Portal do Azure|PowerShell|
|:---|:---|:---|
|Criar uma piscina de exemplos|Não|Sim|
|Atualizar uma piscina de instância (número limitado de propriedades)|Não |Sim |
|Verifique uma instância de utilização da piscina e propriedades|Não|Sim |
|Excluir uma piscina de instância|Não|Sim|
|Criar um caso gerido dentro de uma piscina de exemplo|Não|Sim|
|Atualizar o uso do recurso para uma instância gerida|Sim |Sim|
|Verifique a utilização e as propriedades para uma instância gerida|Sim|Sim|
|Apagar uma instância gerida da piscina|Sim|Sim|
|Criar uma base de dados no caso dentro da piscina|Sim|Sim|
|Eliminar uma base de dados da SQL Managed Instance|Sim|Sim|

Comandos [PowerShell](/powershell/module/az.sql/)disponíveis:

|Cmdlet |Descrição |
|:---|:---|
|[New-AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | Cria uma piscina SQL Managed Instance. |
|[Get-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Devolve informações sobre uma piscina de casos. |
|[Set-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | Define propriedades para uma piscina de exemplo em SQL Managed Instance. |
|[Remove-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | Remove uma piscina de exemplo em SQL Managed Instance. |
|[Get-AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Devolve informações sobre o uso da piscina SQL Managed Instance. |


Para utilizar o PowerShell, [instale a versão mais recente do PowerShell Core](/powershell/scripting/install/installing-powershell#powershell)e siga as instruções para [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps).

Para operações relacionadas com casos tanto dentro de piscinas como instâncias individuais, utilize os comandos de [instância geridos](api-references-create-manage-instance.md#powershell-create-and-configure-managed-instances)padrão, mas a propriedade de nome de *piscina deve* ser povoada quando se utilizam estes comandos, por exemplo, numa piscina.

## <a name="deployment-process"></a>Processo de implementação

Para implantar uma instância gerida num conjunto de instâncias, você deve primeiro implantar o pool de instância, que é uma operação de longa duração única em que a duração é a mesma que implantar uma [única instância criada numa sub-rede vazia](sql-managed-instance-paas-overview.md#management-operations). Depois disso, você pode colocar uma instância gerida na piscina, que é uma operação relativamente rápida que normalmente leva até cinco minutos. O parâmetro da piscina de instância deve ser explicitamente especificado como parte desta operação.

Na pré-visualização pública, ambas as ações são suportadas apenas usando modelos powerShell e Azure Resource Manager. A experiência do portal Azure não está disponível atualmente.

Depois de uma instância gerida ser implantada numa piscina, *pode* utilizar o portal Azure para alterar as suas propriedades na página de nível de preços.

## <a name="create-a-virtual-network-with-a-subnet"></a>Criar uma rede virtual com uma sub-rede 

Para colocar várias piscinas de instância dentro da mesma rede virtual, consulte os seguintes artigos:

- [Determine o tamanho da sub-rede VNet para Azure SQL Managed Instance](vnet-subnet-determine-size.md).
- Crie uma nova rede virtual e sub-rede utilizando o modelo do [portal Azure](virtual-network-subnet-create-arm-template.md) ou siga as instruções para [preparar uma rede virtual existente](vnet-existing-add-subnet.md).
 

## <a name="create-an-instance-pool"></a>Criar uma piscina de exemplos 

Depois de completar os passos anteriores, está pronto para criar uma piscina de exemplo.

As seguintes restrições aplicam-se aos agrupamentos de instância:

- Apenas a General Purpose e a Gen5 estão disponíveis em visualização pública.
- O nome da piscina pode conter apenas letras minúsculas, números e hífens, e não pode começar com um hífen.
- Se quiser utilizar o Azure Hybrid Benefit, é aplicado ao nível da piscina. Pode definir o tipo de licença durante a criação da piscina ou atualizá-la a qualquer momento após a criação.

> [!IMPORTANT]
> A implantação de uma piscina de exemplos é uma operação de longa duração que demora aproximadamente 4,5 horas.

Para obter parâmetros de rede:

```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name "miPoolVirtualNetwork" -ResourceGroupName "myResourceGroup"
$subnet = Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork
```

Para criar uma piscina de exemplos:

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId $subnet.Id `
  -LicenseType "LicenseIncluded" `
  -VCore 8 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> Como a implantação de uma piscina de instância é uma operação de longa duração, é necessário esperar até que esteja concluída antes de executar qualquer um dos seguintes passos neste artigo.

## <a name="create-a-managed-instance"></a>Criar uma instância gerida

Após a implementação bem sucedida do pool de exemplos, é hora de criar uma instância gerida dentro dele.

Para criar uma instância gerida, execute o seguinte comando:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-one-name" -VCore 2 -StorageSizeInGB 256
```

Colocar um caso dentro de uma piscina leva alguns minutos. Após a primeira instância ter sido criada, podem ser criados casos adicionais:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-two-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database"></a>Criar uma base de dados 

Para criar e gerir bases de dados num caso gerido dentro de uma piscina, use os comandos de instância única.

Para criar uma base de dados dentro de uma instância gerida:

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-pool-usage"></a>Obtenha o uso da piscina 
 
Para obter uma lista de casos dentro de uma piscina:

```powershell
$instancePool | Get-AzSqlInstance
```


Para obter o uso do recurso de piscina:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


Para obter uma visão detalhada da visão geral da piscina e instâncias no seu interior:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

Para listar as bases de dados num caso:

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> Existe um limite de 100 bases de dados por pool (não por exemplo).


## <a name="scale"></a>Escala 


Depois de povoar um caso gerido com bases de dados, pode atingir limites de casos relativos ao armazenamento ou desempenho. Nesse caso, se o uso da piscina não tiver sido excedido, pode escalar o seu caso.
Escalar um caso gerido dentro de uma piscina é uma operação que leva alguns minutos. O pré-requisito para a escala está disponível vCores e armazenamento no nível da piscina de exemplo.

Para atualizar o número de vCores e tamanho de armazenamento:

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


Para atualizar apenas o tamanho do armazenamento:

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```

## <a name="connect"></a>Ligar 

Para se ligar a uma instância gerida numa piscina, são necessários os dois passos seguintes:

1. [Ativar o ponto final público, por exemplo.](#enable-the-public-endpoint)
2. [Adicione uma regra de entrada ao grupo de segurança da rede (NSG)](#add-an-inbound-rule-to-the-network-security-group).

Depois de ambos os passos estarem completos, pode ligar-se ao caso utilizando um endereço de ponto final público, porta e credenciais fornecidas durante a criação de exemplos. 

### <a name="enable-the-public-endpoint"></a>Ativar o ponto final público

Permitir o ponto final público, por exemplo, pode ser feito através do portal Azure ou utilizando o seguinte comando PowerShell:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Este parâmetro também pode ser definido durante a criação de exemplos.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Adicione uma regra de entrada ao grupo de segurança da rede 

Este passo pode ser feito através do portal Azure ou utilizando comandos PowerShell, e pode ser feito a qualquer momento após a sub-rede estar preparada para a instância gerida.

Para mais informações, consulte [Permitir o tráfego de ponto final público no grupo de segurança da rede](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).


## <a name="move-an-existing-single-instance-to-a-pool"></a>Mova um único exemplo existente para uma piscina
 
Mover casos dentro e fora de uma piscina é uma das limitações de visualização pública. Uma solução conta com a restauração pontual de bases de dados de um caso fora de uma piscina para um caso que já está em uma piscina. 

Ambas as instâncias devem estar na mesma subscrição e região. A restauração transversal e transversal não é atualmente suportada.

Este processo tem um período de inatividade.

Para mover as bases de dados existentes:

1. Faça uma pausa nas cargas de trabalho sobre o caso gerido de que está a migrar.
2. Gere scripts para criar bases de dados do sistema e executá-las no caso que está dentro do conjunto de casos.
3. Faça um restauro pontual de cada base de dados, desde a única instância até ao caso na piscina.

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

Se existirem várias bases de dados, repita o processo para cada base de dados.


## <a name="next-steps"></a>Passos seguintes

- Para obter uma lista de funcionalidades e comparação, consulte [as características comuns sql](../database/features-comparison.md).
- Para obter mais informações sobre a configuração VNet, consulte [a configuração VNet de instância gerida SQL](connectivity-architecture-overview.md).
- Para um arranque rápido que cria uma instância gerida e restaura uma base de dados a partir de um ficheiro de backup, consulte [Criar uma instância gerida](instance-create-quickstart.md).
- Para obter um tutorial sobre a utilização do Serviço de Migração da Base de Dados Azure para migração, consulte [a migração de instâncias geridas SQL utilizando o Serviço de Migração de Bases de Dados.](../../dms/tutorial-sql-server-to-managed-instance.md)
- Para uma monitorização avançada do desempenho da base de dados SQL Managed Instance com inteligência incorporada para resolução de problemas, consulte [o Monitor Azure SQL Managed Instance utilizando a Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).
- Para obter informações sobre preços, consulte [os preços da SQL Managed Instance](https://azure.microsoft.com/pricing/details/sql-database/managed/).
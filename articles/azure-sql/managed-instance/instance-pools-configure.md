---
title: Implementar um Caso Gerido SQL para uma piscina de instância
titleSuffix: Azure SQL Managed Instance
description: Este artigo descreve como criar e gerir piscinas de instância gerida sql Azure (pré-visualização).
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: c781e23b23f5dbaf8eba9efe4c27428ef35c7be1
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84113632"
---
# <a name="deploy-an-azure-sql-managed-instance-to-an-instance-pool"></a>Implementar um Azure SQL Managed Instance para uma piscina de instância
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo fornece detalhes sobre como criar [piscinas](instance-pools-overview.md) de instância e implementar um Azure SQL Managed Instance para ele. 

## <a name="instance-pool-operations"></a>Operações de piscina de exemplo

O quadro seguinte mostra as operações disponíveis relacionadas com piscinas de exemplo e a sua disponibilidade no portal Azure e powerShell.

|Comando|Portal do Azure|PowerShell|
|:---|:---|:---|
|Criar conjunto de instância|Não|Sim|
|Atualização piscina de instâncias (número limitado de propriedades)|Não |Sim |
|Verifique o uso e propriedades da piscina de instâncias|Não|Sim |
|Eliminar piscina de instâncias|Não|Sim|
|Criar instância gerida SQL dentro da piscina de instância|Não|Sim|
|Atualizar o uso de recursos de instância gerida sQL|Sim |Sim|
|Verifique o uso e propriedades da Instância Gerida SQL|Sim|Sim|
|Eliminar a Instância Gerida SQL da piscina|Sim|Sim|
|Criar uma base de dados, por exemplo, dentro da piscina|Sim|Sim|
|Eliminar uma base de dados da SQL Managed Instance|Sim|Sim|

Comandos [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/) disponíveis

|Cmdlet |Descrição |
|:---|:---|
|[New-AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | Cria uma piscina SQL Managed Instance. |
|[Get-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Devolve informações sobre piscina de instâncias. |
|[Set-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | Define propriedades para uma piscina por exemplo em SQL Managed Instance. |
|[Remover-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | Remove uma piscina de instância sQL managed Instance. |
|[Get-AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Devolve informações sobre o uso da piscina SQL Managed Instance. |


Para utilizar o PowerShell, [instale a versão mais recente do PowerShell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell)e siga as instruções para [instalar o módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps).

Para operações relacionadas com casos tanto dentro de piscinas como em instâncias individuais, utilize os comandos de [instância geridos](api-references-create-manage-instance.md#powershell-create-and-manage-managed-instances)padrão, mas a propriedade de nome de *piscina deve* ser povoada quando utilizar estes comandos, por exemplo, numa piscina.

## <a name="deployment-process"></a>Processo de implementação

Para implantar um SQL Managed Instance numa piscina de instância, deve primeiro implantar a piscina de instância, que é uma operação de longa duração única em que a duração é a mesma que a implantação de uma [única instância criada numa subnet vazia](sql-managed-instance-paas-overview.md#management-operations). Depois disso, pode colocar instâncias geridas sQL na piscina, que é uma operação relativamente rápida que normalmente demora até cinco minutos. O parâmetro da piscina de exemplo deve ser explicitamente especificado como parte desta operação.

Na pré-visualização pública, ambas as ações são suportadas apenas usando modelos powerShell e Resource Manager. A experiência do portal Azure não está disponível atualmente.

Depois de a Instância Gerida SQL ser implantada para uma piscina, *pode* utilizar o portal Azure para alterar as suas propriedades na página de nível de preços.

## <a name="create-virtual-network-with-a-subnet"></a>Criar rede virtual com uma subnet 

Para colocar várias piscinas de instâncias dentro da mesma rede virtual, consulte os seguintes artigos:

- Determine o tamanho da [subnet VNet para uma instância gerida azure SQL](vnet-subnet-determine-size.md).
- Crie uma nova rede virtual e sub-rede utilizando o modelo do [portal Azure](virtual-network-subnet-create-arm-template.md) ou siga as instruções para [preparar uma rede virtual existente](vnet-existing-add-subnet.md).
 

## <a name="create-instance-pool"></a>Criar conjunto de instância 

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

## <a name="create-sql-managed-instance"></a>Criar Instância Gerida do SQL

Após a implementação bem sucedida da piscina de instância, é hora de criar um SQL Managed Instance dentro dela.

Para criar uma Instância Gerida SQL, execute o seguinte comando:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 2 -StorageSizeInGB 256
```

Implementar uma instância dentro de uma piscina leva alguns minutos. Após a criação da primeira instância, podem ser criadas instâncias adicionais:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database"></a>Criar uma base de dados 

Para criar e gerir bases de dados num Caso Gerido SQL que está dentro de uma piscina, use os comandos de instância única.

Para criar uma base de dados dentro de uma Instância Gerida SQL:

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-pool-usage"></a>Obtenha o uso da piscina 
 
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


## <a name="scale"></a>Escala 


Depois de povoar uma Instância Gerida SQL com bases de dados, pode atingir limites de instância no que diz respeito ao armazenamento ou desempenho. Nesse caso, se o uso da piscina não tiver sido ultrapassado, pode escalar a sua instância.
Escalar um SQL Managed Instance dentro de uma piscina é uma operação que leva alguns minutos. O pré-requisito para a escala está disponível vCores e armazenamento no nível da piscina por exemplo.

Para atualizar o número de vCores e tamanho de armazenamento:

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


Para atualizar apenas o tamanho do armazenamento:

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```

## <a name="connect"></a>Ligar 

Para ligar a uma Instância Gerida SQL numa piscina, são necessários os seguintes dois passos:

1. [Ativar o ponto final do público, por exemplo.](#enable-public-endpoint)
2. Adicione uma regra de entrada ao grupo de [segurança da rede (NSG)](#add-an-inbound-rule-to-the-network-security-group).

Depois de ambos os passos estarem completos, pode ligar-se à instância utilizando um endereço de ponto final público, porta e credenciais fornecidas durante a criação de exemplos. 

### <a name="enable-public-endpoint"></a>Ativar o ponto final do público

Permitir o ponto final do público, por exemplo, pode ser feito através do portal Azure ou utilizando o seguinte comando PowerShell:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Este parâmetro também pode ser definido durante a criação de exemplos.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Adicione uma regra de entrada ao grupo de segurança da rede 

Este passo pode ser feito através do portal Azure ou utilizando comandos PowerShell, e pode ser feito a qualquer momento após a subnet a configuração para a instância gerida.

Para mais detalhes, consulte Permitir o [tráfego de pontos finais públicos no grupo de segurança da rede](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).


## <a name="move-existing-single-instance-to-pool"></a>Mova a instância única existente para a piscina
 
Mover instâncias dentro e fora de uma piscina é uma das limitações de pré-visualização pública. Uma saliência baseia-se na restauração pontual de bases de dados de um caso fora de uma piscina para um caso que já está em uma piscina. 

Ambos os casos devem estar na mesma subscrição e região. Atualmente, a restauração transversal e a restauração por assinatura cruzada não são suportadas.

Este processo tem um período de inatividade.

Para mover bases de dados existentes:

1. Pausa na sql Managed Instance de onde está a migrar.
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


## <a name="next-steps"></a>Próximos passos

- Para obter uma lista de funcionalidades e comparação, consulte [as características comuns da SQL](../database/features-comparison.md).
- Para obter mais informações sobre a configuração vNet, consulte a [configuração VNet de Instância Gerida sQL](connectivity-architecture-overview.md).
- Para um arranque rápido que cria uma instância gerida e restaura uma base de dados a partir de um ficheiro de cópia de segurança, consulte [criar uma Instância Gerida SQL](instance-create-quickstart.md).
- Para um tutorial utilizando o Serviço de Migração de Bases de Dados Azure (DMS) para migração, consulte a [migração sQL Managed Instance utilizando DMS](../../dms/tutorial-sql-server-to-managed-instance.md).
- Para uma monitorização avançada do desempenho da base de dados SQL Managed Instance com inteligência incorporada de resolução de problemas, consulte [monitor Azure SQL Managed Instance utilizando o Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).
- Para obter informações sobre preços, consulte os preços da [Instância Gerida sQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).

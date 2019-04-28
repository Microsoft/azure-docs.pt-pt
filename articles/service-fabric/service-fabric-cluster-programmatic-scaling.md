---
title: Azure Service Fabric programática dimensionamento | Documentos da Microsoft
description: Dimensionar um cluster do Azure Service Fabric dentro ou para fora por meio de programação, de acordo com os acionadores personalizados
services: service-fabric
documentationcenter: .net
author: mjrousos
manager: jonjung
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: mikerou
ms.openlocfilehash: 552c9820cca4380c00e1bf435fdb3d068c0690fb
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62111308"
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>Dimensionar um cluster do Service Fabric através de programação 

Clusters do Service Fabric em execução no Azure são criados sobre conjuntos de dimensionamento de máquina virtual.  [Dimensionamento de clusters](./service-fabric-cluster-scale-up-down.md) descreve como clusters do Service Fabric podem ser dimensionados manualmente ou com regras de dimensionamento automático. Este artigo descreve como gerir as credenciais e dimensionar um cluster no ou horizontalmente com o Azure fluent SDK, que é um cenário mais avançado de computação. Para uma descrição geral, leia [métodos programáticos de coordenação de operações de dimensionamento do Azure](service-fabric-cluster-scaling.md#programmatic-scaling). 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="manage-credentials"></a>Gerir credenciais
Um desafio de escrever um serviço para processar o dimensionamento é que o serviço tem de ser capaz de aceder aos recursos de conjunto de dimensionamento de máquina virtual sem um início de sessão interativo. Aceder ao cluster do Service Fabric é fácil se o serviço de dimensionamento é modificar a sua própria aplicação do Service Fabric, mas são necessárias credenciais para aceder ao conjunto de dimensionamento. Para iniciar sessão, pode utilizar um [principal de serviço](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli) criados com o [CLI do Azure](https://github.com/azure/azure-cli).

Um principal de serviço pode ser criado com os seguintes passos:

1. Inicie sessão na CLI do Azure (`az login`) como um utilizador com acesso ao dimensionamento da máquina virtual definido
2. Criar principal com o tipo de serviço `az ad sp create-for-rbac`
    1. Anote o appId (chamado de "ID de cliente" em outro lugar), o nome, a palavra-passe e o inquilino para utilização posterior.
    2. Também precisará de ID da subscrição, que pode ser vista com `az account list`

A biblioteca de computação Fluente pode iniciar sessão com estas credenciais da seguinte forma (Observe que como principais tipos de Azure fluent `IAzure` estão no [Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/) pacote):

```csharp
var credentials = new AzureCredentials(new ServicePrincipalLoginInformation {
                ClientId = AzureClientId,
                ClientSecret = 
                AzureClientKey }, AzureTenantId, AzureEnvironment.AzureGlobalCloud);
IAzure AzureClient = Azure.Authenticate(credentials).WithSubscription(AzureSubscriptionId);

if (AzureClient?.SubscriptionId == AzureSubscriptionId)
{
    ServiceEventSource.Current.ServiceMessage(Context, "Successfully logged into Azure");
}
else
{
    ServiceEventSource.Current.ServiceMessage(Context, "ERROR: Failed to login to Azure");
}
```

Depois de iniciar sessão, a contagem de instâncias de conjunto de dimensionamento pode ser consultada por meio de `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity`.

## <a name="scaling-out"></a>Aumentar horizontalmente
Utilizar o Azure fluent de computação SDK, instâncias podem ser adicionadas ao conjunto com apenas algumas chamadas - de dimensionamento de máquina virtual

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

Em alternativa, o tamanho do conjunto de dimensionamento de máquina virtual também pode ser gerido com cmdlets do PowerShell. [`Get-AzVmss`](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss) Pode obter o objeto de conjunto de dimensionamento de máquina virtual. A capacidade atual está disponível através do `.sku.capacity` propriedade. Depois de alterar a capacidade para o valor pretendido, o conjunto de dimensionamento no Azure pode ser atualizado com o [ `Update-AzVmss` ](https://docs.microsoft.com/powershell/module/az.compute/update-azvmss) comando.

Como adicionar um conjunto de dimensionamento instância deve ser ao adicionar manualmente um nó, tudo o que é necessário para iniciar um novo nó do Service Fabric, uma vez que o conjunto de dimensionamento modelo inclui extensões para associar automaticamente novas instâncias ao cluster do Service Fabric. 

## <a name="scaling-in"></a>Reduzir horizontalmente

Reduzir horizontalmente é semelhante ao aumentar horizontalmente. O real das máquinas virtuais do conjunto de dimensionamento alterações são praticamente os mesmos. No entanto, como foi discutido anteriormente, o Service Fabric apenas limpa automaticamente removidos nós com uma durabilidade de Gold ou Silver. Por isso, a durabilidade de Bronze redução horizontal caso, é necessário interagir com o cluster do Service Fabric para encerrar o nó ser removido e, em seguida, para remover o seu estado.

Preparar o nó para encerramento envolve localizar o nó ser removido (a adicionadas recentemente dimensionamento conjunto instância da máquina virtual) e desativar o-lo. Instâncias do conjunto de dimensionamento de máquina virtual são numeradas na ordem em que são adicionadas, para que nós mais recente podem ser encontrados ao comparar o sufixo de número nos nomes de nós (correspondência de que o dimensionamento de máquinas virtuais subjacentes definido nomes de instância). 

```csharp
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

Assim que o nó ser removido for encontrado, ele pode ser desativado e removido com o mesmo `FabricClient` instância e o `IAzure` instância anteriores.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Como com aumentar horizontalmente, cmdlets do PowerShell para modificar o dimensionamento de máquinas virtuais capacidade do conjunto pode também ser aqui utilizada se uma abordagem de criação de scripts é preferível. Assim que a instância de máquina virtual for removida, o estado de nó do Service Fabric pode ser removido.

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="next-steps"></a>Passos Seguintes

Para começar a implementar a sua própria lógica de dimensionamento automático, familiarize-se com os seguintes conceitos e as APIs úteis:

- [Dimensionamento manualmente ou com regras de dimensionamento automático](./service-fabric-cluster-scale-up-down.md)
- [Fluent bibliotecas de gestão do Azure para .NET](https://github.com/Azure/azure-sdk-for-net/tree/Fluent) (útil para interagir com conjuntos de dimensionamento de máquinas virtuais subjacentes do cluster do Service Fabric)
- [System.Fabric.FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) (útil para interagir com um cluster do Service Fabric e os respetivos nós)

---
title: Dimensionamento programático do Azure Service Fabric
description: Dimensionar um cluster de Service Fabric do Azure para dentro ou para fora de forma programática, de acordo com gatilhos personalizados
author: mjrousos
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: mikerou
ms.openlocfilehash: ffe07960c6d32bea8ec31b1fe8248b6abc2b63af
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458282"
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>Dimensionar um Cluster Service Fabric de forma programática 

Service Fabric clusters em execução no Azure são criados sobre os conjuntos de dimensionamento de máquinas virtuais.  O [dimensionamento de cluster](./service-fabric-cluster-scale-up-down.md) descreve como Service Fabric clusters podem ser dimensionados manualmente ou com regras de dimensionamento automático. Este artigo descreve como gerenciar credenciais e dimensionar um cluster para dentro ou para fora usando o SDK de computação do Azure fluente, que é um cenário mais avançado. Para obter uma visão geral, leia [métodos programáticos para coordenar operações de dimensionamento do Azure](service-fabric-cluster-scaling.md#programmatic-scaling). 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="manage-credentials"></a>Gerir credenciais
Um desafio de escrever um serviço para lidar com o dimensionamento é que o serviço deve ser capaz de acessar recursos do conjunto de dimensionamento de máquinas virtuais sem um logon interativo. O acesso ao cluster de Service Fabric é fácil se o serviço de dimensionamento estiver modificando seu próprio aplicativo Service Fabric, mas as credenciais serão necessárias para acessar o conjunto de dimensionamento. Para entrar, você pode usar uma [entidade de serviço](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli) criada com o [CLI do Azure](https://github.com/azure/azure-cli).

Uma entidade de serviço pode ser criada com as seguintes etapas:

1. Entrar no CLI do Azure (`az login`) como um usuário com acesso ao conjunto de dimensionamento de máquinas virtuais
2. Criar a entidade de serviço com `az ad sp create-for-rbac`
    1. Anote a appId (chamada ' Client ID ' em outro lugar), nome, senha e locatário para uso posterior.
    2. Você também precisará da sua ID de assinatura, que pode ser exibida com `az account list`

A biblioteca de computação fluente pode entrar usando essas credenciais da seguinte maneira (Observe que os principais tipos do Azure fluentes como `IAzure` estão no pacote [Microsoft. Azure. Management. Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/) ):

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

Depois de conectado, a contagem de instâncias do conjunto de dimensionamento pode ser consultada por meio de `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity`.

## <a name="scaling-out"></a>Aumentar horizontalmente
Usando o SDK de computação do Azure fluente, as instâncias podem ser adicionadas ao conjunto de dimensionamento de máquinas virtuais com apenas algumas chamadas-

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

Como alternativa, o tamanho do conjunto de dimensionamento de máquinas virtuais também pode ser gerenciado com cmdlets do PowerShell. [`Get-AzVmss`](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss) pode recuperar o objeto do conjunto de dimensionamento de máquinas virtuais. A capacidade atual está disponível por meio da propriedade `.sku.capacity`. Depois de alterar a capacidade para o valor desejado, o conjunto de dimensionamento de máquinas virtuais no Azure pode ser atualizado com o comando [`Update-AzVmss`](https://docs.microsoft.com/powershell/module/az.compute/update-azvmss) .

Como ao adicionar um nó manualmente, a adição de uma instância do conjunto de dimensionamento deve ser tudo o que é necessário para iniciar um novo nó Service Fabric, uma vez que o modelo do conjunto de dimensionamento inclui extensões para unir automaticamente novas instâncias ao Cluster Service Fabric. 

## <a name="scaling-in"></a>Dimensionamento

O dimensionamento no é semelhante à expansão. As alterações reais do conjunto de dimensionamento de máquinas virtuais são praticamente as mesmas. Mas, como foi discutido anteriormente, Service Fabric apenas limpa automaticamente os nós removidos com uma durabilidade ouro ou prata. Portanto, no caso de escala de durabilidade bronze, é necessário interagir com o Cluster Service Fabric para desligar o nó a ser removido e, em seguida, remover seu estado.

A preparação do nó para desligamento envolve a localização do nó a ser removido (a instância do conjunto de dimensionamento de máquinas virtuais adicionada mais recentemente) e a desativação. As instâncias do conjunto de dimensionamento de máquinas virtuais são numeradas na ordem em que são adicionadas, para que nós mais novos possam ser encontrados comparando o sufixo de número nos nomes dos nós (que correspondem aos nomes de instância do conjunto de dimensionamento de máquinas virtuais subjacentes). 

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

Depois que o nó a ser removido for encontrado, ele poderá ser desativado e removido usando a mesma instância de `FabricClient` e a instância de `IAzure` do anterior.

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

Assim como acontece com a expansão, os cmdlets do PowerShell para modificar a capacidade do conjunto de dimensionamento de máquinas virtuais também podem ser usados aqui se uma abordagem de script for preferível. Depois que a instância da máquina virtual for removida, Service Fabric estado do nó poderá ser removido.

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="next-steps"></a>Passos seguintes

Para começar a implementar sua própria lógica de dimensionamento automático, familiarize-se com os seguintes conceitos e APIs úteis:

- [Dimensionamento manual ou com regras de dimensionamento automático](./service-fabric-cluster-scale-up-down.md)
- [Bibliotecas de gerenciamento do Azure fluente para .net](https://github.com/Azure/azure-sdk-for-net/tree/Fluent) (útil para interagir com os conjuntos de dimensionamento de máquinas virtuais subjacentes do Cluster Service Fabric)
- [System. Fabric. FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) (útil para interagir com um Cluster Service Fabric e seus nós)

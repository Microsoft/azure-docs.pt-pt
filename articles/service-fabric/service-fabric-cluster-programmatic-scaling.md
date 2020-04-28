---
title: Dimensionamento Programático de Tecido de Serviço Azure
description: Escala um cluster de tecido de serviço Azure dentro ou fora programáticamente, de acordo com os gatilhos personalizados
author: mjrousos
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: mikerou
ms.openlocfilehash: ffe07960c6d32bea8ec31b1fe8248b6abc2b63af
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75458282"
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>Escala de um cluster de tecido de serviço programática 

Os clusters de tecido de serviço em funcionamento em Azure são construídos em cima de conjuntos de escala de máquinas virtuais.  [A escala de cluster](./service-fabric-cluster-scale-up-down.md) descreve como os clusters de tecido de serviço podem ser dimensionados manualmente ou com regras de escala automática. Este artigo descreve como gerir credenciais e escalar um cluster dentro ou fora usando o fluente sdk de computação Azure, que é um cenário mais avançado. Para uma visão geral, leia [métodos programáticos de coordenação de operações de escala azure.](service-fabric-cluster-scaling.md#programmatic-scaling) 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="manage-credentials"></a>Gerir credenciais
Um dos desafios da escrita de um serviço para lidar com a escala é que o serviço deve ser capaz de aceder a recursos conjuntos de máquinas virtuais sem um login interativo. O acesso ao cluster Service Fabric é fácil se o serviço de escala estiver a modificar a sua própria aplicação Service Fabric, mas são necessárias credenciais para aceder ao conjunto de escala. Para iniciar sessão, pode utilizar um diretor de [serviço](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli) criado com o [Azure CLI](https://github.com/azure/azure-cli).

Um diretor de serviço pode ser criado com os seguintes passos:

1. Inscreva-se no Azure`az login`CLI ( ) como um utilizador com acesso ao conjunto de escala de máquina virtual
2. Criar o diretor de serviço com`az ad sp create-for-rbac`
    1. Tome nota da appId (chamada 'ID do cliente' em outro lugar), nome, senha e inquilino para uso posterior.
    2. Você também vai precisar do seu ID de subscrição, que pode ser visto com`az account list`

A fluente biblioteca de cálculo pode assinar usando estas credenciais da `IAzure` seguinte forma (note que os tipos de azure fluentes como estão no pacote [Microsoft.Azure.Management.Fluent):](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/)

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

Uma vez iniciado, a contagem de instâncias `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity`de conjunto de escala pode ser consultada através de .

## <a name="scaling-out"></a>Aumentar horizontalmente
Utilizando o fluente SDK computacional Azure, as instâncias podem ser adicionadas à escala de máquina virtual definida com apenas algumas chamadas -

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

Alternativamente, o tamanho do conjunto de máquinas virtuais também pode ser gerido com cmdlets PowerShell. [`Get-AzVmss`](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss)pode recuperar o objeto conjunto de escala de máquina virtual. A capacidade atual está `.sku.capacity` disponível através do imóvel. Depois de alterar a capacidade para o valor desejado, a escala de [`Update-AzVmss`](https://docs.microsoft.com/powershell/module/az.compute/update-azvmss) máquina virtual definida em Azure pode ser atualizada com o comando.

Como ao adicionar um nó manualmente, adicionar uma instância de conjunto de escala deve ser tudo o que é necessário para iniciar um novo nó de Tecido de Serviço, uma vez que o modelo de conjunto de escala inclui extensões para aderir automaticamente a novas instâncias ao cluster De Tecido de Serviço. 

## <a name="scaling-in"></a>Escalando em

Escalar é semelhante ao escalonamento. As alterações reais da escala de máquinavirtual são praticamente as mesmas. Mas, como foi discutido anteriormente, o Service Fabric apenas limpa automaticamente os nós removidos com uma durabilidade de Ouro ou Prata. Assim, no caso da escala de durabilidade do Bronze, é necessário interagir com o cluster de tecido de serviço para desligar o nó a ser removido e, em seguida, remover o seu estado.

Preparar o nó para encerramento envolve encontrar o nó a ser removido (a instância de conjunto de escala de máquina virtual mais recente) e desativar o mesmo. As instâncias de conjunto de escala de máquina virtual são numeradas na ordem em que são adicionadas, pelo que os nódos mais recentes podem ser encontrados comparando o sufixo de número nos nomes dos nós (que correspondem aos nomes de exemplos de instâncias de escala de máquina virtual subjacente). 

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

Uma vez que o nó a ser removido, pode ser `FabricClient` desativado e removido usando a mesma instância e a `IAzure` instância de antes.

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

Tal como acontece com a escala, os cmdlets PowerShell para modificar a capacidade de conjunto de escala de máquina virtual também podem ser usados aqui se uma abordagem de scriptfor preferível. Uma vez removida a instância da máquina virtual, o estado do nó do tecido de serviço pode ser removido.

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="next-steps"></a>Passos seguintes

Para começar a implementar a sua própria lógica de auto-escala, familiarize-se com os seguintes conceitos e APIs úteis:

- [Escalamanualmente ou com regras de escala automática](./service-fabric-cluster-scale-up-down.md)
- [Fluent Azure Management Libraries for .NET](https://github.com/Azure/azure-sdk-for-net/tree/Fluent) (útil para interagir com os conjuntos de escala de máquinavirtual subjacentes de um cluster de tecido de serviço)
- [System.Fabric.FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) (útil para interagir com um cluster de tecido de serviço e seus nós)

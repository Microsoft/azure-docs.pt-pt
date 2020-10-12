---
title: Escala programática programática de tecido de serviço Azure
description: Escalar um cluster de tecido de serviço Azure dentro ou fora programáticamente, de acordo com os gatilhos personalizados
author: mjrousos
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: mikerou
ms.custom: devx-track-csharp
ms.openlocfilehash: bd47e5e39684bd4b684cd1e12dd9a3d420640ee2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89005825"
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>Escalar um cluster de tecido de serviço programáticamente 

Os clusters de tecido de serviço em execução em Azure são construídos em cima de conjuntos de escala de máquina virtual.  [A escala de cluster](./service-fabric-cluster-scale-in-out.md) descreve como os clusters de tecido de serviço podem ser dimensionados manualmente ou com regras de escala automática. Este artigo descreve como gerir credenciais e escalar um cluster dentro ou fora usando o fluente Azure compute SDK, que é um cenário mais avançado. Para uma visão geral, leia [os métodos programáticos de coordenação das operações de escala do Azure](service-fabric-cluster-scaling.md#programmatic-scaling). 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="manage-credentials"></a>Gerir credenciais
Um dos desafios de escrever um serviço para lidar com o dimensionamento é que o serviço deve ser capaz de aceder a recursos de escala de máquina virtual definidos sem um login interativo. O acesso ao cluster de Tecido de Serviço é fácil se o serviço de escala está a modificar a sua própria aplicação de Tecido de Serviço, mas são necessárias credenciais para aceder ao conjunto de escalas. Para iniciar seduca, pode utilizar um [principal de serviço](/cli/azure/create-an-azure-service-principal-azure-cli) criado com o [Azure CLI](https://github.com/azure/azure-cli).

Um principiante de serviço pode ser criado com os seguintes passos:

1. Inscreva-se no Azure CLI `az login` ( ) como um utilizador com acesso ao conjunto de escala de máquina virtual
2. Criar o principal de serviço com `az ad sp create-for-rbac`
    1. Tome nota do appId (chamado 'ID do cliente' em outro lugar), nome, senha e inquilino para posterior utilização.
    2. Também vai precisar do seu ID de subscrição, que pode ser visto com `az account list`

A biblioteca de computação fluente pode iniciar sação usando estas credenciais da seguinte forma (note que os tipos de Azure fluentes fundamentais como `IAzure` estão no pacote [Microsoft.Azure.Management.Fluent):](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/)

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

Uma vez iniciado o login, a contagem de instâncias definidas em escala pode ser consultada através `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity` de .

## <a name="scaling-out"></a>Aumentar horizontalmente
Utilizando o fluente Azure compute SDK, os casos podem ser adicionados à escala de máquina virtual definida com apenas algumas chamadas -

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

Alternativamente, o tamanho da balança de máquina virtual também pode ser gerido com cmdlets PowerShell. [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) pode recuperar o objeto de escala de máquina virtual. A capacidade atual está disponível através da `.sku.capacity` propriedade. Depois de alterar a capacidade para o valor pretendido, a escala de máquina virtual definida em Azure pode ser atualizada com o [`Update-AzVmss`](/powershell/module/az.compute/update-azvmss) comando.

Como ao adicionar um nó manualmente, adicionar uma instância definida em escala deve ser tudo o que é necessário para iniciar um novo nó de Tecido de Serviço, uma vez que o modelo de conjunto de escala inclui extensões para juntar automaticamente novas instâncias ao cluster de Tecido de Serviço. 

## <a name="scaling-in"></a>Escalando em

A escala é semelhante à escala. As mudanças reais do conjunto de escalas de máquinas virtuais são praticamente as mesmas. Mas, como foi discutido anteriormente, o Service Fabric só limpa automaticamente os nós removidos com uma durabilidade de Ouro ou Prata. Assim, no caso de escala de durabilidade de Bronze, é necessário interagir com o cluster de tecido de serviço para desligar o nó a ser removido e, em seguida, remover o seu estado.

Preparar o nó para o encerramento implica encontrar o nó a ser removido (a instância de conjunto de escala de máquina virtual mais recentemente adicionada) e desativá-lo. As instâncias de conjunto de escala de máquina virtual são numeradas na ordem em que são adicionadas, de modo que os nós mais recentes podem ser encontrados comparando o sufixo de número nos nomes dos nós (que correspondem aos nomes de instâncias de escala de máquina virtual subjacentes). 

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

Uma vez encontrado o nó a remover, pode ser desativado e removido utilizando a mesma `FabricClient` instância e a instância `IAzure` anterior.

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

Tal como com a escala, os cmdlets PowerShell para modificar a capacidade de conjunto de escala de máquina virtual também podem ser usados aqui se uma abordagem de script é preferível. Uma vez removida a instância da máquina virtual, o estado do nó do tecido de serviço pode ser removido.

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="next-steps"></a>Passos seguintes

Para começar a implementar a sua própria lógica de auto-escala, familiarize-se com os seguintes conceitos e APIs úteis:

- [Dimensionamento manual ou com regras de escala automática](./service-fabric-cluster-scale-in-out.md)
- [Fluent Azure Management Libraries for .NET](https://github.com/Azure/azure-sdk-for-net/tree/Fluent) (útil para interagir com os conjuntos de escala de máquinas virtuais subjacentes de um cluster de tecido de serviço)
- [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient) (útil para interagir com um cluster de tecido de serviço e seus nós)

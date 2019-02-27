---
title: O Azure Service Fabric planeamento de capacidade e melhores práticas de dimensionamento | Documentos da Microsoft
description: Melhores práticas para o planejamento e dimensionar aplicações e clusters do Service Fabric.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: f91ea4c4ec887a9f9fe0c15000e3810109caeb96
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889074"
---
# <a name="capacity-planning-and-scaling"></a>Planejamento de capacidade e dimensionamento

Antes de criar qualquer cluster do Azure Service Fabric ou dimensionar os recursos de computação que aloja o seu cluster, é importante planear a capacidade. Para obter mais informações sobre o planeamento de capacidade, consulte [planear a capacidade de cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity). Para além de considerar as características de Cluster e Nodetype, planeie para dimensionar as operações a demorar mais do que uma hora para concluir para um ambiente de produção, independentemente do número de VMs que está a adicionar.

## <a name="auto-scaling"></a>Dimensionamento Automático
Operações de dimensionamento devem ser realizada por meio de implementação do modelo de recurso do Azure, porque é uma prática recomendada de tratar [configurações de recursos como código]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)e usando o conjunto de dimensionamento de Máquina Virtual dimensionamento automático irá resultar em seu contagens de instâncias de definir com versão do modelo do Resource Manager assinalados definição de dimensionamento de máquinas virtuais aumentar o risco de Implantações futuras, fazendo com que as operações de dimensionamento não-intencionais e, em geral deverá usar o dimensionamento automático, se:

* Implementar modelos do Resource Manager com capacidade adequada declarada não suporta o seu caso de utilização.
  * Além de dimensionamento manual, pode configurar uma [integração contínua e o Pipeline de entrega nos serviços de DevOps do Azure com projetos de implantação do grupo de recursos do Azure]( https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts), que normalmente é acionado por uma aplicação lógica que utiliza métricas de desempenho da máquina virtual consultadas a partir [API de REST do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough); efetivamente o dimensionamento automático com base em qualquer métricas desejar, durante a otimização para o Azure Resource Manager de valor agregado.
* Só precisa de dimensionar horizontalmente 1 nó de conjunto de dimensionamento de máquina virtual ao mesmo tempo.
  * Para aumentar horizontalmente por 3 ou mais nós ao mesmo tempo, deve [dimensionar um cluster do Service Fabric out ao adicionar um conjunto de dimensionamento de Máquina Virtual](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out), e é mais segura reduzir horizontalmente e o dimensionamento de máquinas virtuais define horizontalmente 1 nó por vez.
* Tem em confiabilidade Silver ou superior para o seu Cluster do Service Fabric e durabilidade Silver ou superior em qualquer escala conjunto configurar regras de dimensionamento automático.
  * Capacidade de regras de dimensionamento automático [mínima] tem de ser igual ou maior do que 5 instâncias da máquina virtual e tem de ser igual ou maior do que o mínimo de escalão de fiabilidade para o seu tipo de nó primário.

> [!NOTE]
> Recursos de infraestrutura de serviço com estado do Azure Service Fabric: / sistema/InfastructureService/< NODE_TYPE_NAME >, é executado em cada tipo de nó que tem Silver ou proporcionar uma durabilidade elevada, que é o único serviço de sistema, que é suportado para ser executado no Azure em qualquer um dos seus tipos de nós de clusters . 

## <a name="vertical-scaling-considerations"></a>Considerações sobre o dimensionamento vertical

[Dimensionamento vertical](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) um tipo de nó no Azure Service Fabric requer um número de passos e considerações. Por exemplo:
* O cluster tem de estar em bom estado antes de dimensionar. Caso contrário, será apenas desestabilizar ainda mais o cluster.
* **Silver durabilidade de nível ou superior** é necessário para todos os Service Fabric Cluster NodeTypes que alojam serviços com estado.

> [!NOTE]
> O NodeType primário que aloja os serviços de sistema de recursos de infraestrutura de serviço com estado tem de ser Silver durabilidade, nível ou superior. Se ativar a durabilidade silver, operações de cluster, como as atualizações, a adição ou remoção de nós e assim por diante se torne mais lento porque o sistema otimiza para segurança de dados em velocidade das operações.

Vertical de dimensionamento de um conjunto de dimensionamento de Máquina Virtual é uma operação destrutiva. Em vez disso, horizontalmente dimensionar o seu cluster, adicionando um novo conjunto de dimensionamento com o SKU pretendido e migrar os serviços para o SKU pretendido para concluir uma operação de dimensionamento vertical segura. Alterar um SKU de recurso o conjunto de dimensionamento de Máquina Virtual é uma operação destrutiva porque ela recria imagens seus hosts que remove todo o estado persistente localmente.

Service Fabric [propriedades de nó e restrições de posicionamento](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) são usados pelo seu cluster para decidir onde hospedar seus serviços de aplicativos. Quando dimensionar verticalmente o seu tipo de nó principal, declarar os valores de propriedade idênticos para `"nodeTypeRef"`, que se encontra na extensão de recursos de infraestrutura de serviço de definir dimensionamento de Máquina Virtual. O seguinte fragmento de modelo do Resource Manager mostra as propriedades que irá declarar, com o mesmo valor para os seus conjuntos de dimensionamento aprovisionado novo que estão a dimensionar para e só é suportado como uma temporária com monitoração de estado para o seu cluster:

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Não deixe o seu cluster em execução com vários conjuntos de dimensionamento que utilizam o mesmo `nodeTypeRef` mais do que o valor da propriedade necessário para concluir uma operação de dimensionamento vertical com êxito.
> Valide sempre operações em ambientes de teste antes de tentar alterações de ambiente de produção. Por predefinição, os serviços do sistema de Cluster do Service Fabric tem uma restrição de posicionamento para segmentar apenas o nodetype primário.

Com as propriedades de nó e restrições de posicionamento declaradas, efetue a seguinte passos uma instância de VM ao mesmo tempo. Isso permite que os serviços do sistema (e seus serviços com estado) ao encerrar corretamente na instância de VM que está a remover à medida que novos réplicas são criadas noutro local.
1. A partir do PowerShell, execute `Disable-ServiceFabricNode` com a intenção "RemoveNode" para desativar o nó que pretende remover. Remova o tipo de nó que tem o número mais elevado. Por exemplo, se tiver um cluster de seis nós, remova a instância de máquina virtual "MyNodeType_5".
2. Executar `Get-ServiceFabricNode` para se certificar de que o nó foi transferido para desativado. Caso contrário, aguarde até que o nó está desabilitado. Isto pode demorar algumas horas para cada nó. Não continue até que o nó foi transferido para desativado.
3. Diminua o número de VMs por um nesse tipo de nó. A instância VM mais elevada agora será removida.
4. Repita os passos 1 a 3 conforme necessário, mas nunca reduzir verticalmente o número de instâncias, os tipos de nó primário, de menos do que o que justifica o escalão de fiabilidade. Ver [planear a capacidade de cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) para obter uma lista de instâncias recomendadas.

## <a name="horizontal-scaling"></a>Dimensionamento horizontal

Horizontal dimensionamento no Service Fabric pode ser feito um [manualmente](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) ou [programaticamente](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling).

> [!NOTE]
> Se o dimensionamento de um tipo de nó que possui durabilidade silver ou gold, dimensionamento será lenta.

### <a name="scaling-out"></a>Aumentar horizontalmente

Aumentar horizontalmente um cluster do Service Fabric ao aumentar a contagem de instâncias para um determinado conjunto de dimensionamento de Máquina Virtual. Pode aumentar horizontalmente por meio de programação com o AzureClient e o ID para o conjunto para aumentar a capacidade de dimensionamento pretendido.

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Para ampliar manualmente, atualize a capacidade na propriedade SKU do desejada [conjunto de dimensionamento de Máquina Virtual](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) recursos.
```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>Reduzir horizontalmente

Dimensionar em requer consideração mais do que aumentar horizontalmente. Por exemplo:
* Executam serviços de sistema do Service Fabric no tipo de nó principal no seu cluster. Nunca encerrar ou reduzir verticalmente o número de instâncias para esse tipo de nó para que tenha menos instâncias que o que justifica o escalão de fiabilidade. 
* Para um serviço com estado, é necessário um determinado número de nós que estão sempre se manter a disponibilidade e preservar o estado do seu serviço. No mínimo, é necessário um número de nós igual à contagem de conjunto de réplicas de destino do serviço/partição.

Para reduzir horizontalmente manualmente, siga estes passos:

1. A partir do PowerShell, execute `Disable-ServiceFabricNode` com a intenção "RemoveNode" para desativar o nó que pretende remover. Remova o tipo de nó que tem o número mais elevado. Por exemplo, se tiver um cluster de seis nós, remova a instância de máquina virtual "MyNodeType_5".
2. Executar `Get-ServiceFabricNode` para se certificar de que o nó foi transferido para desativado. Caso contrário, aguarde até que o nó está desabilitado. Isto pode demorar algumas horas para cada nó. Não continue até que o nó foi transferido para desativado.
3. Diminua o número de VMs por um nesse tipo de nó. A instância VM mais elevada agora será removida.
4. Repita os passos 1 a 3 conforme necessário, mas nunca reduzir verticalmente o número de instâncias, os tipos de nó primário, de menos do que o que justifica o escalão de fiabilidade. Ver [planear a capacidade de cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) para obter uma lista de instâncias recomendadas.

Para reduzir horizontalmente manualmente, atualize a capacidade na propriedade SKU do desejada [conjunto de dimensionamento de Máquina Virtual](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) recursos.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

1. Repita os passos 1 a 3 até que Aprovisione a capacidade de que pretende. Não reduza verticalmente o número de instâncias, os tipos de nó primário para o menor do que o que justifica o escalão de fiabilidade. Para obter detalhes sobre camadas de confiabilidade e o número de instâncias que necessário, consulte a [planear a capacidade de cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Tem de preparar o nó de encerramento para reduzir horizontalmente por meio de programação. Isso envolve localizar o nó ser removido que é o nó mais alto de instância e desativar o-lo. Por exemplo:

```c#
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

Depois de identificar o nó para remover, desativar e removê-lo com o mesmo `FabricClient` instância (`client` neste caso) e o nome de instância de nó (`instanceIdString` neste caso) utilizado no código acima:

```c#
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

## <a name="reliability-levels"></a>Níveis de confiabilidade

O [nível de fiabilidade](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) é uma propriedade do seu recurso de Cluster do Service Fabric e não pode ser configurado de forma diferente para nodeTypes individuais. Ele controla o fator de replicação de serviços do sistema para o cluster e é uma definição ao nível do recurso de cluster. O nível de fiabilidade determinará o número mínimo de nós que seu tipo de nó primário tem de ter. O escalão de fiabilidade pode realizar os seguintes valores:
* Platinum - executa os serviços do sistema com uma contagem de conjunto de réplicas de destino de nós de semente de sete e nove.
* Ouro - executa os serviços do sistema com uma contagem de conjunto de réplicas de destino de sete e sete nós de semente.
* Silver - executa os serviços do sistema com uma contagem de conjunto de réplicas de destino de nós de semente de cinco e cinco.
* Bronze - executa os serviços do sistema com uma contagem de conjunto de réplicas de destino de três e três nós de semente.

O nível de fiabilidade recomendada mínimo é Silver.

O nível de confiabilidade é definido na secção de propriedades do [Microsoft.ServiceFabric/clusters recursos](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters), assim:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Níveis de durabilidade

> [!WARNING]
> Obter tipos de nós em execução com durabilidade de Bronze _sem privilégios_. Isso significa que as tarefas de infraestrutura que afetam as cargas de trabalho sem monitorização de estado serão não ser paradas ou atrasadas, que podem afetar as suas cargas de trabalho. Utilize a durabilidade de Bronze apenas para tipos de nós que executam cargas de trabalho sem monitorização de estado. Para cargas de trabalho de produção, execute Silver ou superior para garantir a consistência de estado. Escolha a confiabilidade certa com base na documentação de orientação na [documentação de planeamento de capacidade](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

O nível de durabilidade deve ser definido em dois recursos. O perfil de extensão do [recurso de conjunto de dimensionamento de Máquina Virtual](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

```json
"extensionProfile": {
    "extensions":          {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
        "properties": {
            "settings": {
                "durabilityLevel": "Bronze"
            }
        }
    }
}
```

E, em `nodeTypes` no [Microsoft.ServiceFabric/clusters recursos](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters) 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Passos Seguintes

* Crie um cluster em VMs ou computadores que executam o Windows Server: [Criação do cluster do Service Fabric para o Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Crie um cluster em VMs ou computadores que executam o Linux: [Criar um cluster do Linux](service-fabric-cluster-creation-via-portal.md)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png

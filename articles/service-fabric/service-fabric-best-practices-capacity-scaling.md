---
title: Planeamento e dimensionamento de capacidades para o tecido de serviço Azure
description: Melhores práticas para planeamento e dimensionamento de clusters e aplicações de tecido de serviço.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.custom: devx-track-csharp
ms.openlocfilehash: 96cd460ddfea863eb27a1087ff59f3b87acf65d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90531309"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Planeamento e dimensionamento de capacidades para o tecido de serviço Azure

Antes de criar qualquer cluster de tecido de serviço Azure ou recursos de computação em escala que acolhem o seu cluster, é importante planear a capacidade. Para obter mais informações sobre o planeamento da capacidade, consulte [o Planeamento da capacidade de cluster do Tecido de Serviço.](./service-fabric-cluster-capacity.md) Para obter mais orientações sobre as melhores práticas para a escalabilidade do cluster, consulte considerações de [escalabilidade do tecido de serviço](/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations).

Além de considerar o tipo de nó e as características do cluster, deve esperar que as operações de escala levem mais de uma hora para completar para um ambiente de produção. Esta consideração é verdadeira independentemente do número de VMs que está a adicionar.

## <a name="autoscaling"></a>Dimensionamento automático
Deve realizar operações de escala através de modelos Azure Resource Manager, porque é a melhor prática para tratar [as configurações](./service-fabric-best-practices-infrastructure-as-code.md)de recursos como código . 

A utilização de escalas automáticas através de conjuntos de escala de máquinas virtuais fará com que o seu modelo de Gestor de Recursos em versão defina incorretamente as suas contagens de exemplo para conjuntos de escala de máquina virtual. A definição imprecisa aumenta o risco de futuras implementações causarem operações de escala não intencionais. Em geral, deve utilizar auto-cálculo se:

* A implementação dos modelos do Gestor de Recursos com a capacidade adequada declarada não suporta o seu caso de utilização.
     
   Além da escala manual, pode configurar um [gasoduto de integração e entrega contínua nos Serviços Azure DevOps utilizando projetos de implementação de grupos de recursos Azure.](../azure-resource-manager/templates/add-template-to-azure-pipelines.md) Este gasoduto é normalmente desencadeado por uma aplicação lógica que utiliza métricas de desempenho da máquina virtual questionadas a partir da [API do Monitor Azure.](../azure-monitor/platform/rest-api-walkthrough.md) O pipeline efetivamente autoescala com base em todas as métricas que você deseja, ao mesmo tempo que otimiza para modelos de Gestor de Recursos.
* É necessário escalar horizontalmente apenas um nó de balança de máquina virtual de cada vez.
   
   Para escalar por três ou mais nós de cada vez, deverá [escalar um cluster de Tecido de Serviço adicionando um conjunto de escala de máquina virtual](virtual-machine-scale-set-scale-node-type-scale-out.md). É mais seguro escalar e escalar conjuntos de escala de máquina virtual horizontalmente, um nó de cada vez.
* Tem fiabilidade prateada ou superior para o seu cluster de Tecido de Serviço, e durabilidade prateada ou superior em qualquer escala em que configurar regras de autoscalagem.
  
   A capacidade mínima para as regras de autoscalagem deve ser igual ou superior a cinco instâncias de máquinas virtuais. Também deve ser igual ou superior ao seu nível de fiabilidade mínimo para o seu tipo de nó primário.

> [!NOTE]
> O tecido de serviço stateful Do Tecido de Serviço:/System/InfastructureService/<NODE_TYPE_NAME> funciona em todos os tipos de nó que têm prata ou durabilidade superior. É o único serviço de sistema que é suportado para funcionar em Azure em qualquer um dos seus tipos de nó clusters.

> [!IMPORTANT]
> Suportes de autoescalagem de tecido de serviço `Default` e `NewestVM` [configurações de escala de escala de](../virtual-machine-scale-sets/virtual-machine-scale-sets-scale-in-policy.md)máquina virtual .

## <a name="vertical-scaling-considerations"></a>Considerações de escala vertical

[A escala vertical de](./virtual-machine-scale-set-scale-node-type-scale-out.md) um nó no tecido de serviço Azure requer uma série de passos e considerações. Por exemplo:

* O aglomerado deve estar saudável antes de escalonar. Caso contrário, desestabilizará ainda mais o aglomerado.
* O nível de durabilidade prateada ou superior é necessário para todos os tipos de nó de cluster de tecido de serviço que acolhem serviços estatais.

> [!NOTE]
> O seu tipo de nó primário que acolhe serviços de sistema stateful Service Fabric deve ser o nível de durabilidade prateada ou maior. Depois de ativar a durabilidade da Prata, as operações de cluster, tais como upgrades, adição ou remoção de nós, e assim por diante serão mais lentas porque o sistema otimiza para a segurança de dados em relação à velocidade das operações.

Escalar verticalmente um conjunto de escala de máquina virtual é uma operação destrutiva. Em vez disso, dimensione horizontalmente o seu cluster adicionando um conjunto de escala nova com o SKU desejado. Em seguida, emigre os seus serviços para o seu SKU desejado para completar uma operação de escala vertical segura. Alterar um recurso de conjunto de escala de máquina virtual SKU é uma operação destrutiva porque reimagema os seus anfitriões, que remove todo o estado persistido localmente.

O seu cluster utiliza propriedades de nó de nó de tecido de serviço [e restrições de colocação](./service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints) para decidir onde hospedar os serviços da sua aplicação. Quando estiver a escalonar verticalmente o seu tipo de nó primário, declare valores de propriedade idênticos para `"nodeTypeRef"` . Pode encontrar estes valores na extensão do Tecido de Serviço para conjuntos de escala de máquina virtual. 

O seguinte corte de um modelo de Gestor de Recursos mostra as propriedades que vai declarar. Tem o mesmo valor para os conjuntos de escala recém-a provisionados a que está a escalonar, e é suportado apenas como um serviço temporário estatal para o seu cluster.

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Não deixe o seu cluster em funcionamento com conjuntos de várias escalas que usam o mesmo `nodeTypeRef` valor de propriedade por mais tempo do que o necessário para completar uma operação de escala vertical bem sucedida.
>
> Valide sempre as operações em ambientes de teste antes de tentar alterações no ambiente de produção. Por predefinição, os serviços de sistema de cluster de tecido de serviço têm uma restrição de colocação apenas para o tipo de nó primário alvo.

Com as propriedades do nó e os constrangimentos de colocação declarados, faça os seguintes passos uma instância VM de cada vez. Isto permite que os serviços de sistema (e os seus serviços imponentes) sejam encerrados graciosamente no caso VM que está a remover à medida que novas réplicas são criadas em outros lugares.

1. Da PowerShell, corra `Disable-ServiceFabricNode` com `RemoveNode` intenção de desativar o nó que vai remover. Retire o tipo de nó que tem o número mais alto. Por exemplo, se tiver um cluster de seis nós, remova a caixa da máquina virtual "MyNodeType_5".
2. Corra `Get-ServiceFabricNode` para se certificar de que o nó passou para desativado. Caso contrário, aguarde até que o nó esteja desativado. Isto pode levar algumas horas para cada nó. Não prossiga até que o nó tenha transitado para deficiente.
3. Diminua o número de VMs por um nesse tipo de nó. A maior instância de VM será agora removida.
4. Repita os passos 1 a 3 conforme necessário, mas nunca dimensione o número de casos nos tipos de nó primário inferiores ao que o nível de fiabilidade garante. Consulte [a capacidade de planeamento do cluster de tecido de serviço](./service-fabric-cluster-capacity.md) para uma lista de instâncias recomendadas.
5. Uma vez que todos os VMs se foram (representados como "Down") o tecido:/System/InfrastructureService/[nome do nó] mostrará um estado de Erro. Em seguida, pode atualizar o recurso de cluster para remover o tipo de nó. Pode utilizar a implementação do modelo ARM ou editar o recurso de cluster através do [gestor de recursos Azure](https://resources.azure.com). Isto irá iniciar uma atualização de cluster que removerá o serviço de tecido:/System/InfrastructureService/[nó tipo] que está em estado de erro.
 6. Depois disso, pode eliminar opcionalmente o VMScaleSet, mas ainda verá os nós como "Down" da vista do Explorador de Tecidos de Serviço. O último passo seria limpá-los com `Remove-ServiceFabricNodeState` o comando.

## <a name="horizontal-scaling"></a>Dimensionamento horizontal

Pode fazer escala horizontal [manual](./service-fabric-cluster-scale-in-out.md) ou [programática.](./service-fabric-cluster-programmatic-scaling.md)

> [!NOTE]
> Se estiver a escalonar um nó que tenha durabilidade prateada ou dourada, a escala será lenta.

### <a name="scaling-out"></a>Aumentar horizontalmente

Dimensione um cluster de tecido de serviço aumentando a contagem de exemplos para um determinado conjunto de escala de máquina virtual. Pode escalar programáticamente usando `AzureClient` e o ID para a escala desejada definida para aumentar a capacidade.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Para escalar manualmente, atualize a capacidade na propriedade SKU do recurso [de conjunto de escala de máquina virtual](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) pretendido.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>Escalando em

A escala requer mais consideração do que a escalonamento. Por exemplo:

* Os serviços de sistema de tecido de serviço funcionam no tipo de nó primário no seu cluster. Nunca desligue ou escalone no número de casos para esse tipo de nó, de modo a que tenha menos instâncias do que o que o nível de fiabilidade garante. 
* Para um serviço estatal, você precisa de um certo número de nós que estão sempre até para manter a disponibilidade e preservar o estado do seu serviço. No mínimo, precisa de um número de nós iguais à contagem definida de réplica do alvo da partição ou serviço.

Para escalar manualmente, siga estes passos:

1. Da PowerShell, corra `Disable-ServiceFabricNode` com `RemoveNode` intenção de desativar o nó que vai remover. Retire o tipo de nó que tem o número mais alto. Por exemplo, se tiver um cluster de seis nós, remova a caixa da máquina virtual "MyNodeType_5".
2. Corra `Get-ServiceFabricNode` para se certificar de que o nó passou para desativado. Caso contrário, aguarde até que o nó esteja desativado. Isto pode levar algumas horas para cada nó. Não prossiga até que o nó tenha transitado para deficiente.
3. Diminua o número de VMs por um nesse tipo de nó. A maior instância de VM será agora removida.
4. Repita os passos 1 a 3, se necessário, até fornecer a capacidade desejada. Não dimensione o número de casos nos tipos de nó primário para menos do que o nível de fiabilidade garante. Consulte [a capacidade de planeamento do cluster de tecido de serviço](./service-fabric-cluster-capacity.md) para uma lista de instâncias recomendadas.

Para escalar manualmente, atualize a capacidade na propriedade SKU do recurso [de conjunto de escala de máquina virtual](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) pretendido.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

Deve preparar o nó para o encerramento para escalar programáticamente. Encontre o nó a ser removido (o nó de instância mais alta). Por exemplo:

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

Desativar e remover o nó utilizando o mesmo `FabricClient` caso `client` (neste caso) e a instância do nó `instanceIdString` (neste caso) que utilizou no código anterior:

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shut down
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement virtual machine scale set capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> Quando escala num cluster, verá a instância nó/VM removida exibida num estado pouco saudável no Service Fabric Explorer. Para obter uma explicação deste comportamento, consulte [Comportamentos que possa observar no Service Fabric Explorer](./service-fabric-cluster-scale-in-out.md#behaviors-you-may-observe-in-service-fabric-explorer). Pode:
> * Ligue para o [comando Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps&preserve-view=true) com o nome de nó apropriado.
> * Implemente a [aplicação de ajudante de escala automática do Tecido de Serviço](https://github.com/Azure/service-fabric-autoscale-helper/) no seu cluster. Esta aplicação garante que os nós de escala são limpos do Service Fabric Explorer.

## <a name="reliability-levels"></a>Níveis de fiabilidade

O [nível de fiabilidade](./service-fabric-cluster-capacity.md) é uma propriedade do seu recurso cluster De Tecido de Serviço. Não pode ser configurado de forma diferente para os tipos individuais de nós. Controla o fator de replicação dos serviços de sistema para o cluster, e é uma definição ao nível do recurso cluster. 

O nível de fiabilidade determinará o número mínimo de nós que o seu nó primário deve ter. O nível de fiabilidade pode assumir os seguintes valores:

* Platinum: Executa os serviços do sistema com uma contagem de réplicas de sete e nove nós de sementes.
* Ouro: Executa os serviços do sistema com uma réplica de sete e sete nós de sementes.
* Prata: Executa os serviços do sistema com uma réplica de cinco e cinco nós de sementes.
* Bronze: Executa os serviços do sistema com uma contagem de réplicas de três e três nós de sementes.

O nível mínimo de fiabilidade recomendado é prata.

O nível de fiabilidade é definido na secção de propriedades do [recurso Microsoft.ServiceFabric/clusters,](/azure/templates/microsoft.servicefabric/2018-02-01/clusters)como este:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Níveis de durabilidade

> [!WARNING]
> Os tipos de nó que correm com durabilidade de Bronze não obtêm _privilégios_. Os trabalhos de infraestrutura que afetam as suas cargas de trabalho apátridas não serão interrompidos ou atrasados, o que poderá afetar as suas cargas de trabalho. 
>
> Utilize durabilidade de Bronze apenas para tipos de nó que executam cargas de trabalho apátridas. Para cargas de trabalho de produção, corra prata ou superior para garantir a consistência do estado. Escolha a fiabilidade certa com base na orientação na documentação de [planeamento de capacidade.](./service-fabric-cluster-capacity.md)

O nível de durabilidade deve ser fixado em dois recursos. Um deles é o perfil de extensão do [recurso conjunto de escala de máquina virtual:](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)

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

O outro recurso está no `nodeTypes` [recurso Microsoft.ServiceFabric/clusters](/azure/templates/microsoft.servicefabric/2018-02-01/clusters): 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Passos seguintes

* Crie um cluster em VMs ou computadores que executam o Windows Server: [Criação de cluster de tecido de serviço para o Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Crie um cluster em VMs ou computadores que executam o Linux: [Crie um cluster Linux](service-fabric-cluster-creation-via-portal.md).
* Saiba mais sobre [as opções de suporte do Tecido de Serviço.](service-fabric-support.md)

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
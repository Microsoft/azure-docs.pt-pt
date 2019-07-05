---
title: Planeamento de capacidade e dimensionamento para o Azure Service Fabric | Documentos da Microsoft
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
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 8ba4763e8d4835911d33d21c0f5bb431851a649b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444723"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Planejamento de capacidade e dimensionamento para o Azure Service Fabric

Antes de criar qualquer cluster do Azure Service Fabric ou dimensionar recursos que alojam o seu cluster de computação, é importante planear a capacidade. Para obter mais informações sobre o planeamento de capacidade, consulte [planear a capacidade de cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity). Para mais diretrizes de práticas recomendadas para a escalabilidade do cluster, consulte [considerações de escalabilidade do Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations).

Para além de considerar as características de tipo e o cluster de nó, deve esperar que operações de dimensionamento a demorar mais do que uma hora para concluir para um ambiente de produção. Isso é verdade não importando o número de VMs que está a adicionar.

## <a name="autoscaling"></a>Dimensionamento automático
Deve realizar operações de dimensionamento através de modelos do Azure Resource Manager, porque é a melhor prática para tratar [configurações de recursos como código]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code). 

Utilizar o dimensionamento automático através de conjuntos de dimensionamento de máquinas virtuais fará com que o modelo do Resource Manager com a versão assinalados definir sua contagens de instâncias de conjuntos de dimensionamento de máquina virtual. Definição incorreta aumenta o risco de que as futuras Implantações fará com que as operações de dimensionamento não-intencionais. Em geral, deve usar o dimensionamento automático se:

* Implementar modelos do Resource Manager com capacidade adequada declarada não suporta o seu caso de utilização.
     
   Além de dimensionamento manual, pode configurar uma [pipeline de integração e entrega contínua nos serviços de DevOps do Azure ao utilizar projetos de implantação de grupo de recursos do Azure](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts). Este pipeline normalmente é acionado por uma aplicação lógica que utiliza as métricas de desempenho da máquina virtual consultadas a partir da [API de REST do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough). O pipeline com eficiência é dimensionado automaticamente com base em qualquer métricas desejar, durante a otimização para modelos do Resource Manager.
* Precisa Dimensionar horizontalmente apenas um nó de conjunto de dimensionamento de máquina virtual ao mesmo tempo.
   
   Para aumentar horizontalmente por três ou mais nós ao mesmo tempo, deve [aumentar horizontalmente um cluster do Service Fabric através da adição de um conjunto de dimensionamento de máquinas virtuais](virtual-machine-scale-set-scale-node-type-scale-out.md). É mais seguro reduzir horizontalmente e aumentar horizontalmente o dimensionamento de máquinas virtuais horizontalmente, define um nó por vez.
* Tem a confiabilidade Silver ou superior para o seu cluster do Service Fabric e durabilidade Silver ou superior em qualquer escala onde configurar regras de dimensionamento automático.
  
   A capacidade mínima para regras de dimensionamento automático tem de ser igual ou maior do que cinco instâncias de máquina virtual. Também tem de ser igual ou maior do que o mínimo de escalão de fiabilidade para o seu tipo de nó primário.

> [!NOTE]
> O Service Fabric com monitorização de estado do service fabric: / sistema/InfastructureService/< NODE_TYPE_NAME > é executado em cada tipo de nó que tem uma durabilidade Silver ou superior. É o serviço de sistema apenas é suportado para ser executado no Azure em qualquer um dos seus tipos de nó de clusters.

## <a name="vertical-scaling-considerations"></a>Considerações sobre o dimensionamento vertical

[Dimensionamento vertical](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) um tipo de nó no Azure Service Fabric requer um número de passos e considerações. Por exemplo:

* O cluster tem de estar em bom estado antes de dimensionar. Caso contrário, irá desestabilizar ainda mais o cluster.
* A durabilidade Silver nível ou superior é obrigatória para todos os recursos de infraestrutura do serviço cluster tipos de nós que alojam serviços com estado.

> [!NOTE]
> O tipo de nó principal que aloja os serviços de sistema do Service Fabric com monitorização de estado tem de ser Silver durabilidade, nível ou superior. Depois de ativar a durabilidade Silver, operações de cluster, como as atualizações, a adição ou remoção de nós e assim por diante se torne mais lento porque o sistema otimiza para segurança de dados em velocidade das operações.

Um conjunto de dimensionamento de máquinas virtuais de dimensionamento vertical é uma operação destrutiva. Em vez disso, dimensione horizontalmente o cluster ao adicionar um novo conjunto de dimensionamento com o SKU pretendido. Em seguida, migre os serviços para o SKU pretendido para concluir uma operação de dimensionamento vertical segura. Alterar um recurso de conjunto de dimensionamento de máquina virtual SKU é uma operação destrutiva porque ela recria imagens seus anfitriões, que remove todos os localmente persistente de estado.

Utiliza o seu cluster do Service Fabric [propriedades de nó e restrições de posicionamento](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) decidir onde hospedar serviços da sua aplicação. Quando está a dimensionar verticalmente seu tipo de nó principal, declarar os valores de propriedade idênticos para `"nodeTypeRef"`. Pode encontrar estes valores na extensão do Service Fabric para conjuntos de dimensionamento de máquina virtual. 

O seguinte fragmento de um modelo do Resource Manager mostra as propriedades que declarar. Ele tem o mesmo valor para os conjuntos de dimensionamento recentemente aprovisionadas que está a dimensionar para, e é suportado apenas como um serviço com estado temporário para o seu cluster.

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Não deixe o seu cluster em execução com vários conjuntos de dimensionamento que utilizam o mesmo `nodeTypeRef` mais do que o valor da propriedade necessário para concluir uma operação de dimensionamento vertical com êxito.
>
> Valide sempre operações em ambientes de teste antes de tentar alterações ao ambiente de produção. Por predefinição, os serviços de sistema de cluster do Service Fabric tem uma restrição de posicionamento para apenas o tipo de nó primário de destino.

Com as propriedades de nó e restrições de posicionamento declaradas, efetue a seguinte passos uma instância de VM ao mesmo tempo. Isso permite que os serviços do sistema (e seus serviços com estado) ao encerrar corretamente na instância de VM que está a remover à medida que novos réplicas são criadas noutro local.

1. A partir do PowerShell, execute `Disable-ServiceFabricNode` com a intenção `RemoveNode` para desativar o nó que pretende remover. Remova o tipo de nó que tem o número mais elevado. Por exemplo, se tiver um cluster de seis nós, remova a instância de máquina virtual "MyNodeType_5".
2. Executar `Get-ServiceFabricNode` para se certificar de que o nó foi transferido para desativado. Caso contrário, aguarde até que o nó está desabilitado. Isto poderá demorar algumas horas para cada nó. Não continue até que o nó foi transferido para desativado.
3. Diminua o número de VMs por um nesse tipo de nó. A instância VM mais elevada agora será removida.
4. Repita os passos 1 a 3 conforme necessário, mas nunca reduzir verticalmente o número de instâncias, os tipos de nó primário, de menos do que o que justifica o escalão de fiabilidade. Ver [planear a capacidade de cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) para obter uma lista de instâncias recomendadas.
5. Depois de todas as VMs foram removidas (representado como "Down") do fabric: / sistema/InfrastructureService / [nome do nó] mostrará um Estado de erro. Em seguida, pode atualizar o recurso de cluster para remover o tipo de nó. Pode utilizar a implementação do modelo ARM, ou editar o recurso de cluster através da [do Azure resource manager](https://resources.azure.com). Isto irá iniciar uma atualização de cluster que removerá o fabric: / / InfrastructureService / [tipo de nó] serviço do sistema que está no Estado com erros.
 6. Depois que pode, opcionalmente, elimine o VMScaleSet, ainda verá os nós como "Down" no Explorador de recursos de infraestrutura do serviço de ver no entanto. A última etapa seria limpá-los com `Remove-ServiceFabricNodeState` comando.

### <a name="example-scenario"></a>Cenário de exemplo
É um cenário suportado para quando efetuar uma operação de dimensionamento vertical: que pretende migrar o cluster do Service Fabric e a aplicação a partir de um disco não gerido para os managed disks, sem períodos de indisponibilidade de aplicação. 

Pode aprovisionar um novo conjunto de dimensionamento de máquina virtual com discos geridos e efetuar uma atualização de aplicação com restrições de posicionamento destinados a capacidade aprovisionada. Cluster do Service Fabric, em seguida, pode agendar a sua carga de trabalho na capacidade de nó de cluster aprovisionado que é implementada por domínio de atualização sem períodos de indisponibilidade de aplicação. 

Pontos finais de conjunto de back-end para o [SKU básico do Balanceador de carga do Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) podem ser máquinas virtuais num único conjunto de disponibilidade ou um conjunto de dimensionamento de máquina virtual. Isso significa que não é possível utilizar um balanceador de carga de SKU básico se mover a sua aplicação de sistemas do Service Fabric entre conjuntos de dimensionamento, sem causar inaccessibility temporário do ponto final de gestão do cluster do Service Fabric. Isso é verdade apesar do cluster e de seu aplicativo ainda estão em execução.

Os utilizadores normalmente aprovisionar um balanceador de carga de SKU padrão quando efetuar uma alternância de endereço (VIP) de IP virtual entre o Balanceador de carga de SKU básico e recursos do Balanceador de carga de SKU padrão. Essa técnica limita qualquer inaccessibility futura para cerca de 30 segundos necessário para a troca de VIP.

## <a name="horizontal-scaling"></a>Dimensionamento horizontal

Pode fazer o dimensionamento horizontal seja [manualmente](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) ou [programaticamente](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling).

> [!NOTE]
> Se está a dimensionar um tipo de nó que possui a durabilidade de competência Silver ou Gold, dimensionamento será lenta.

### <a name="scaling-out"></a>Aumentar horizontalmente

Aumentar horizontalmente um cluster do Service Fabric ao aumentar a contagem de instâncias para um conjunto de dimensionamento de máquina virtual específica. Pode aumentar horizontalmente por meio de programação utilizando `AzureClient` e o ID para o conjunto para aumentar a capacidade de dimensionamento pretendido.

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Para ampliar manualmente, atualize a capacidade na propriedade SKU do desejada [conjunto de dimensionamento de máquina virtual](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) recursos.

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
* Para um serviço com estado, terá de um determinado número de nós que estão sempre se manter a disponibilidade e preservar o estado do seu serviço. No mínimo, é necessário um número de nós igual à contagem de conjunto de réplicas de destino da partição ou serviço.

Para reduzir horizontalmente manualmente, siga estes passos:

1. A partir do PowerShell, execute `Disable-ServiceFabricNode` com a intenção `RemoveNode` para desativar o nó que pretende remover. Remova o tipo de nó que tem o número mais elevado. Por exemplo, se tiver um cluster de seis nós, remova a instância de máquina virtual "MyNodeType_5".
2. Executar `Get-ServiceFabricNode` para se certificar de que o nó foi transferido para desativado. Caso contrário, aguarde até que o nó está desabilitado. Isto poderá demorar algumas horas para cada nó. Não continue até que o nó foi transferido para desativado.
3. Diminua o número de VMs por um nesse tipo de nó. A instância VM mais elevada agora será removida.
4. Repita os passos 1 a 3 conforme necessário até que Aprovisione a capacidade de que pretende. Não reduza verticalmente o número de instâncias, os tipos de nó primário para o menor do que o que justifica o escalão de fiabilidade. Ver [planear a capacidade de cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) para obter uma lista de instâncias recomendadas.

Para reduzir horizontalmente manualmente, atualize a capacidade na propriedade SKU do desejada [conjunto de dimensionamento de máquina virtual](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) recursos.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

Tem de preparar o nó de encerramento reduzir horizontalmente por meio de programação. Localize o nó ser removido (o nó mais alto instância). Por exemplo:

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

Desativar e remover o nó com o mesmo `FabricClient` instância (`client` neste caso) e a instância de nó (`instanceIdString` neste caso) que utilizou no código anterior:

```c#
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
> Quando reduzir verticalmente um cluster, verá a instância VM/nó removido apresentada em mau estado de funcionamento no Service Fabric Explorer. Para obter uma explicação desse comportamento, consulte [comportamentos, pode observar no Service Fabric Explorer](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer). Pode:
> * Chamar o [comando Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) com o nome de nó adequado.
> * Implementar o [aplicação de programa auxiliar de dimensionamento automático do Service Fabric](https://github.com/Azure/service-fabric-autoscale-helper/) no seu cluster. Esse aplicativo garante que os nós de diminuída são limpas do Service Fabric Explorer.

## <a name="reliability-levels"></a>Níveis de confiabilidade

O [nível de fiabilidade](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) é uma propriedade do seu recurso de cluster do Service Fabric. Não é possível configurar diferentes para tipos de nós individuais. Ele controla o fator de replicação de serviços do sistema para o cluster e é uma definição ao nível do recurso de cluster. 

O nível de fiabilidade determinará o número mínimo de nós que seu tipo de nó primário tem de ter. O escalão de fiabilidade pode realizar os seguintes valores:

* Platina: Execute os serviços do sistema com uma contagem de conjunto de réplica de destino de nós de semente de sete e nove.
* Ouro: Execute os serviços do sistema com uma contagem de conjunto de réplicas de destino de sete e sete nós de semente.
* Prata: Execute os serviços do sistema com uma contagem de conjunto de réplicas de destino de cinco e cinco nós de semente.
* Bronze: Execute os serviços do sistema com uma contagem de conjunto de réplicas de destino de três e três nós de semente.

O nível de fiabilidade recomendada mínimo é Silver.

O nível de confiabilidade é definido na secção de propriedades do [Microsoft.ServiceFabric/clusters recursos](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters), assim:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Níveis de durabilidade

> [!WARNING]
> Obter tipos de nós em execução com durabilidade de Bronze _sem privilégios_. Tarefas de infraestrutura que afetam as cargas de trabalho sem monitorização de estado serão não ser paradas ou atrasadas, que podem afetar as suas cargas de trabalho. 
>
> Utilize a durabilidade de Bronze apenas para tipos de nós que executam cargas de trabalho sem monitorização de estado. Para cargas de trabalho de produção, execute Silver ou superior para garantir a consistência de estado. Escolha a confiabilidade certa com base na documentação de orientação na [documentação de planeamento de capacidade](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

O nível de durabilidade deve ser definido em dois recursos. Um é o perfil de extensão do [recurso de conjunto de dimensionamento de máquina virtual](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

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

O outro recurso está sob `nodeTypes` no [Microsoft.ServiceFabric/clusters recursos](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters): 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Passos Seguintes

* Crie um cluster em VMs ou computadores que executam o Windows Server: [Criação do cluster do Service Fabric para o Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Crie um cluster em VMs ou computadores que executam o Linux: [Criar um cluster do Linux](service-fabric-cluster-creation-via-portal.md).
* Saiba mais sobre [opções de suporte do Service Fabric](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png

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
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 9bddb6552b11dd506ee3e2c1c416c15da11048b7
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258741"
---
# <a name="capacity-planning-and-scaling"></a>Planejamento de capacidade e dimensionamento

Antes de criar qualquer cluster do Azure Service Fabric ou dimensionar os recursos de computação que aloja o seu cluster, é importante planear a capacidade. Para obter mais informações sobre o planeamento de capacidade, consulte [planear a capacidade de cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity). Para ainda mais a melhor prática orientações para a escalabilidade do cluster consulte [considerações de escalabilidade do Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations)

Além de considerar as características de tipo e o cluster de nó, deve planear de dimensionamento de operações para a demorar mais do que uma hora para concluir para um ambiente de produção, independentemente do número de VMs que está a adicionar.

## <a name="auto-scaling"></a>Dimensionamento Automático
Operações de dimensionamento devem ser realizada por meio de implementação do modelo de recurso do Azure, porque é a melhor prática para tratar [configurações de recursos como código]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)e o dimensionamento automático irá resultar num conjunto de dimensionamento de máquina virtual a utilizar o contagens de instâncias de definir com versão do modelo do Resource Manager assinalados definição de dimensionamento de máquinas virtuais aumentar o risco de Implantações futuras, fazendo com que as operações de dimensionamento não-intencionais e, em geral deverá usar o dimensionamento automático, se:

* Implementar modelos do Resource Manager com capacidade adequada declarada não suporta o seu caso de utilização.
  * Além de dimensionamento manual, pode configurar uma [integração contínua e o Pipeline de entrega nos serviços de DevOps do Azure com projetos de implantação do grupo de recursos do Azure](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts), que normalmente é acionado por uma aplicação lógica que utiliza métricas de desempenho da máquina virtual consultadas a partir [API de REST do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough); efetivamente dimensionamento automático com base em qualquer métricas que desejar, durante a otimização para adição de valor do Azure Resource Manager.
* Só precisa de dimensionar horizontalmente 1 nó de conjunto de dimensionamento de máquina virtual ao mesmo tempo.
  * Para aumentar horizontalmente por 3 ou mais nós ao mesmo tempo, deve [dimensionar um cluster do Service Fabric para fora, adicionando um conjunto de dimensionamento de máquinas virtuais](https://docs.microsoft.com/azure service-fabric/virtual-machine-scale-set-scale-node-type-scale-out)e é mais seguro reduzir horizontalmente e máquina virtual de conjuntos de dimensionamento horizontal 1 nó cada vez.
* Tem em confiabilidade Silver ou superior para o seu Cluster do Service Fabric e durabilidade Silver ou superior em qualquer escala conjunto configurar regras de dimensionamento automático.
  * Capacidade de regras de dimensionamento automático (mínima) tem de ser igual ou maior do que 5 instâncias da máquina virtual e tem de ser igual ou maior do que o mínimo de escalão de fiabilidade para o seu tipo de nó primário.

> [!NOTE]
> Recursos de infraestrutura de serviço com estado do Azure Service Fabric: / sistema/InfastructureService/< NODE_TYPE_NAME >, é executado em cada tipo de nó que tem Silver ou proporcionar uma durabilidade elevada, que é o único serviço de sistema, que é suportado para ser executado no Azure em qualquer um dos seus tipos de nó de clusters .

## <a name="vertical-scaling-considerations"></a>Considerações sobre o dimensionamento vertical

[Dimensionamento vertical](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) um tipo de nó no Azure Service Fabric requer um número de passos e considerações. Por exemplo:

* O cluster tem de estar em bom estado antes de dimensionar. Caso contrário, será apenas desestabilizar ainda mais o cluster.
* **Silver durabilidade de nível ou superior** é necessária para todos os tipos de nó de Cluster do Service Fabric que alojam serviços com estado.

> [!NOTE]
> O tipo de nó principal que aloja os serviços de sistema de recursos de infraestrutura de serviço com estado tem de ser Silver durabilidade, nível ou superior. Se ativar a durabilidade silver, operações de cluster, como as atualizações, a adição ou remoção de nós e assim por diante se torne mais lento porque o sistema otimiza para segurança de dados em velocidade das operações.

Um conjunto de dimensionamento de máquinas virtuais de dimensionamento vertical é uma operação destrutiva. Em vez disso, horizontalmente dimensionar o seu cluster, adicionando um novo conjunto de dimensionamento com o SKU pretendido e migrar os serviços para o SKU pretendido para concluir uma operação de dimensionamento vertical segura. Alterar um recurso de conjunto de dimensionamento de máquina virtual SKU é uma operação destrutiva porque ele cria uma nova imagem seus hosts que remove todo o estado persistente localmente.

Service Fabric [propriedades de nó e restrições de posicionamento](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) são usados pelo seu cluster para decidir onde hospedar seus serviços de aplicativos. Quando dimensionar verticalmente o seu tipo de nó principal, declarar os valores de propriedade idênticos para `"nodeTypeRef"`, que se encontra no dimensionamento de máquinas virtuais definir a extensão do Service Fabric. O seguinte fragmento de modelo do Resource Manager mostra as propriedades que irá declarar, com o mesmo valor para os seus conjuntos de dimensionamento aprovisionado novo que estão a dimensionar para e só é suportado como uma temporária com monitoração de estado para o seu cluster:

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Não deixe o seu cluster em execução com vários conjuntos de dimensionamento que utilizam o mesmo `nodeTypeRef` mais do que o valor da propriedade necessário para concluir uma operação de dimensionamento vertical com êxito.
> Valide sempre operações em ambientes de teste antes de tentar alterações de ambiente de produção. Por predefinição, os serviços do sistema de Cluster do Service Fabric tem uma restrição de colocação apenas o tipo de nó primário destino.

Com as propriedades de nó e restrições de posicionamento declaradas, efetue a seguinte passos uma instância de VM ao mesmo tempo. Isso permite que os serviços do sistema (e seus serviços com estado) ao encerrar corretamente na instância de VM que está a remover à medida que novos réplicas são criadas noutro local.

1. A partir do PowerShell, execute `Disable-ServiceFabricNode` com a intenção "RemoveNode" para desativar o nó que pretende remover. Remova o tipo de nó que tem o número mais elevado. Por exemplo, se tiver um cluster de seis nós, remova a instância de máquina virtual "MyNodeType_5".
2. Executar `Get-ServiceFabricNode` para se certificar de que o nó foi transferido para desativado. Caso contrário, aguarde até que o nó está desabilitado. Isto pode demorar algumas horas para cada nó. Não continue até que o nó foi transferido para desativado.
3. Diminua o número de VMs por um nesse tipo de nó. A instância VM mais elevada agora será removida.
4. Repita os passos 1 a 3 conforme necessário, mas nunca reduzir verticalmente o número de instâncias, os tipos de nó primário, de menos do que o que justifica o escalão de fiabilidade. Ver [planear a capacidade de cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) para obter uma lista de instâncias recomendadas.

> [!NOTE]
> É um cenário suportado para quando efetuar uma operações de dimensionamento vertical: Eu possível migrar meu Cluster do Service Fabric e o aplicativo de disco não gerido para os Managed Disks sem períodos de indisponibilidade de aplicação. Ao aprovisionar uma nova máquina virtual de conjuntos de dimensionamento com discos geridos e efetuar uma atualização de aplicação com restrições de posicionamento que visam aprovisionado capacidade. cluster do Service Fabric pode agendar sua carga de trabalho na capacidade de nó de cluster aprovisionado que é implementada por domínio de atualização sem períodos de indisponibilidade de aplicação. [Azure SKU básico de balanceadores de carga](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) pontos finais de conjunto de back-end podem ser uma máquinas virtuais num único conjunto de disponibilidade ou conjunto de dimensionamento de máquina virtual. Isso significa que não é possível utilizar um balanceador de carga de SKU básico se mover a sua aplicação de sistemas do Service Fabric entre conjuntos de dimensionamento, sem causar inaccessibility temporária de seus recursos de infraestrutura do serviço de cluster ponto final de gestão, mesmo que o cluster e a sua aplicação ainda em execução; normalmente utilizador aprovisionar um balanceador de carga de SKU padrão, quando efetuar uma alternância de endereço IP (VIP) virtual entre recursos de LB de SKU básico e padrão de SKU de LB, para minimizar qualquer futuro aproximadamente 30 segundos são isso inaccessibility necessário para a troca de VIP.

## <a name="horizontal-scaling"></a>Dimensionamento horizontal

Horizontal dimensionamento no Service Fabric pode ser feito um [manualmente](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) ou [programaticamente](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling).

> [!NOTE]
> Se o dimensionamento de um tipo de nó que possui durabilidade silver ou gold, dimensionamento será lenta.

### <a name="scaling-out"></a>Aumentar horizontalmente

Aumentar horizontalmente um cluster do Service Fabric ao aumentar a contagem de instâncias para um conjunto de dimensionamento de máquina virtual específica. Pode aumentar horizontalmente por meio de programação com o AzureClient e o ID para o conjunto para aumentar a capacidade de dimensionamento pretendido.

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
* Para um serviço com estado, é necessário um determinado número de nós que estão sempre se manter a disponibilidade e preservar o estado do seu serviço. No mínimo, é necessário um número de nós igual à contagem de conjunto de réplicas de destino do serviço/partição.

Para reduzir horizontalmente manualmente, siga estes passos:

1. A partir do PowerShell, execute `Disable-ServiceFabricNode` com a intenção "RemoveNode" para desativar o nó que pretende remover. Remova o tipo de nó que tem o número mais elevado. Por exemplo, se tiver um cluster de seis nós, remova a instância de máquina virtual "MyNodeType_5".
2. Executar `Get-ServiceFabricNode` para se certificar de que o nó foi transferido para desativado. Caso contrário, aguarde até que o nó está desabilitado. Isto pode demorar algumas horas para cada nó. Não continue até que o nó foi transferido para desativado.
3. Diminua o número de VMs por um nesse tipo de nó. A instância VM mais elevada agora será removida.
4. Repita os passos 1 a 3 conforme necessário, mas nunca reduzir verticalmente o número de instâncias, os tipos de nó primário, de menos do que o que justifica o escalão de fiabilidade. Ver [planear a capacidade de cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) para obter uma lista de instâncias recomendadas.

Para reduzir horizontalmente manualmente, atualize a capacidade na propriedade SKU do desejada [conjunto de dimensionamento de máquina virtual](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) recursos.

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

> [!NOTE]
> Quando dimensiona um cluster para baixo, verá a instância VM/nó removido apresentada em mau estado de funcionamento no Service Fabric Explorer. Para obter uma explicação desse comportamento, consulte [comportamentos, pode observar no Service Fabric Explorer](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer). Pode:
> * Chamar [Remove-ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) com o nome de nó adequado.
> * Implementar [aplicação de programa auxiliar de dimensionamento automático de recursos de infraestrutura do serviço](https://github.com/Azure/service-fabric-autoscale-helper/) no cluster, o que garante a dimensionado para baixo de nós são limpas do Service Fabric Explorer.

## <a name="reliability-levels"></a>Níveis de confiabilidade

O [nível de fiabilidade](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) é uma propriedade do seu recurso de Cluster do Service Fabric e não pode ser configurado de forma diferente para tipos de nós individuais. Ele controla o fator de replicação de serviços do sistema para o cluster e é uma definição ao nível do recurso de cluster. O nível de fiabilidade determinará o número mínimo de nós que seu tipo de nó primário tem de ter. O escalão de fiabilidade pode realizar os seguintes valores:

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

O nível de durabilidade deve ser definido em dois recursos. O perfil de extensão do [recurso de conjunto de dimensionamento de máquina virtual](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

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

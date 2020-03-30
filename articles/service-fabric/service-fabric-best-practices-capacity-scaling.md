---
title: Planeamento e escalação de capacidades para tecido de serviço Azure
description: Boas práticas para planeamento e dimensionamento de clusters e aplicações de tecido de serviço.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: bf228e17ca24df9833f96f0c6fd3ef232cdf7ae6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258996"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Planeamento e escalação de capacidades para tecido de serviço Azure

Antes de criar qualquer cluster de tecido de serviço Azure ou recursos de computação em escala que acolhem o seu cluster, é importante planear a capacidade. Para obter mais informações sobre o planeamento da capacidade, consulte Planeamento da capacidade de [cluster de Tecido de Serviço](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity). Para obter mais orientações sobre as melhores práticas para a escalabilidade do cluster, consulte considerações de [escalabilidade](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations)do Tecido de Serviço .

Além de considerar as características do tipo nó e do cluster, é de esperar que as operações de escala demorem mais de uma hora a ser concluídas para um ambiente de produção. Esta consideração é verdadeira independentemente do número de VMs que está a adicionar.

## <a name="autoscaling"></a>Dimensionamento automático
Deve realizar operações de escala através de modelos do Gestor de Recursos Do Azure, porque é a melhor prática para tratar as [configurações]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)de recursos como código . 

A utilização de escalas automáticas através de conjuntos de escala de máquinavirtual fará com que o seu modelo de Gestor de Recursos versado defina incorretamente as suas contagens de exemplo para conjuntos de escala de máquinas virtuais. A definição imprecisa aumenta o risco de futuras implantações causarem operações de escala não intencionais. Em geral, deve utilizar a autoscalcificação se:

* A implementação dos seus modelos de Gestor de Recursos com capacidade adequada declarada não suporta o seu caso de utilização.
     
   Além da escala manual, pode configurar um gasoduto de [integração e entrega contínuo nos Serviços Azure DevOps utilizando projetos de implantação do grupo azure.](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts) Este gasoduto é comumente desencadeado por uma aplicação lógica que utiliza métricas de desempenho de máquinas virtuais consultadas pela [API REST Do Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough). O gasoduto efetivamente autoescala com base nas métricas que quiser, ao mesmo tempo que otimiza para os modelos de Gestor de Recursos.
* Você precisa escalar horizontalmente apenas um nó de escala de máquina virtual de cada vez.
   
   Para escamas em três ou mais nós de cada vez, deve [escalonar um cluster de Tecido de Serviço adicionando um conjunto](virtual-machine-scale-set-scale-node-type-scale-out.md)de escala de máquina virtual . É mais seguro escalar e escalar a escala virtual da escala horizontalmente, um nó de cada vez.
* Tem fiabilidade de Prata ou superior para o seu cluster de Tecido de Serviço, e durabilidade de prata ou superior em qualquer escala onde configura rindo regras de autoscalcificação.
  
   A capacidade mínima para as regras de autoscalcificação deve ser igual ou superior a cinco casos de máquinas virtuais. Também deve ser igual ou superior ao mínimo do seu Nível de Fiabilidade para o seu tipo de nó primário.

> [!NOTE]
> O tecido de serviço de serviço imponente:/Sistema/InfastructureServiço/<NODE_TYPE_NAME> funciona em todos os tipos de nó que têm prata ou maior durabilidade. É o único serviço de sistema que é suportado para funcionar em Azure em qualquer um dos seus clusters tipos de nós.

## <a name="vertical-scaling-considerations"></a>Considerações de escala vertical

[A escala vertical](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) de um nó em Tecido de Serviço Azure requer uma série de passos e considerações. Por exemplo:

* O aglomerado deve estar saudável antes de escalar. Caso contrário, irá desestabilizar ainda mais o aglomerado.
* O nível de durabilidade da prata ou superior é necessário para todos os tipos de nó de cluster de tecido de serviço que acolhem serviços estatais.

> [!NOTE]
> O seu nó primário que acolhe serviços de sistema de tecido de serviço audais deve ser o nível de durabilidade de Prata ou superior. Depois de ativar a durabilidade da Prata, operações de cluster tais como upgrades, adição ou remoção de nós, e assim por diante será mais lento porque o sistema otimiza para a segurança dos dados em vez da velocidade das operações.

A escala vertical de um conjunto de escala de máquina virtual é uma operação destrutiva. Em vez disso, escala horizontalmente o seu cluster adicionando um novo conjunto de escala com o SKU desejado. Em seguida, emigre os seus serviços para o SKU desejado para completar uma operação de escala vertical segura. Mudar um recurso de conjunto de máquina virtual SKU é uma operação destrutiva porque reimagens os seus anfitriões, que remove todo o estado persponia localmente.

O seu cluster utiliza propriedades do nó de tecido de serviço [e restrições](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) de colocação para decidir onde hospedar os serviços da sua aplicação. Quando estiver a escalar verticalmente o seu tipo de nó `"nodeTypeRef"`primário, declare valores de propriedade idênticos para . Pode encontrar estes valores na extensão de Tecido de Serviço para conjuntos de escala de máquinavirtual. 

O seguinte corte de um modelo de Gestor de Recursos mostra as propriedades que irá declarar. Tem o mesmo valor para os conjuntos de escala recém-provisionados a que está a escalonar, e é suportado apenas como um serviço temporário e statal para o seu cluster.

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Não deixe o seu cluster a funcionar com `nodeTypeRef` conjuntos de escala múltiplas que usam o mesmo valor de propriedade mais tempo do que o necessário para completar uma operação de escala vertical bem sucedida.
>
> Valide sempre as operações em ambientes de teste antes de tentar alterações no ambiente de produção. Por predefinição, os serviços do sistema de cluster service Fabric têm uma restrição de colocação apenas ao tipo de nó primário alvo.

Com as propriedades do nó e os constrangimentos de colocação declarados, faça os seguintes passos um caso VM de cada vez. Isto permite que os serviços do sistema (e os seus serviços estatais) sejam encerrados graciosamente na instância VM que está a remover à medida que novas réplicas são criadas em outros lugares.

1. Da PowerShell, `Disable-ServiceFabricNode` corra `RemoveNode` com intenção de desativar o nó que vai remover. Retire o tipo de nó que tem o maior número. Por exemplo, se tiver um cluster de seis nós, remova a instância de máquina virtual "MyNodeType_5".
2. Corra `Get-ServiceFabricNode` para se certificar de que o nó passou para deficientes. Caso contrário, aguarde até que o nó esteja desativado. Isto pode levar algumas horas para cada nó. Não prossiga até que o nó tenha transitado para deficientes.
3. Diminua o número de VMs por um nesse tipo de nó. A maior instância vm será agora removida.
4. Repita os passos 1 a 3 conforme necessário, mas nunca reduza o número de instâncias nos tipos do nó primário menos do que o que o nível de fiabilidade garante. Consulte [a capacidade do cluster Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) para obter uma lista de instâncias recomendadas.
5. Uma vez que todos os VMs se foram (representados como "Down") o tecido:/Sistema/Serviço de Infraestruturas/[nome do nó] mostrará um estado de erro. Em seguida, pode atualizar o recurso do cluster para remover o tipo de nó. Pode utilizar a implementação do modelo ARM ou editar o recurso de cluster através do gestor de [recursos Azure](https://resources.azure.com). Isto iniciará uma atualização do cluster que removerá o serviço de tecido:/Sistema/Infra-estruturaService/[nó tipo] que está em estado de erro.
 6. Depois disso, pode eliminar opcionalmente o VMScaleSet, ainda verá os nós como "Down" da vista Service Fabric Explorer. O último passo seria limpá-los com `Remove-ServiceFabricNodeState` o comando.

## <a name="horizontal-scaling"></a>Dimensionamento horizontal

Pode fazer escalas horizontais [manualmente](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) ou [programáticas](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling).

> [!NOTE]
> Se estiver escalonando um nó que tem durabilidade de Prata ou Ouro, a escala será lenta.

### <a name="scaling-out"></a>Aumentar horizontalmente

Escamas de um cluster de tecido de serviço aumentando a contagem de exemplo para um conjunto de escala de máquina virtual particular. Pode escalar programáticamente utilizando `AzureClient` e o ID para a escala desejada para aumentar a capacidade.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Para esdimensionar manualmente, atualize a capacidade na propriedade SKU do recurso de conjunto de [escala virtual](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) de escala de máquina sku pretendido.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>Escalando em

A escala requer mais consideração do que escalonar. Por exemplo:

* Serviço Os serviços de sistema fabric a funcionar no tipo de nó primário no seu cluster. Nunca desligue ou reduza o número de instâncias para esse tipo de nó, de modo a que tenha menos instâncias do que o que o nível de fiabilidade garante. 
* Para um serviço imponente, você precisa de um certo número de nós que estão sempre dispostos a manter a disponibilidade e preservar o estado do seu serviço. No mínimo, precisa de um número de nós iguais à contagem de réplicas-alvo da partição ou serviço.

Para escalar manualmente, siga estes passos:

1. Da PowerShell, `Disable-ServiceFabricNode` corra `RemoveNode` com intenção de desativar o nó que vai remover. Retire o tipo de nó que tem o maior número. Por exemplo, se tiver um cluster de seis nós, remova a instância de máquina virtual "MyNodeType_5".
2. Corra `Get-ServiceFabricNode` para se certificar de que o nó passou para deficientes. Caso contrário, aguarde até que o nó esteja desativado. Isto pode levar algumas horas para cada nó. Não prossiga até que o nó tenha transitado para deficientes.
3. Diminua o número de VMs por um nesse tipo de nó. A maior instância vm será agora removida.
4. Repita os passos 1 a 3, conforme necessário até fornecer a capacidade que deseja. Não reduza o número de instâncias nos tipos do nó primário para menos do que o nível de fiabilidade garante. Consulte [a capacidade do cluster Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) para obter uma lista de instâncias recomendadas.

Para escalar manualmente, atualize a capacidade na propriedade SKU do recurso de conjunto de [escala virtual](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) de escala de máquina pretendido.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

Deve preparar o nó para a escala em escala programática. Encontre o nó a ser removido (o nó de mais alta instância). Por exemplo:

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

Desative e remova o nó `FabricClient` utilizando`client` a mesma instância (neste`instanceIdString` caso) e a instância nó (neste caso) que utilizou no código anterior:

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
> Quando reduzir um cluster, verá a instância de nó/VM removida exibida num estado pouco saudável no Service Fabric Explorer. Para obter uma explicação deste comportamento, consulte [Comportamentos que poderá observar no Service Fabric Explorer](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer). Pode:
> * Ligue para o [comando Remover-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) com o nome do nó apropriado.
> * Implemente a aplicação de ajudante de [autoescala de tecido de serviço](https://github.com/Azure/service-fabric-autoscale-helper/) no seu cluster. Esta aplicação garante que os nós dimensionados são retirados do Service Fabric Explorer.

## <a name="reliability-levels"></a>Níveis de fiabilidade

O [nível de fiabilidade](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) é uma propriedade do seu recurso de cluster Service Fabric. Não pode ser configurado de forma diferente para tipos individuais de nó. Controla o fator de replicação dos serviços do sistema para o cluster, e é uma definição ao nível dos recursos do cluster. 

O nível de fiabilidade determinará o número mínimo de nós que o seu nó primário deve ter. O nível de fiabilidade pode assumir os seguintes valores:

* Platinum: Executa os serviços do sistema com uma contagem de sete e nove nódosas de sementes.
* Ouro: Gere os serviços do sistema com uma contagem de sete e sete nódosas de sementes.
* Prata: Executa os serviços do sistema com uma contagem de cinco e cinco nódosas de sementes.
* Bronze: Executa os serviços do sistema com uma contagem de três e três nódosas de semente.

O nível mínimo de fiabilidade recomendado é Prata.

O nível de fiabilidade é definido na secção de propriedades do [recurso Microsoft.ServiceFabric/clusters,](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters)como este:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Níveis de durabilidade

> [!WARNING]
> Os tipos de nó que correm com durabilidade de Bronze não obtêm _privilégios._ Os trabalhos de infraestrutura que afetam as suas cargas de trabalho apátridas não serão interrompidos ou atrasados, o que poderá afetar as suas cargas de trabalho. 
>
> Utilize a durabilidade de Bronze apenas para tipos de nó que executam cargas de trabalho apátridas. Para cargas de trabalho de produção, execute silver ou superior para garantir a consistência do estado. Escolha a fiabilidade certa com base na orientação na [documentação](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)de planeamento de capacidades.

O nível de durabilidade deve ser fixado em dois recursos. Um deles é o perfil de extensão do recurso conjunto de escala de [máquina virtual:](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)

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

O outro recurso `nodeTypes` encontra-se no [recurso Microsoft.ServiceFabric/clusters:](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters) 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Passos seguintes

* Crie um cluster em VMs ou computadores que executem o Windows Server: [Criação](service-fabric-cluster-creation-for-windows-server.md)de cluster de tecido de serviço para o Windows Server .
* Crie um cluster em VMs ou computadores em execução de Linux: [Crie um cluster Linux](service-fabric-cluster-creation-via-portal.md).
* Saiba mais sobre as opções de suporte do [Tecido de Serviço.](service-fabric-support.md)

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png

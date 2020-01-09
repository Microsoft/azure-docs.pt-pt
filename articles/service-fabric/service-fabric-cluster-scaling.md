---
title: Dimensionamento do Cluster Service Fabric do Azure
description: Saiba mais sobre como dimensionar os clusters do Azure Service Fabric para dentro ou para fora ou para baixo. À medida que as demandas de aplicativo mudam, podem Service Fabric clusters.
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: atsenthi
ms.openlocfilehash: 9dd60a5898b648215fc8b26e49a706a7b19dfeeb
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610085"
---
# <a name="scaling-azure-service-fabric-clusters"></a>Dimensionando clusters de Service Fabric do Azure
Um Cluster Service Fabric é um conjunto de máquinas físicas ou virtuais conectadas à rede em que seus microserviços são implantados e gerenciados. Uma máquina ou VM que faz parte de um cluster é chamada de nó. Os clusters podem conter potencialmente milhares de nós. Depois de criar um Cluster Service Fabric, você pode dimensionar o cluster horizontalmente (alterar o número de nós) ou verticalmente (alterar os recursos dos nós).  Você pode dimensionar o cluster a qualquer momento, mesmo quando as cargas de trabalho estiverem em execução no cluster.  À medida que o cluster é dimensionado, os aplicativos também são dimensionados automaticamente.

Por que dimensionar o cluster? As demandas de aplicativo mudam ao longo do tempo.  Talvez seja necessário aumentar os recursos de cluster para atender à carga de trabalho de aplicativo ou ao tráfego de rede aumentado ou diminuir os recursos de cluster quando a demanda cair.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Dimensionamento ou dimensionamento horizontal
Altera o número de nós no cluster.  Depois que os novos nós ingressam no cluster, o [Gerenciador de recursos de cluster](service-fabric-cluster-resource-manager-introduction.md) move os serviços para eles, o que reduz a carga nos nós existentes.  Você também pode diminuir o número de nós se os recursos do cluster não estiverem sendo usados com eficiência.  Como os nós deixam o cluster, os serviços saem desses nós e aumentam a carga nos nós restantes.  Reduzir o número de nós em um cluster em execução no Azure pode poupar dinheiro, pois você paga pelo número de VMs usadas e não pela carga de trabalho nessas VMs.  

- Vantagens: escala infinita, em teoria.  Se seu aplicativo for projetado para escalabilidade, você poderá habilitar o crescimento ilimitado adicionando mais nós.  As ferramentas em ambientes de nuvem facilitam a adição ou a remoção de nós, portanto, é fácil ajustar a capacidade e você só paga pelos recursos que usar.  
- Desvantagens: os aplicativos devem ser [projetados para escalabilidade](service-fabric-concepts-scalability.md).  Os bancos de dados de aplicativos e a persistência podem exigir um trabalho adicional de arquitetura para serem dimensionados também.  As [coleções confiáveis](service-fabric-reliable-services-reliable-collections.md) no Service Fabric serviços com estado, no entanto, tornam muito mais fácil dimensionar os dados do aplicativo.

Os conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure que pode utilizar para implementar e gerir uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó definido em um cluster do Azure é [configurado como um conjunto de dimensionamento separado](service-fabric-cluster-nodetypes.md). Cada tipo de nó, em seguida, pode ser reduzido horizontalmente ou horizontalmente de forma independente, têm conjuntos diferentes de portas abertas e pode ter métricas de capacidade diferente. 

Ao dimensionar um cluster do Azure, tenha em mente as seguintes diretrizes:
- os tipos de nó primários que executam cargas de trabalho de produção sempre devem ter cinco ou mais nós.
- os tipos de nó não primário que executam cargas de trabalho de produção com estado sempre devem ter cinco ou mais nós.
- os tipos de nó não primário que executam cargas de trabalho de produção sem monitoração de estado sempre devem ter dois ou mais nós.
- Qualquer tipo de nó de [nível de durabilidade](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Gold ou prata sempre deve ter cinco ou mais nós.
- Não remova instâncias/nós de VM aleatórios de um tipo de nó, sempre use o recurso de redução vertical do conjunto de dimensionamento de máquinas virtuais. A exclusão de instâncias de VM aleatórias pode afetar negativamente a capacidade dos sistemas de balancear a carga corretamente.
- Se estiver usando regras de dimensionamento automático, defina as regras para que o dimensionamento (removendo instâncias de VM) seja feito um nó por vez. Reduzir mais de uma instância por vez não é seguro.

Como os Service Fabric tipos de nó no cluster são compostos de conjuntos de dimensionamento de máquinas virtuais no back-end, você pode [configurar regras de dimensionamento automático ou dimensionar manualmente](service-fabric-cluster-scale-up-down.md) cada tipo de nó/conjunto de dimensionamento de máquinas virtuais.

### <a name="programmatic-scaling"></a>Dimensionamento programático
Em muitos cenários, [dimensionar um cluster manualmente ou com regras de dimensionamento automático](service-fabric-cluster-scale-up-down.md) são boas soluções. No entanto, para cenários mais avançados, eles podem não ser os adequados. As possíveis desvantagens dessas abordagens incluem:

- O dimensionamento manual exige que você entre e solicite explicitamente as operações de dimensionamento. Se as operações de dimensionamento forem necessárias com frequência ou em momentos imprevisíveis, essa abordagem poderá não ser uma boa solução.
- Quando as regras de dimensionamento automático removem uma instância de um conjunto de dimensionamento de máquinas virtuais, elas não removem automaticamente o conhecimento desse nó do cluster de Service Fabric associado, a menos que o tipo de nó tenha um nível de durabilidade prata ou ouro. Como as regras de dimensionamento automático funcionam no nível do conjunto de dimensionamento (em vez de no nível de Service Fabric), as regras de dimensionamento automático podem remover Service Fabric nós sem desligá-los normalmente. Essa remoção de nó rude deixará o ' fantasma ' Service Fabric estado do nó atrás das operações de redução em escala. Um indivíduo (ou um serviço) precisaria limpar periodicamente o estado do nó removido no Cluster Service Fabric.
- Um tipo de nó com um nível de durabilidade ouro ou prata limpa automaticamente os nós removidos, portanto, nenhuma limpeza adicional é necessária.
- Embora haja [muitas métricas](../azure-monitor/platform/autoscale-common-metrics.md) com suporte pelas regras de dimensionamento automático, ele ainda é um conjunto limitado. Se seu cenário chamar o dimensionamento com base em alguma métrica não abordada nesse conjunto, as regras de dimensionamento automático podem não ser uma boa opção.

Como você deve abordar Service Fabric o dimensionamento depende do seu cenário. Se o dimensionamento for incomum, a capacidade de adicionar ou remover nós manualmente provavelmente será suficiente. Para cenários mais complexos, as regras de dimensionamento automático e os SDKs que expõem a capacidade de dimensionar programaticamente oferecem alternativas poderosas.

Existem APIs do Azure que permitem que os aplicativos trabalhem programaticamente com conjuntos de dimensionamento de máquinas virtuais e clusters de Service Fabric. Se as opções de dimensionamento automático existentes não funcionarem para seu cenário, essas APIs possibilitarão a implementação da lógica de dimensionamento personalizada. 

Uma abordagem para implementar essa funcionalidade de dimensionamento automático de ' Home-out ' é adicionar um novo serviço sem estado ao aplicativo Service Fabric para gerenciar as operações de dimensionamento. Criar seu próprio serviço de dimensionamento fornece o maior grau de controle e personalização sobre o comportamento de dimensionamento do seu aplicativo. Isso pode ser útil para cenários que exigem controle preciso sobre quando ou como um aplicativo é dimensionado para dentro ou para fora. No entanto, esse controle vem com uma compensação da complexidade do código. Usar essa abordagem significa que você precisa ter o código de dimensionamento, que não é trivial. Dentro do método de `RunAsync` do serviço, um conjunto de gatilhos pode determinar se o dimensionamento é necessário (incluindo a verificação de parâmetros como o tamanho máximo do cluster e o dimensionamento de cooldowns).   

A API usada para interações de conjunto de dimensionamento de máquinas virtuais (para verificar o número atual de instâncias de máquina virtual e modificá-la) é a [biblioteca de computação de gerenciamento do Azure fluente](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/). A biblioteca de computação fluente fornece uma API fácil de usar para interagir com conjuntos de dimensionamento de máquinas virtuais.  Para interagir com o próprio cluster Service Fabric, use [System. Fabric. FabricClient](/dotnet/api/system.fabric.fabricclient).

No entanto, o código de dimensionamento não precisa ser executado como um serviço no cluster para ser dimensionado. Tanto `IAzure` quanto `FabricClient` podem se conectar a seus recursos do Azure associados remotamente, de modo que o serviço de dimensionamento pode ser facilmente um aplicativo de console ou serviço do Windows em execução de fora do aplicativo Service Fabric.

Com base nessas limitações, talvez você queira [implementar modelos de dimensionamento automático mais personalizados](service-fabric-cluster-programmatic-scaling.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Dimensionamento vertical ou horizontal 
Altera os recursos (CPU, memória ou armazenamento) de nós no cluster.
- Vantagens: a arquitetura de software e aplicativo permanece a mesma.
- Desvantagens: escala finita, já que há um limite para o quanto você pode aumentar os recursos em nós individuais. Tempo de inatividade, pois você precisará usar máquinas físicas ou virtuais offline para adicionar ou remover recursos.

Os conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure que pode utilizar para implementar e gerir uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó definido em um cluster do Azure é [configurado como um conjunto de dimensionamento separado](service-fabric-cluster-nodetypes.md). Cada tipo de nó pode ser gerenciado separadamente.  Dimensionar um tipo de nó para cima ou para baixo envolve alterar a SKU das instâncias de máquina virtual no conjunto de dimensionamento. 

> [!WARNING]
> Recomendamos que você não altere a SKU da VM de um conjunto de dimensionamento/tipo de nó, a menos que ele esteja sendo executado na [durabilidade de prata ou superior](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). A alteração do tamanho do SKU da VM é uma operação de infraestrutura in-loco destrutiva de dados. Sem alguma capacidade de atrasar ou monitorar essa alteração, é possível que a operação possa causar perda de dados para serviços com estado ou causar outros problemas operacionais imprevistos, mesmo para cargas de trabalho sem estado. 
>

Ao dimensionar um cluster do Azure, tenha em mente a seguinte diretriz:
- Se reduzir verticalmente um tipo de nó primário, você nunca deverá diminuí-lo mais do que o permitido pela [camada de confiabilidade](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) .

O processo de dimensionamento de um tipo de nó para cima ou para baixo é diferente, dependendo se ele é um tipo de nó não primário ou primário.

### <a name="scaling-non-primary-node-types"></a>Dimensionamento de tipos de nó não primários
Crie um novo tipo de nó com os recursos de que você precisa.  Atualize as restrições de posicionamento dos serviços em execução para incluir o novo tipo de nó.  Gradualmente (uma de cada vez), reduza a contagem de instâncias da contagem de instâncias do tipo de nó antigo para zero para que a confiabilidade do cluster não seja afetada.  Os serviços migrarão gradualmente para o novo tipo de nó, já que o tipo de nó antigo é encerrado.

### <a name="scaling-the-primary-node-type"></a>Dimensionando o tipo de nó primário
Recomendamos que você não altere a SKU da VM do tipo de nó primário. Se você precisar de mais capacidade de cluster, recomendamos adicionar mais instâncias. 

Se isso não for possível, você poderá criar um novo cluster e [restaurar o estado do aplicativo](service-fabric-reliable-services-backup-restore.md) (se aplicável) do cluster antigo. Você não precisa restaurar nenhum estado do serviço do sistema, eles são recriados quando você implanta seus aplicativos no novo cluster. Se você estava apenas executando aplicativos sem estado no cluster, tudo o que você faz é implantar seus aplicativos no novo cluster, não há nada para restaurar. Se você decidir ir para a rota sem suporte e quiser alterar a SKU da VM, faça modificações na definição do modelo do conjunto de dimensionamento de máquinas virtuais para refletir a nova SKU. Se o cluster tiver apenas um tipo de nó, certifique-se de que todos os seus aplicativos com estado respondam a todos os [eventos de ciclo de vida de réplica de serviço](service-fabric-reliable-services-lifecycle.md) (como a réplica na compilação está paralisada) em tempo hábil e que a duração da recompilação da réplica de serviço seja inferior a cinco minutos (para nível de durabilidade prateada). 

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a [escalabilidade do aplicativo](service-fabric-concepts-scalability.md).
* [Dimensionar ou reduzir um cluster do Azure](service-fabric-tutorial-scale-cluster.md).
* [Dimensione um cluster do Azure programaticamente](service-fabric-cluster-programmatic-scaling.md) usando o SDK de computação do Azure fluente.
* [Dimensionar ou reduzir um cluster autônomo](service-fabric-cluster-windows-server-add-remove-nodes.md).


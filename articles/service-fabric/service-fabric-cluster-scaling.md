---
title: Dimensionamento do Azure Service Fabric cluster | Documentos da Microsoft
description: Saiba mais sobre o dimensionamento de clusters do Azure Service Fabric no ou horizontalmente e ou reduzir verticalmente.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: aljo
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/13/2018
ms.author: ryanwi
ms.openlocfilehash: 527ab4ee0edaf3ac2048403d7063edef8fc58ae8
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57451982"
---
# <a name="scaling-azure-service-fabric-clusters"></a>Clusters de dimensionamento do Azure Service Fabric
Um cluster do Service Fabric é um conjunto ligado à rede de máquinas virtuais ou físicas, no qual os microsserviços são implementados e geridos. Uma máquina ou VM que faça parte de um cluster é chamado de nó. Clusters podem conter potencialmente milhares de nós. Depois de criar um cluster do Service Fabric, pode dimensionar o cluster horizontalmente (alterar o número de nós) ou na vertical (alterar os recursos de nós).  Pode dimensionar o cluster em qualquer altura, mesmo quando as cargas de trabalho em execução no cluster.  Como dimensiona o cluster, as aplicações são dimensionadas automaticamente também.

Por que motivo a dimensionar o cluster? Alteram pedidos de aplicações ao longo do tempo.  Poderá ter de aumentar os recursos do cluster para corresponder ao tráfego de rede ou de carga de trabalho de aplicações mais ou diminuir recursos do cluster quando a procura cair.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Dimensionamento e reduzir ou dimensionamento horizontal
Altera o número de nós do cluster.  Depois dos novos nós aderirem ao cluster, o [Resource Manager de Cluster](service-fabric-cluster-resource-manager-introduction.md) move serviços-lhes que reduz a carga em nós existentes.  Também pode diminuir o número de nós se não a recursos do cluster estão a ser utilizados com eficiência.  Como nós de deixam o cluster, os serviços sair de nós e carga aumenta em nós restantes.  Reduzindo o número de nós num cluster em execução no Azure pode economizar dinheiro, uma vez que paga o número de VMs que não a carga de trabalho nessas VMS e de utilização.  

- Vantagens: Dimensionamento infinito, na teoria.  Se seu aplicativo for desenvolvido para escalabilidade, pode ativar o crescimento ilimitado ao adicionar mais nós.  As ferramentas em ambientes de cloud torna mais fácil adicionar ou remover nós, portanto, é fácil de ajustar a capacidade e paga apenas pelos recursos que utilizar.  
- Desvantagens: Os aplicativos devem ser [concebida para escalabilidade e](service-fabric-concepts-scalability.md).  Bases de dados do aplicativo e persistência, podem exigir trabalho adicional de arquitetura para dimensionar também.  [As coleções fiáveis](service-fabric-reliable-services-reliable-collections.md) nos serviços de monitorização de estado do Service Fabric, no entanto, torná-lo muito mais fácil de dimensionar os dados da aplicação.

Os conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure que pode utilizar para implementar e gerir uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó que está definido num cluster do Azure é [configurado como um conjunto de dimensionamento separado](service-fabric-cluster-nodetypes.md). Cada tipo de nó, em seguida, pode ser reduzido horizontalmente ou horizontalmente de forma independente, têm conjuntos diferentes de portas abertas e pode ter métricas de capacidade diferente. 

Quando dimensionar um cluster do Azure, mantenha as seguintes diretrizes em mente:
- tipos de nó primário executar cargas de trabalho de produção sempre devem ter cinco ou mais nós.
- executar cargas de trabalho de produção com monitorização de estado de tipos de nó não primário sempre devem ter cinco ou mais nós.
- executar cargas de trabalho de produção sem monitoração de estado de tipos de nó não primário devem ter sempre dois ou mais nós.
- Qualquer tipo de nó de [nível de durabilidade](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) de Gold ou Silver sempre deve ter cinco ou mais nós.
- Não remover aleatórios VM instâncias/nós de um tipo de nó, utilize sempre o dimensionamento do conjunto de dimensionamento da máquina virtual para baixo de funcionalidade. A eliminação aleatórias de instâncias de VM pode afetar negativamente a capacidade de sistemas de balanceamento de carga corretamente.
- Se utilizar regras de dimensionamento automático, defina as regras para que o dimensionamento em (remover instâncias de VM) é feito um nó por vez. Não é seguro reduzir verticalmente mais de uma instância de cada vez.

Uma vez que os tipos de nós no cluster do Service Fabric são constituídos por conjuntos de dimensionamento de máquina virtual no back-end, pode [configurar regras de dimensionamento automático ou dimensionar manualmente](service-fabric-cluster-scale-up-down.md) cada conjunto de dimensionamento de máquinas virtuais/tipo do nó.

### <a name="programmatic-scaling"></a>Dimensionamento programática
Em muitos cenários, [um cluster de dimensionamento manualmente ou com regras de dimensionamento automático](service-fabric-cluster-scale-up-down.md) são soluções boa. Para cenários mais avançados, no entanto, podem não ser adequado. Potenciais desvantagens para essas abordagens incluem:

- Dimensionar Manualmente requer que inicie sessão e solicitar explicitamente as operações de dimensionamento. Se as operações de dimensionamento são necessárias com frequência, ou em momentos imprevisíveis, essa abordagem pode não ser uma boa solução.
- Quando as regras de dimensionamento automático remover uma instância de um conjunto de dimensionamento de máquinas virtuais, eles não remove automaticamente conhecimento desse nó de cluster do Service Fabric associado, a menos que o tipo de nó tem um nível de durabilidade de Gold ou Silver. Como regras de dimensionamento automático funcionam no nível de conjunto de dimensionamento (e não ao nível do Service Fabric), regras de dimensionamento automático podem remover nós do Service Fabric sem encerrá-los corretamente. Esta remoção do nó rude deixar "fantasma" Estado de nó do Service Fabric por trás após operações de dimensionamento. Uma pessoa (ou um serviço), seria necessário limpar periodicamente o estado do nó removido no cluster do Service Fabric.
- Um tipo de nó com um nível de durabilidade de Gold ou Silver limpa automaticamente removidos nós, pelo que não existem limpeza adicional é necessária.
- Apesar de existirem [diversas métricas](../azure-monitor/platform/autoscale-common-metrics.md) suportados pelas regras de dimensionamento automático, ainda é um conjunto limitado. Se o seu cenário chama-se para dimensionamento com base em algumas métrica não abrangida nesse conjunto, em seguida, regras de dimensionamento automático podem não ser uma boa opção.

Como deve abordar o dimensionamento do Service Fabric depende do seu cenário. Se o dimensionamento é incomum, a capacidade de adicionar ou remover nós manualmente é provavelmente suficiente. Para cenários mais complexos, regras de dimensionamento automático e SDKs expor a capacidade de dimensionar de forma programática oferecem alternativas poderosas.

APIs do Azure existem que permitem a clusters do Service Fabric e conjuntos de dimensionamento de aplicações para trabalhar programaticamente com a máquina virtual. Se as opções de dimensionamento automático existente não funcionarem para o seu cenário, essas APIs tornam possível implementar a lógica de dimensionamento personalizada. 

Uma abordagem para implementar essa funcionalidade de dimensionamento automático feitas home é adicionar um novo serviço sem monitoração de estado para a aplicação do Service Fabric para gerir operações de dimensionamento. Criar a sua própria a dimensionar o serviço fornece que o nível mais alto de controle e capacidade de personalização ao longo do seu aplicativo do dimensionamento comportamento. Isso pode ser útil para cenários que exigem um controlo preciso sobre o quando ou como um aplicativo é dimensionado dentro ou para fora. No entanto, esse controle gera uma compensação de complexidade de código. Usando essa abordagem, significa que precisa para o próprio código dimensionamento, o que não é simples. Dentro do serviço `RunAsync` método, um conjunto de acionadores pode determinar se o dimensionamento é necessário (incluindo a verificação de parâmetros, como o tamanho máximo de cluster e dimensionamento cooldowns).   

A API utilizada para interações de conjunto de dimensionamento de máquina virtual (ambos para verificar o número atual de instâncias de máquina virtual e modificá-la) é o [biblioteca de computação do Azure gestão fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/). A biblioteca de computação fluent fornece uma API fácil de usar para interagir com conjuntos de dimensionamento de máquina virtual.  Para interagir com o próprio cluster do Service Fabric, utilize [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient).

O código de dimensionamento não precisa ser executado como um serviço no cluster para ser dimensionada, no entanto. Ambos `IAzure` e `FabricClient` pode ligar-se aos seus recursos do Azure associados remotamente, para que o serviço de dimensionamento pode ser facilmente um aplicativo de console ou o serviço de Windows em execução de fora da aplicação do Service Fabric.

Com base nessas limitações, pode desejar [implemente mais adaptadas modelos de dimensionamento automáticos](service-fabric-cluster-programmatic-scaling.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Aumentar e reduzir verticalmente ou dimensionamento vertical 
Altera os recursos (CPU, memória ou armazenamento) de nós no cluster.
- Vantagens: Arquitetura de software e o aplicativo permanece o mesmo.
- Desvantagens: Escala finita, uma vez que existe um limite quanto pode aumentar os recursos em nós individuais. Tempo de inatividade, uma vez que precisará executar máquinas físicas ou virtuais offline para adicionar ou remover recursos.

Os conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure que pode utilizar para implementar e gerir uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó que está definido num cluster do Azure é [configurado como um conjunto de dimensionamento separado](service-fabric-cluster-nodetypes.md). Cada tipo de nó pode ser gerido separadamente.  Aumentando ou reduzindo, um tipo de nó envolve a alterar o SKU de instâncias da máquina virtual no conjunto de dimensionamento. 

> [!WARNING]
> Recomendamos que não altere o SKU de VM de um tipo de nó/conjunto de dimensionamento, a menos que ele está em execução no [durabilidade de Silver ou superior](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Alterar tamanho da SKU de VM é uma operação de infraestrutura do destrutiva dados no local. Sem capacidade algum atraso ou monitorizar esta alteração, é possível que a operação pode causar perda de dados para serviços com estado ou causar outros problemas operacionais imprevistos, mesmo para cargas de trabalho sem monitorização de estado. 
>

Quando dimensionar um cluster do Azure, considere a diretriz seguinte:
- Se reduzir verticalmente um tipo de nó principal, deve nunca dimensioná-lo para baixo mais do que o que o [escalão de fiabilidade](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) permite.

O processo de aumentando ou reduzindo, um tipo de nó é diferente consoante seja um tipo de nó não principal ou primária.

### <a name="scaling-non-primary-node-types"></a>Tipos de nós não primário de dimensionamento
Crie um novo tipo de nó com os recursos que necessários.  Atualize as restrições de posicionamento de serviços para incluir o novo tipo de nó em execução.  Gradualmente (um por vez), reduzir a contagem de instâncias de contagem de instâncias de tipo de nó antigo para zero, para que a confiabilidade do cluster não é afetada.  Serviços gradualmente serão migrados para o novo tipo de nó, como o tipo de nó antigo está desativado.

### <a name="scaling-the-primary-node-type"></a>Dimensionar o tipo de nó primário
Recomendamos que não altere o SKU de VM do tipo de nó primário. Se precisar de mais capacidade de cluster, recomendamos que adicione mais instâncias. 

Se que não é possível, pode criar um novo cluster e [restaurar estado do aplicativo](service-fabric-reliable-services-backup-restore.md) (se aplicável) do cluster antigo. Não é necessário restaurar o estado do serviço qualquer sistema, eles são recriados quando implanta aplicativos para o novo cluster. Se fosse apenas executar aplicativos sem monitoração de estado no seu cluster, então tudo o que fazer é implementar as suas aplicações para o novo cluster, não têm nada a restaurar. Se optar por seguir o caminho não suportado e quiser alterar o SKU de VM, em seguida, fazer modificações para o dimensionamento de máquinas virtuais definição de modelo para refletir o novo SKU do conjunto. Se o cluster tem apenas um tipo de nó, em seguida, certifique-se de que todas as suas aplicações com monitorização de estado a responder a todos [eventos de ciclo de vida de réplica do serviço](service-fabric-reliable-services-lifecycle.md) (como a réplica de compilação está bloqueada) na forma oportuna e que a réplica de serviço reconstruir duração é menos de cinco minutos (para o nível de durabilidade Silver). 

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [escalabilidade do aplicativo](service-fabric-concepts-scalability.md).
* [Dimensionar um cluster do Azure e reduzir](service-fabric-tutorial-scale-cluster.md).
* [Dimensionar um cluster do Azure através de programação](service-fabric-cluster-programmatic-scaling.md) através do Azure fluent computação SDK.
* [Dimensionar um cluster autónomo e reduzir](service-fabric-cluster-windows-server-add-remove-nodes.md).


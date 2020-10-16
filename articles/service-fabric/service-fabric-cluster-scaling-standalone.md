---
title: Escalagem autónoma de cluster de tecido de serviço Azure
description: Saiba mais sobre o escalonamento de aglomerados autónomos de tecido de serviço dentro ou fora e para cima ou para baixo.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 8184b2455e938fa0500308b462176e78b8dadcab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843036"
---
# <a name="scaling-service-fabric-standalone-clusters"></a>Clusters autónomos de tecido de escala
Um cluster de tecido de serviço é um conjunto de máquinas virtuais ou físicas ligadas à rede em que os seus microserviços são implantados e geridos. Uma máquina ou VM que faz parte de um aglomerado é chamada de nó. Os aglomerados podem conter potencialmente milhares de nós. Depois de criar um cluster de Tecido de Serviço, pode escalar o cluster horizontalmente (alterar o número de nós) ou verticalmente (alterar os recursos dos nós).  Pode escalar o cluster a qualquer momento, mesmo quando as cargas de trabalho estão a funcionar no cluster.  À medida que o cluster escala, as suas aplicações também escalam automaticamente.

Por que escalar o aglomerado? A aplicação exige mudanças ao longo do tempo.  Poderá ser necessário aumentar os recursos de cluster para responder ao aumento da carga de trabalho de aplicação ou ao tráfego de redes ou diminuir os recursos de cluster quando a procura diminui.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Escalonamento para dentro e para fora, ou escala horizontal
Altera o número de nós no cluster.  Assim que os novos nós se juntam ao cluster, o [Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md) transfere-lhes serviços que reduzem a carga nos nós existentes.  Também pode diminuir o número de nós se os recursos do cluster não estiverem a ser utilizados de forma eficiente.  À medida que os nós saem do cluster, os serviços movem-se desses nós e aumentam os aumentos de carga nos restantes nós.  A redução do número de nós num cluster em execução em Azure pode economizar dinheiro, uma vez que paga pelo número de VMs que utiliza e não pela carga de trabalho nesses VMs.  

- Vantagens: Escala infinita, em teoria.  Se a sua aplicação for concebida para a escalabilidade, pode permitir um crescimento ilimitado adicionando mais nós.  A ferramenta em ambientes em nuvem facilita a adição ou remoção de nós, por isso é fácil ajustar a capacidade e só paga pelos recursos que utiliza.  
- Desvantagens: As aplicações devem ser [concebidas para a escalabilidade](service-fabric-concepts-scalability.md).  As bases de dados de aplicações e a persistência podem exigir trabalhos arquitetónicos adicionais à escala também.  No entanto, [as coleções fiáveis](service-fabric-reliable-services-reliable-collections.md) nos serviços estatais da Service Fabric tornam muito mais fácil a escala dos dados da sua aplicação.

Os clusters autónomos permitem-lhe implantar o cluster de Tecidos de Serviço no local ou no fornecedor de nuvem à sua escolha.  Os tipos de nó são compostos por máquinas físicas ou máquinas virtuais, dependendo da sua implantação. Em comparação com os aglomerados que correm em Azure, o processo de escalonamento de um cluster autónomo está um pouco mais envolvido.  Deve alterar manualmente o número de nós no cluster e, em seguida, executar uma atualização de configuração de cluster.

A remoção dos nós pode iniciar várias atualizações. Alguns nós são marcados com `IsSeedNode=”true”` etiqueta e podem ser identificados consultando o manifesto do cluster usando [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest). A remoção de tais nós pode demorar mais tempo do que outros, uma vez que os nós de sementes terão de ser movidos nesses cenários. O cluster deve manter um mínimo de três nós do tipo nó primário.

> [!WARNING]
> Recomendamos que não baixe a contagem de nós abaixo [do tamanho do cluster do nível de fiabilidade](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) para o cluster. Isto irá interferir com a capacidade dos Serviços de Sistema de Tecido de Serviço serem replicados em todo o cluster, e irá desestabilizar ou possivelmente destruir o cluster.
>

Ao escalar um cluster autónomo, tenha em mente as seguintes diretrizes:
- A substituição dos nós primários deve ser executada um nó após o outro, em vez de remover e, em seguida, adicionar em lotes.
- Antes de remover um tipo de nó, verifique se existem nóles que referenciam o tipo de nó. Retire estes nós antes de remover o tipo de nó correspondente. Uma vez removidos todos os nós correspondentes, pode remover o NodeType da configuração do cluster e iniciar uma atualização de configuração utilizando [start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade).

Para obter mais informações, consulte [um cluster autónomo.](service-fabric-cluster-windows-server-add-remove-nodes.md)

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Escalonamento para cima e para baixo, ou escala vertical 
Altera os recursos (CPU, memória ou armazenamento) dos nós no cluster.
- Vantagens: A arquitetura de software e aplicações permanece a mesma.
- Desvantagens: Escala finita, uma vez que há um limite para quanto você pode aumentar recursos em nós individuais. Tempo de inatividade, porque terá de desligar as máquinas físicas ou virtuais para adicionar ou remover recursos.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [a escalabilidade da aplicação.](service-fabric-concepts-scalability.md)
* [Escalar um aglomerado Azure dentro ou fora](service-fabric-tutorial-scale-cluster.md).
* [Dimensione um cluster Azure programáticamente](service-fabric-cluster-programmatic-scaling.md) usando o fluente Azure compute SDK.
* [Escalar um aglomerado autónomo para dentro ou para fora](service-fabric-cluster-windows-server-add-remove-nodes.md).


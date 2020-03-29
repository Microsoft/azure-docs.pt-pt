---
title: Escala de cluster autónomo azure service Fabric
description: Aprenda sobre a escalade os clusters autónomos de tecido solado dentro ou fora e para cima ou para baixo.
author: dkkapur
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: dekapur
ms.openlocfilehash: 16ec0eb429ec6e8f6613490226b7cff01dff1b32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451916"
---
# <a name="scaling-service-fabric-standalone-clusters"></a>Clusters autónomos de tecido de serviço de escala
Um cluster de tecido de serviço é um conjunto de máquinas virtuais ou físicas ligadas à rede, nas quais os seus microserviços são implantados e geridos. Uma máquina ou VM que faz parte de um aglomerado é chamado de nó. Os aglomerados podem conter potencialmente milhares de nós. Depois de criar um cluster de Tecido de Serviço, pode escalar o cluster horizontalmente (alterar o número de nós) ou verticalmente (alterar os recursos dos nós).  Pode escalar o cluster a qualquer momento, mesmo quando as cargas de trabalho estão a decorrer no cluster.  À medida que o cluster escala, as suas aplicações também escalam automaticamente.

Por que escalar o aglomerado? A aplicação exige mudanças ao longo do tempo.  Pode ser necessário aumentar os recursos de cluster para satisfazer o aumento da carga de trabalho das aplicações ou o tráfego de rede ou diminuir os recursos de cluster quando a procura diminui.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Escalando para dentro e para fora, ou escalação horizontal
Altera o número de nós no cluster.  Assim que os novos nós se juntam ao cluster, o [Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md) transfere os serviços para eles, o que reduz a carga nos nós existentes.  Também pode diminuir o número de nós se os recursos do cluster não estiverem a ser utilizados de forma eficiente.  À medida que os nós saem do cluster, os serviços saem desses nós e a carga aumenta nos restantes nós.  Reduzir o número de nós num cluster em funcionamento em Azure pode economizar dinheiro, uma vez que paga pelo número de VMs que utiliza e não pela carga de trabalho nesses VMs.  

- Vantagens: Escala infinita, em teoria.  Se a sua aplicação for concebida para a escalabilidade, pode permitir um crescimento ilimitado adicionando mais nós.  A ferramenta em ambientes de nuvem facilita a adição ou remoção de nós, por isso é fácil ajustar a capacidade e só paga pelos recursos que utiliza.  
- Desvantagens: As aplicações devem ser [concebidas para a escalabilidade](service-fabric-concepts-scalability.md).  As bases de dados de aplicações e a persistência podem exigir também trabalhos arquitetónicos adicionais à escala.  No entanto, as [coleções fiáveis](service-fabric-reliable-services-reliable-collections.md) em serviços de serviço saem, no entanto, facilitam muito a escala dos dados da sua aplicação.

Os clusters autónomos permitem-lhe implantar cluster service Fabric no local ou no fornecedor de nuvem à sua escolha.  Os tipos de nó são compostos por máquinas físicas ou máquinas virtuais, dependendo da sua implantação. Em comparação com os aglomerados em funcionamento em Azure, o processo de escalação de um cluster autónomo está um pouco mais envolvido.  Tem de alterar manualmente o número de nós no cluster e, em seguida, executar uma atualização de configuração de cluster.

A remoção dos nódosos pode iniciar várias atualizações. Alguns nós são `IsSeedNode=”true”` marcados com etiqueta e podem ser identificados consultando o manifesto do cluster usando [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest). A remoção desses nódosos pode demorar mais tempo do que outros, uma vez que os nódosos de sementes terão de ser movidos nesses cenários. O aglomerado deve manter um mínimo de três nós primários do tipo.

> [!WARNING]
> Recomendamos que não baixe a contagem do nó abaixo do [tamanho do cluster do Nível de Fiabilidade](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) para o cluster. Isto interferirá com a capacidade dos Serviços de Sistema de Tecidos de Serviço de serem replicados em todo o cluster, e irá desestabilizar ou possivelmente destruir o cluster.
>

Ao escalonar um cluster autónomo, tenha em mente as seguintes orientações:
- A substituição dos nós primários deve ser executada um nó após o outro, em vez de remover e, em seguida, adicionar em lotes.
- Antes de remover um nó, verifique se existem nós referentes ao tipo de nó. Retire estes nódosos antes de remover o tipo de nó correspondente. Uma vez removidos todos os nós correspondentes, pode remover o NodeType da configuração do cluster e iniciar uma atualização de configuração utilizando o [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade).

Para mais informações, consulte [a escala de um cluster autónomo](service-fabric-cluster-windows-server-add-remove-nodes.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Escalando para cima e para baixo, ou escalação vertical 
Altera os recursos (CPU, memória ou armazenamento) dos nós do cluster.
- Vantagens: A arquitetura de software e aplicação permanece a mesma.
- Desvantagens: Escala finita, uma vez que há um limite para o quanto pode aumentar os recursos em nós individuais. Tempo de inatividade, porque terá de desligar as máquinas físicas ou virtuais para adicionar ou remover recursos.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [a escalabilidade da aplicação.](service-fabric-concepts-scalability.md)
* [Escala um aglomerado Azure para dentro ou para fora.](service-fabric-tutorial-scale-cluster.md)
* [Dimensione um cluster Azure programáticamente](service-fabric-cluster-programmatic-scaling.md) utilizando o fluente sdk de computação Azure.
* [Escala um aglomerado autónomo dentro ou fora.](service-fabric-cluster-windows-server-add-remove-nodes.md)


---
title: Dimensionamento de cluster autônomo do Azure Service Fabric | Microsoft Docs
description: Saiba mais sobre como dimensionar Service Fabric clusters autônomos para dentro ou para fora ou para baixo.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/13/2018
ms.author: dekapur
ms.openlocfilehash: eedf80ec82a748f5da8e51aed8b4d403dffe4169
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599865"
---
# <a name="scaling-service-fabric-standalone-clusters"></a>Dimensionamento Service Fabric clusters autônomos
Um Cluster Service Fabric é um conjunto de máquinas físicas ou virtuais conectadas à rede em que seus microserviços são implantados e gerenciados. Uma máquina ou VM que faz parte de um cluster é chamada de nó. Os clusters podem conter potencialmente milhares de nós. Depois de criar um Cluster Service Fabric, você pode dimensionar o cluster horizontalmente (alterar o número de nós) ou verticalmente (alterar os recursos dos nós).  Você pode dimensionar o cluster a qualquer momento, mesmo quando as cargas de trabalho estiverem em execução no cluster.  À medida que o cluster é dimensionado, os aplicativos também são dimensionados automaticamente.

Por que dimensionar o cluster? As demandas de aplicativo mudam ao longo do tempo.  Talvez seja necessário aumentar os recursos de cluster para atender à carga de trabalho de aplicativo ou ao tráfego de rede aumentado ou diminuir os recursos de cluster quando a demanda cair.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Dimensionamento ou dimensionamento horizontal
Altera o número de nós no cluster.  Depois que os novos nós ingressam no cluster, o [Gerenciador de recursos de cluster](service-fabric-cluster-resource-manager-introduction.md) move os serviços para eles, o que reduz a carga nos nós existentes.  Você também pode diminuir o número de nós se os recursos do cluster não estiverem sendo usados com eficiência.  Como os nós deixam o cluster, os serviços saem desses nós e aumentam a carga nos nós restantes.  Reduzir o número de nós em um cluster em execução no Azure pode poupar dinheiro, pois você paga pelo número de VMs usadas e não pela carga de trabalho nessas VMs.  

- Principais Escala infinita, em teoria.  Se seu aplicativo for projetado para escalabilidade, você poderá habilitar o crescimento ilimitado adicionando mais nós.  As ferramentas em ambientes de nuvem facilitam a adição ou a remoção de nós, portanto, é fácil ajustar a capacidade e você só paga pelos recursos que usar.  
- Desvantagens Os aplicativos devem ser [projetados para escalabilidade](service-fabric-concepts-scalability.md).  Os bancos de dados de aplicativos e a persistência podem exigir um trabalho adicional de arquitetura para serem dimensionados também.  As [coleções confiáveis](service-fabric-reliable-services-reliable-collections.md) no Service Fabric serviços com estado, no entanto, tornam muito mais fácil dimensionar os dados do aplicativo.

Os clusters autônomos permitem que você implante Service Fabric cluster local ou no provedor de nuvem de sua escolha.  Os tipos de nó são compostos de máquinas físicas ou computadores virtuais, dependendo de sua implantação. Em comparação com os clusters em execução no Azure, o processo de dimensionamento de um cluster autônomo é um pouco mais envolvido.  Você deve alterar manualmente o número de nós no cluster e, em seguida, executar uma atualização de configuração de cluster.

A remoção de nós pode iniciar várias atualizações. Alguns nós são marcados com `IsSeedNode=”true”` marca e podem ser identificados consultando o manifesto do cluster usando [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest). A remoção desses nós pode levar mais tempo do que outros, já que os nós de semente precisarão ser movidos nesses cenários. O cluster deve manter um mínimo de três nós de tipo de nó primário.

> [!WARNING]
> Recomendamos que você não diminua a contagem de nós abaixo do [tamanho do cluster da camada de confiabilidade](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) do cluster. Isso irá interferir na capacidade de os serviços do sistema Service Fabric ser replicados pelo cluster e desestabilizar ou possivelmente destruir o cluster.
>

Ao dimensionar um cluster autônomo, tenha em mente as seguintes diretrizes:
- A substituição de nós primários deve ser executada em um nó após o outro, em vez de remover e depois adicionar em lotes.
- Antes de remover um tipo de nó, verifique se há nós que fazem referência ao tipo de nó. Remova esses nós antes de remover o tipo de nó correspondente. Depois que todos os nós correspondentes forem removidos, você poderá remover o NodeType da configuração do cluster e iniciar uma atualização de configuração usando [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade).

Para obter mais informações, consulte [dimensionar um cluster autônomo](service-fabric-cluster-windows-server-add-remove-nodes.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Dimensionamento vertical ou horizontal 
Altera os recursos (CPU, memória ou armazenamento) de nós no cluster.
- Principais A arquitetura de software e aplicativos permanece a mesma.
- Desvantagens Escala finita, já que há um limite para o quanto você pode aumentar os recursos em nós individuais. Tempo de inatividade, pois você precisará usar máquinas físicas ou virtuais offline para adicionar ou remover recursos.

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre a escalabilidade do [aplicativo](service-fabric-concepts-scalability.md).
* [Dimensionar ou reduzir um cluster do Azure](service-fabric-tutorial-scale-cluster.md).
* [Dimensione um cluster do Azure](service-fabric-cluster-programmatic-scaling.md) programaticamente usando o SDK de computação do Azure fluente.
* [Dimensionar ou reduzir um cluster autônomo](service-fabric-cluster-windows-server-add-remove-nodes.md).


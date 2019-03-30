---
title: Dimensionamento de clusters de autónomo de Service Fabric do Azure | Documentos da Microsoft
description: Saiba mais sobre o dimensionamento de clusters autónomos do Service Fabric no ou horizontalmente e ou reduzir verticalmente.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: aljo
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/13/2018
ms.author: dekapur
ms.openlocfilehash: 05049b9b08b4630c4299a6d3054c7815b082af52
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663388"
---
# <a name="scaling-service-fabric-standalone-clusters"></a>Dimensionar clusters autónomos do Service Fabric
Um cluster do Service Fabric é um conjunto ligado à rede de máquinas virtuais ou físicas, no qual os microsserviços são implementados e geridos. Uma máquina ou VM que faça parte de um cluster é chamado de nó. Clusters podem conter potencialmente milhares de nós. Depois de criar um cluster do Service Fabric, pode dimensionar o cluster horizontalmente (alterar o número de nós) ou na vertical (alterar os recursos de nós).  Pode dimensionar o cluster em qualquer altura, mesmo quando as cargas de trabalho em execução no cluster.  Como dimensiona o cluster, as aplicações são dimensionadas automaticamente também.

Por que motivo a dimensionar o cluster? Alteram pedidos de aplicações ao longo do tempo.  Poderá ter de aumentar os recursos do cluster para corresponder ao tráfego de rede ou de carga de trabalho de aplicações mais ou diminuir recursos do cluster quando a procura cair.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Dimensionamento e reduzir ou dimensionamento horizontal
Altera o número de nós do cluster.  Depois dos novos nós aderirem ao cluster, o [Resource Manager de Cluster](service-fabric-cluster-resource-manager-introduction.md) move serviços-lhes que reduz a carga em nós existentes.  Também pode diminuir o número de nós se não a recursos do cluster estão a ser utilizados com eficiência.  Como nós de deixam o cluster, os serviços sair de nós e carga aumenta em nós restantes.  Reduzindo o número de nós num cluster em execução no Azure pode economizar dinheiro, uma vez que paga o número de VMs que não a carga de trabalho nessas VMS e de utilização.  

- Vantagens: Dimensionamento infinito, na teoria.  Se seu aplicativo for desenvolvido para escalabilidade, pode ativar o crescimento ilimitado ao adicionar mais nós.  As ferramentas em ambientes de cloud torna mais fácil adicionar ou remover nós, portanto, é fácil de ajustar a capacidade e paga apenas pelos recursos que utilizar.  
- Desvantagens: Os aplicativos devem ser [concebida para escalabilidade e](service-fabric-concepts-scalability.md).  Bases de dados do aplicativo e persistência, podem exigir trabalho adicional de arquitetura para dimensionar também.  [As coleções fiáveis](service-fabric-reliable-services-reliable-collections.md) nos serviços de monitorização de estado do Service Fabric, no entanto, torná-lo muito mais fácil de dimensionar os dados da aplicação.

Clusters autónomos permitem implementar o Service Fabric cluster no local ou no fornecedor de cloud à sua escolha.  Tipos de nó são constituídos por máquinas físicas ou máquinas virtuais, dependendo da sua implementação. Em comparação com clusters em execução no Azure, o processo de dimensionar um cluster autónomo é um pouco mais envolvido.  Tem de alterar manualmente o número de nós do cluster e, em seguida, executar uma atualização de configuração de cluster.

Remoção de nós pode iniciar várias atualizações. Alguns nós são marcados com `IsSeedNode=”true”` Etiquetar e podem ser identificados através da consulta do cluster através do manifesto [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest). Remoção desses nós pode demorar mais tempo do que outras pessoas, visto que os nós de semente tem de ser movidos em torno em tais cenários. O cluster tem de manter um mínimo de três nós de tipo de nó primário.

> [!WARNING]
> Recomendamos que não a reduzir a contagem de nós seguir a [tamanho do Cluster do escalão de fiabilidade](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) para o cluster. Isto irá interferir com a capacidade dos serviços de sistema de recursos de infraestrutura do serviço ao ser replicada no cluster e que serão desestabilizar ou, possivelmente, destrua o cluster.
>

Quando dimensionar um cluster autónomo, mantenha as seguintes diretrizes em mente:
- A substituição de nós principal deve ser efetuado um nó após a outra, em vez de remover e, em seguida, adicionar em lotes.
- Antes de remover um tipo de nó, verifique se existem quaisquer nós a referenciar o tipo de nó. Remova estes nós antes de remover o tipo de nó correspondente. Depois de todos os nós correspondentes são removidos, pode remover o NodeType da configuração do cluster e começar a uma configuração de atualizar com o [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade).

Para obter mais informações, consulte [dimensionar um cluster autónomo](service-fabric-cluster-windows-server-add-remove-nodes.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Aumentar e reduzir verticalmente ou dimensionamento vertical 
Altera os recursos (CPU, memória ou armazenamento) de nós no cluster.
- Vantagens: Arquitetura de software e o aplicativo permanece o mesmo.
- Desvantagens: Escala finita, uma vez que existe um limite quanto pode aumentar os recursos em nós individuais. Tempo de inatividade, uma vez que precisará executar máquinas físicas ou virtuais offline para adicionar ou remover recursos.

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [escalabilidade do aplicativo](service-fabric-concepts-scalability.md).
* [Dimensionar um cluster do Azure e reduzir](service-fabric-tutorial-scale-cluster.md).
* [Dimensionar um cluster do Azure através de programação](service-fabric-cluster-programmatic-scaling.md) através do Azure fluent computação SDK.
* [Dimensionar um cluster autónomo e reduzir](service-fabric-cluster-windows-server-add-remove-nodes.md).


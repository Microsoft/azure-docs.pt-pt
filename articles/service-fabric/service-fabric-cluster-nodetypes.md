---
title: Conjuntos de dimensionamento de máquina virtual e tipos de nó do Service Fabric do Azure | Documentos da Microsoft
description: Saiba como define o nó do Azure Service Fabric tipos relacionam ao dimensionamento de máquinas virtuais e como ligar remotamente a um dimensionamento de conjunto de instância ou nó de cluster.
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: chackdan
ms.openlocfilehash: 7f9397ee21f74fe6a776881940e5721264216b0f
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58660619"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Conjuntos de dimensionamento de máquina virtual e tipos de nó do Service Fabric do Azure
[Os conjuntos de dimensionamento de máquinas virtuais](/azure/virtual-machine-scale-sets) são o recurso de computação do Azure. Pode utilizar os conjuntos de dimensionamento para implementar e gerir uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó que definem num cluster do Azure Service Fabric configura um dimensionamento separado.  Definir o tempo de execução do Service Fabric instalado em cada máquina virtual em escala. Forma independente pode aumentar cada tipo de nó ou reduzir verticalmente, alterar o SKU de SO em execução em cada nó de cluster, têm conjuntos diferentes de portas abertas e utilize métricas de capacidade diferente.

A figura seguinte mostra um cluster que tem dois tipos de nó, com o nome front-end e back-end. Cada tipo de nó tem cinco nós.

![Um cluster que tem dois tipos de nó][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Mapear instâncias do conjunto de dimensionamento de máquina virtual para nós
Conforme mostrado na figura anterior, instâncias do conjunto de dimensionamento começam em 0 da instância e, em seguida, aumentam em 1. A numeração é refletida nos nomes de nó. Por exemplo, o nó BackEnd_0 é 0 da instância de conjunto de dimensionamento de back-end. Este conjunto de dimensionamento específico tem cinco instâncias, com o nome BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 e BackEnd_4.

Ao aumentar verticalmente um conjunto de dimensionamento, é criada uma nova instância. O novo nome de instância de conjunto de dimensionamento é, normalmente, que o conjunto de dimensionamento nome e o número seguinte de instância. No nosso exemplo, é BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Mapear balanceadores de carga do conjunto de dimensionamento para tipos de nós e conjuntos de dimensionamento
Se implementou o cluster no portal do Azure ou utilizar o modelo do Azure Resource Manager de exemplo, são listados todos os recursos num grupo de recursos. Pode ver os balanceadores de carga para cada tipo de nó ou conjunto de dimensionamento. O nome do Balanceador de carga utiliza o seguinte formato: **LB -&lt;nome do tipo de nó&gt;**. Um exemplo é LB-sfcluster4doc-0, conforme mostrado na figura a seguir:

![Recursos][Resources]


## <a name="next-steps"></a>Passos Seguintes
* Consulte a [descrição geral do recurso "Implementar em qualquer lugar" e uma comparação com os clusters geridos pelo Azure](service-fabric-deploy-anywhere.md).
* Saiba mais sobre [segurança de cluster](service-fabric-cluster-security.md).
* [Ligar remotamente](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) para uma instância de conjunto de dimensionamento específico
* [Atualizar os valores de intervalo de portas RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) no cluster de VMs após a implementação
* [Alterar o nome de utilizador administrador e a palavra-passe](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) para VMs do cluster

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png

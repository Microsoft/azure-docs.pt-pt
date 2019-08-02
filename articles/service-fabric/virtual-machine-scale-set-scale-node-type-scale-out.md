---
title: Adicionar um tipo de nó a um cluster de Service Fabric do Azure | Microsoft Docs
description: Saiba como escalar horizontalmente um cluster de Service Fabric adicionando um conjunto de dimensionamento de máquinas virtuais.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/13/2019
ms.author: atsenthi
ms.openlocfilehash: 1414e656a358af1e258c823cc7ec747fefa986ba
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598686"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>Dimensionar um cluster de Service Fabric de saída adicionando um conjunto de dimensionamento de máquinas virtuais
Este artigo descreve como dimensionar um cluster de Service Fabric do Azure adicionando um novo tipo de nó a um cluster existente. Um Cluster Service Fabric é um conjunto de máquinas físicas ou virtuais conectadas à rede em que seus microserviços são implantados e gerenciados. Uma máquina ou VM que faz parte de um cluster é chamada de nó. Os conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure que você usa para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó definido em um cluster do Azure é [configurado como um conjunto de dimensionamento separado](service-fabric-cluster-nodetypes.md). Cada tipo de nó pode ser gerenciado separadamente. Depois de criar um Cluster Service Fabric, você pode dimensionar um cluster horizontalmente adicionando um novo tipo de nó (conjunto de dimensionamento de máquinas virtuais) a um cluster existente.  Você pode dimensionar o cluster a qualquer momento, mesmo quando as cargas de trabalho estiverem em execução no cluster.  À medida que o cluster é dimensionado, os aplicativos também são dimensionados automaticamente.

## <a name="add-an-additional-scale-set-to-an-existing-cluster"></a>Adicionar um conjunto de dimensionamento adicional a um cluster existente
Adicionar um novo tipo de nó (que é apoiado por um conjunto de dimensionamento de máquinas virtuais) a um cluster existente é semelhante à [atualização do tipo de nó primário](service-fabric-scale-up-node-type.md), exceto que você não usará o mesmo NodeTypeRef; Obviamente, não estará desabilitando nenhum conjunto de dimensionamento de máquinas virtuais ativamente utilizado, e você não perderá a disponibilidade do cluster se não atualizar o tipo de nó primário. 

A propriedade NodeTypeRef é declarada no conjunto de dimensionamento de máquinas virtuais Service Fabric Propriedades de extensão:
```json
<snip>
"publisher": "Microsoft.Azure.ServiceFabric",
     "settings": {
     "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
     "nodeTypeRef": "[parameters('vmNodeType2Name')]",
     "dataPath": "D:\\\\SvcFab",
     "durabilityLevel": "Silver",
<snip>
```

Além disso, você precisará adicionar esse novo tipo de nó ao seu Service Fabric recurso de cluster:

```json
<snip>
"nodeTypes": [
      {
      "name": "[parameters('vmNodeType2Name')]",
      "applicationPorts": {
                "endPort": "[parameters('nt2applicationEndPort')]",
                "startPort": "[parameters('nt2applicationStartPort')]"
      },
      "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
      "durabilityLevel": "Silver",
       "ephemeralPorts": {
                "endPort": "[parameters('nt2ephemeralEndPort')]",
                "startPort": "[parameters('nt2ephemeralStartPort')]"
      },
      "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
      "isPrimary": false,
      "vmInstanceCount": "[parameters('nt2InstanceCount')]"
},
<snip>
```

## <a name="next-steps"></a>Passos seguintes
* Saiba como [escalar verticalmente o tipo de nó primário](service-fabric-scale-up-node-type.md)
* Saiba mais sobre a escalabilidade do [aplicativo](service-fabric-concepts-scalability.md).
* [Dimensionar ou reduzir um cluster do Azure](service-fabric-tutorial-scale-cluster.md).
* [Dimensione um cluster do Azure](service-fabric-cluster-programmatic-scaling.md) programaticamente usando o SDK de computação do Azure fluente.
* [Dimensionar ou reduzir um cluster autônomo](service-fabric-cluster-windows-server-add-remove-nodes.md).


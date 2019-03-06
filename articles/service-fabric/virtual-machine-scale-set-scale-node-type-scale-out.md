---
title: Adicionar um tipo de nó a um cluster do Azure Service Fabric | Documentos da Microsoft
description: Saiba como aumentar horizontalmente um cluster do Service Fabric através da adição de um conjunto de dimensionamento de Máquina Virtual.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/13/2019
ms.author: aljo
ms.openlocfilehash: d732c26fd503f65bbd82bff076873ea5de4edb39
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57455603"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>Dimensionar um cluster do Service Fabric horizontalmente ao adicionar um conjunto de dimensionamento de máquinas virtuais
Este artigo descreve como dimensionar um cluster do Azure Service Fabric, adicionando um novo tipo de nó num cluster existente. Um cluster do Service Fabric é um conjunto ligado à rede de máquinas virtuais ou físicas, no qual os microsserviços são implementados e geridos. Uma máquina ou VM que faça parte de um cluster é chamado de nó. Os conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure que utilizar para implementar e gerir uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó que está definido num cluster do Azure é [configurado como um conjunto de dimensionamento separado](service-fabric-cluster-nodetypes.md). Cada tipo de nó pode ser gerido separadamente. Depois de criar um cluster do Service Fabric, pode dimensionar um cluster horizontalmente ao adicionar um novo tipo de nó (conjunto de dimensionamento de máquina virtual) a um cluster existente.  Pode dimensionar o cluster em qualquer altura, mesmo quando as cargas de trabalho em execução no cluster.  Como dimensiona o cluster, as aplicações são dimensionadas automaticamente também.

## <a name="add-an-additional-scale-set-to-an-existing-cluster"></a>Adicionar um dimensionamento adicional definido para um cluster existente
Adicionar um novo tipo de nó (o que é suportado por um conjunto de dimensionamento de máquinas virtuais) a um cluster existente é semelhante à [atualização do tipo de nó primário](service-fabric-scale-up-node-type.md), exceto que não utiliza o NodeTypeRef mesmo; obviamente, a não ser desabilitar qualquer utilizado ativamente conjuntos de dimensionamento de máquina virtual, e não perderá a disponibilidade do cluster se não atualizar o tipo de nó primário. 

O NodeTypeRef propriedade é declarada dentro da máquina virtual propriedades de extensão do Service Fabric do conjunto de dimensionamento:
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

Além disso precisará adicionar este novo tipo de nó para o recurso de cluster do Service Fabric:

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

## <a name="next-steps"></a>Passos Seguintes
* Saiba como [aumentar verticalmente o tipo de nó primário](service-fabric-scale-up-node-type.md)
* Saiba mais sobre [escalabilidade do aplicativo](service-fabric-concepts-scalability.md).
* [Dimensionar um cluster do Azure e reduzir](service-fabric-tutorial-scale-cluster.md).
* [Dimensionar um cluster do Azure através de programação](service-fabric-cluster-programmatic-scaling.md) através do Azure fluent computação SDK.
* [Dimensionar um cluster autónomo e reduzir](service-fabric-cluster-windows-server-add-remove-nodes.md).


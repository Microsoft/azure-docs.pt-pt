---
title: Adicione um tipo de nó a um cluster de tecido de serviço Azure
description: Aprenda a dimensionar um cluster de tecido de serviço adicionando um conjunto de escala de máquina virtual.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: 1e7bae89561d43d717eb4d15e95183761b077443
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463968"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>Escala um cluster de tecido de serviço, adicionando um conjunto de escala de máquina virtual
Este artigo descreve como escalar um cluster azure service fabric adicionando um novo tipo de nó a um cluster existente. Um cluster de tecido de serviço é um conjunto de máquinas virtuais ou físicas ligadas à rede, nas quais os seus microserviços são implantados e geridos. Uma máquina ou VM que faz parte de um aglomerado é chamado de nó. Os conjuntos de escala de máquinas virtuais são um recurso de computação Azure que utiliza para implantar e gerir uma coleção de máquinas virtuais como conjunto. Cada tipo de nó definido num cluster Azure é [configurado como um conjunto](service-fabric-cluster-nodetypes.md)de escala separado . Cada tipo de nó pode então ser gerido separadamente. Depois de criar um cluster de Tecido de Serviço, pode escalar um cluster horizontalmente adicionando um novo tipo de nó (conjunto de escala de máquina virtual) a um cluster existente.  Pode escalar o cluster a qualquer momento, mesmo quando as cargas de trabalho estão a decorrer no cluster.  À medida que o cluster escala, as suas aplicações também escalam automaticamente.

## <a name="add-an-additional-scale-set-to-an-existing-cluster"></a>Adicione um conjunto de escala adicional a um cluster existente
Adicionar um novo tipo de nó (que é apoiado por um conjunto de escala de máquina virtual) a um cluster existente é semelhante à [atualização do tipo](service-fabric-scale-up-node-type.md)de nó primário, exceto que não utilizará o mesmo NodeTypeRef; Obviamente não vai desativar quaisquer conjuntos de escala de máquinavirtual ativamente utilizados, e você não perderá a disponibilidade do cluster se não atualizar o tipo de nó primário. 

A propriedade NodeTypeRef é declarada dentro das propriedades de extensão de extensão de tecido de conjunto de máquina virtual:
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

Além disso, terá de adicionar este novo tipo de nó ao seu recurso de cluster Service Fabric:

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
* Aprenda a [escalar o tipo de nó primário](service-fabric-scale-up-node-type.md)
* Saiba mais sobre [a escalabilidade da aplicação.](service-fabric-concepts-scalability.md)
* [Escala um aglomerado Azure para dentro ou para fora.](service-fabric-tutorial-scale-cluster.md)
* [Dimensione um cluster Azure programáticamente](service-fabric-cluster-programmatic-scaling.md) utilizando o fluente sdk de computação Azure.
* [Escala um aglomerado autónomo dentro ou fora.](service-fabric-cluster-windows-server-add-remove-nodes.md)


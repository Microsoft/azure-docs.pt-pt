---
title: Adicione um tipo de nó a um cluster de tecido de serviço Azure
description: Aprenda a escalar um cluster de Tecido de Serviço adicionando um Conjunto de Escala de Máquina Virtual.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: efd329c07b4881c6710d4173857b4186965438d8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88719330"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>Dimensione um cluster de tecido de serviço adicionando um conjunto de escala de máquina virtual
Este artigo descreve como escalar um cluster de tecido de serviço Azure adicionando um novo tipo de nó a um cluster existente. Um cluster de tecido de serviço é um conjunto de máquinas virtuais ou físicas ligadas à rede em que os seus microserviços são implantados e geridos. Uma máquina ou VM que faz parte de um aglomerado é chamada de nó. Os conjuntos de escala de máquinas virtuais são um recurso computacional Azure que utiliza para implementar e gerir uma coleção de máquinas virtuais como conjunto. Todos os tipos de nó definidos num cluster Azure [são configurado como um conjunto de escala separada](service-fabric-cluster-nodetypes.md). Cada tipo de nó pode então ser gerido separadamente. Depois de criar um cluster de Tecido de Serviço, pode escalar um cluster horizontalmente adicionando um novo tipo de nó (conjunto de balança de máquina virtual) a um cluster existente.  Pode escalar o cluster a qualquer momento, mesmo quando as cargas de trabalho estão a funcionar no cluster.  À medida que o cluster escala, as suas aplicações também escalam automaticamente.

## <a name="add-an-additional-scale-set-to-an-existing-cluster"></a>Adicione uma escala adicional definida a um cluster existente
Adicionar um novo tipo de nó (que é apoiado por um conjunto de escala de máquina virtual) a um cluster existente é semelhante ao [upgrade do tipo de nó primário](service-fabric-scale-up-primary-node-type.md), exceto que não utilizará o mesmo NodeTypeRef; obviamente não estará a desativar conjuntos de escala de máquinas virtuais ativamente utilizados, e não perderá a disponibilidade do cluster se não atualizar o tipo de nó primário. 

A propriedade NodeTypeRef é declarada dentro da escala virtual de escala de capacidade De serviço Propriedades de extensão de tecido de serviço:
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

Além disso, você precisará adicionar este novo tipo de nó ao seu recurso de cluster de Tecido de Serviço:

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
* Saiba como [escalar o tipo de nó primário](service-fabric-scale-up-primary-node-type.md)
* Saiba mais sobre [a escalabilidade da aplicação.](service-fabric-concepts-scalability.md)
* [Escalar um aglomerado Azure dentro ou fora](service-fabric-tutorial-scale-cluster.md).
* [Dimensione um cluster Azure programáticamente](service-fabric-cluster-programmatic-scaling.md) usando o fluente Azure compute SDK.
* [Escalar um aglomerado autónomo para dentro ou para fora](service-fabric-cluster-windows-server-add-remove-nodes.md).


---
title: Service Fabric Azure Resource Manager guarda-costas
description: Este artigo fornece uma visão geral dos erros comuns cometidos ao implantar um cluster de Tecido de Serviço através do Gestor de Recursos Azure e como evitá-los.
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: pepogors
ms.openlocfilehash: a61b0cf30ca46eb77837eb09d6a9a0b6f30e89a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368574"
---
# <a name="service-fabric-guardrails"></a>Guarda-costas de tecido de serviço 
Ao implantar um cluster de tecido de serviço, os guarda-costas são colocados no lugar, o que falhará uma implementação do Gestor de Recursos Azure no caso de uma configuração de cluster inválida. As seguintes secções fornecem uma visão geral dos problemas comuns de configuração do cluster e dos passos necessários para mitigar estas questões. 

## <a name="durability-mismatch"></a>Desfasamento de durabilidade
### <a name="overview"></a>Descrição geral
O valor de durabilidade de um tipo de nó de tecido de serviço é definido em duas secções diferentes de um modelo de Gestor de Recursos Azure. A secção de extensão de conjunto de escala de máquina virtual do recurso conjunto de escala de máquina virtual e a secção tipo nó do recurso de cluster de tecido de serviço. É um requisito que o valor de durabilidade nestas secções corresponda, caso contrário a implantação de recursos falhará.

A secção seguinte contém um exemplo de um desfasamento de durabilidade entre a definição de durabilidade da extensão da escala de máquina virtual e a definição de durabilidade do tipo de nó de tecido de serviço:  

**Definição de durabilidade da escala de máquina virtual**
```json 
{
  "extensions": [
    {
      "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
      "properties": {
        "type": "ServiceFabricNode",
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
          "durabilityLevel": "Silver",
        }
      }
    }
  ]
}
```

**Definição de durabilidade do tipo de nó de tecido de serviço** 
```json
{
  "nodeTypes": [
    {
      "name": "[variables('vmNodeType0Name')]",
      "durabilityLevel": "Bronze",
      "isPrimary": true,
      "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    }
  ]
}
```

### <a name="error-messages"></a>Mensagens de erro
* O desfasamento da durabilidade da balança de máquinavirtual não corresponde ao nível atual de durabilidade do tipo de nó de tecido de serviço
* A durabilidade do conjunto de escala de máquina virtual não corresponde ao nível de durabilidade do tipo de nó de tecido de serviço alvo
* A durabilidade do conjunto de conjunto de máquinas virtuais corresponde ao nível atual de durabilidade do tecido de serviço ou ao nível de durabilidade do tipo de nó de tecido de serviço alvo 

### <a name="mitigation"></a>Mitigação
Para corrigir uma incompatibilidade de durabilidade, que é indicada por qualquer uma das mensagens de erro acima:
1. Atualize o nível de durabilidade na extensão do conjunto de escala de máquina virtual ou na secção tipo de nó de tecido de serviço do modelo Do Gestor de Recursos Azure para garantir que os valores correspondem.
2. Reutilizar o modelo do Gestor de Recursos Azure com os valores atualizados.


## <a name="seed-node-deletion"></a>Eliminação do nó de sementes 
### <a name="overview"></a>Descrição geral
Um cluster de tecido de serviço tem uma propriedade [de nível](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) de fiabilidade que é usada para determinar o número de réplicas de serviços de sistema que funcionam no tipo principal do nó do cluster. O número de réplicas necessárias determinará o número mínimo de nós que deve ser mantido no tipo principal do nó do cluster. Se o número de nós no nó primário ficar abaixo do mínimo exigido para o nível de fiabilidade, o cluster tornar-se-á instável.  

### <a name="error-messages"></a>Mensagens de erro 
A operação de remoção do nó de sementes foi detetada e será rejeitada. 
* Esta operação resultaria {0} apenas em potenciais nós de sementes {1} a permanecer no aglomerado, enquanto necessário sê-lo como mínimo.
* Remover {0} os nós de {1} sementes resultaria na queda do aglomerado devido à perda de quórum node de sementes. O número máximo de nós de sementes que {2}podem ser removidos de cada vez é .
 
### <a name="mitigation"></a>Mitigação 
Certifique-se de que o seu tipo principal de nó tem máquinas virtuais suficientes para a fiabilidade especificada no seu cluster. Não poderá remover uma Máquina Virtual se colocar o Conjunto de Escala de Máquina Virtual abaixo do número mínimo de nós para o determinado nível de fiabilidade.
* Se o nível de fiabilidade estiver corretamente especificado, certifique-se de que tem nós suficientes no nó primário, conforme necessário para o nível de fiabilidade. 
* Se o nível de fiabilidade estiver incorreto, inicie uma alteração no recurso Service Fabric para baixar primeiro o nível de fiabilidade antes de iniciar quaisquer operações de conjunto de escala de máquina virtual e aguarde que esteja concluída.
* Se o nível de fiabilidade for Bronze, por favor siga estes [passos](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#manually-remove-vms-from-a-node-typevirtual-machine-scale-set) para reduzir graciosamente o seu cluster.

## <a name="next-steps"></a>Passos seguintes
* Criar um cluster em VMs ou computadores que executam o Windows Server: [Criação](service-fabric-cluster-creation-for-windows-server.md) de cluster de tecido de serviço para Windows Server
* Criar um cluster em VMs ou computadores em execução linux: [Criar um cluster Linux](service-fabric-cluster-creation-via-portal.md)
* Tecido de serviço de resolução de [problemas: guias de resolução de problemas](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)

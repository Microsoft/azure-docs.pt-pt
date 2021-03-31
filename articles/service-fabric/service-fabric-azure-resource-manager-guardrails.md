---
title: Guarda-costas de implementação do Gestor de Recursos Azure do Tecido de Serviço
description: Este artigo fornece uma visão geral dos erros comuns cometidos ao implementar um cluster de Tecidos de Serviço através do Azure Resource Manager e como evitá-los.
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: pepogors
ms.openlocfilehash: aa1499c57ead28bfcee90a2f224ef9c3bb1d7f58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86247829"
---
# <a name="service-fabric-guardrails"></a>Guarda-costas de tecido de serviço 
Ao implementar um cluster de tecido de serviço, são colocados guarda-costas, o que falhará na implementação de um Gestor de Recursos Azure no caso de uma configuração de cluster inválida. As secções seguintes fornecem uma visão geral das questões comuns de configuração do cluster e dos passos necessários para mitigar estas questões. 

## <a name="durability-mismatch"></a>Incompatibilidade de durabilidade
### <a name="overview"></a>Descrição Geral
O valor de durabilidade para um tipo de nó de tecido de serviço é definido em duas secções diferentes de um modelo de Gestor de Recursos Azure. A secção de extensão de escala de máquina virtual do recurso Conjunto de Escala de Máquina Virtual e a secção Tipo de Nó do recurso cluster de tecido de serviço. É um requisito que o valor de durabilidade nestas secções corresponda, caso contrário a implementação do recurso falhará.

A seguinte secção contém um exemplo de uma incompatibilidade de durabilidade entre a definição de durabilidade da escala de máquina virtual e a definição de durabilidade do tipo de nó de serviço:  

**Escala de máquina virtual Definir durabilidade**
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

**Configuração de durabilidade do tipo de nó de tecido de serviço** 
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
* Escala de máquina virtual Desajuste de durabilidade não corresponde ao nível de durabilidade do tipo de durabilidade do tipo de tecido de serviço atual
* Escala de máquina virtual A durabilidade do conjunto de escala não corresponde ao nível de durabilidade do tipo de durabilidade do tipo de tecido de serviço alvo
* Escala de máquina virtual A durabilidade do conjunto de durabilidade corresponde ao nível de durabilidade do tecido de serviço atual ou ao nível de durabilidade do tipo de durabilidade do tipo de tecido de serviço alvo 

### <a name="mitigation"></a>Mitigação
Para corrigir uma incompatibilidade de durabilidade, que é indicada por qualquer uma das mensagens de erro acima:
1. Atualize o nível de durabilidade na extensão virtual do Conjunto de Escala de Máquina ou na secção do tipo de nó de tecido de serviço do modelo Azure Resource Manager para garantir que os valores correspondam.
2. Reimplantar o modelo do Gestor de Recursos Azure com os valores atualizados.


## <a name="seed-node-deletion"></a>Supressão do nó de sementes 
### <a name="overview"></a>Descrição Geral
Um cluster de tecido de serviço tem uma propriedade [de nível de fiabilidade](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) que é usada para determinar o número de réplicas de serviços de sistema que funcionam no tipo de nó primário do cluster. O número de réplicas necessárias determinará o número mínimo de nós que devem ser mantidos no tipo de nó primário do cluster. Se o número de nós no tipo de nó primário for inferior ao mínimo exigido para o nível de fiabilidade, o cluster tornar-se-á instável.  

### <a name="error-messages"></a>Mensagens de erro 
A operação de remoção do nó de sementes foi detetada e será rejeitada. 
* Esta operação resultaria apenas em {0} potenciais nós de sementes para permanecer no cluster, enquanto {1} necessários no mínimo.
* A remoção {0} dos nós de sementes resultaria na queda do aglomerado devido à perda do {1} quórum do nó de sementes. O número máximo de nós de sementes que podem ser removidos de cada vez é {2} .
 
### <a name="mitigation"></a>Mitigação 
Certifique-se de que o seu tipo de nó primário tem máquinas virtuais suficientes para a fiabilidade especificada no seu cluster. Não será possível remover uma Máquina Virtual se colocar o Conjunto de Balanças de Máquina Virtual abaixo do número mínimo de nós para o nível de fiabilidade dado.
* Se o nível de fiabilidade estiver corretamente especificado, certifique-se de que tem nós suficientes no tipo de nó primário, conforme necessário para o nível de fiabilidade. 
* Se o nível de fiabilidade estiver incorreto, inicie uma alteração no recurso 'Tecido de Serviço' para baixar primeiro o nível de fiabilidade antes de iniciar quaisquer operações de Conjunto de Escala de Máquina Virtual e aguarde que esteja concluído.
* Se o nível de fiabilidade for bronze, siga estes [passos](./service-fabric-cluster-scale-in-out.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set) para escalar graciosamente no seu agrupamento.

## <a name="next-steps"></a>Passos seguintes
* Criar um cluster em VMs ou computadores que executam o Windows Server: [Criação de cluster de tecido de serviço para windows server](service-fabric-cluster-creation-for-windows-server.md)
* Criar um cluster em VMs ou computadores que executam o Linux: [Criar um cluster Linux](service-fabric-cluster-creation-via-portal.md)
* Tecido de serviço de resolução [de problemas: guias de resolução de problemas](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)

---
title: Tipos de nós e conjuntos de dimensionamento de máquinas virtuais
description: Saiba como os tipos de nó de tecido de serviço Azure se relacionam com conjuntos de escala de máquina virtual e como ligar remotamente a uma instância de conjunto de escala ou nó de cluster.
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: pepogors
ms.custom: sfrev
ms.openlocfilehash: 37d4c27d3033545c523cefc2f317073af531f095
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78199721"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Tipos de nó de tecido de serviço Azure e conjuntos de escala de máquina virtual

[Os conjuntos](/azure/virtual-machine-scale-sets) de escala de máquinas virtuais são um recurso de computação Azure. Pode utilizar conjuntos de escala para implantar e gerir uma coleção de máquinas virtuais como conjunto. Cada tipo de nó que define num cluster de tecido de serviço Azure configura exatamente um conjunto de escala: vários tipos de nó não podem ser apoiados pelo mesmo conjunto de escala e um tipo de nó não deve (na maioria dos casos) ser apoiado por conjuntos de escala múltipla. Uma exceção a isso é na rara situação de [escala vertical](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations) de um tipo de `nodeTypeRef` nó, quando você tem temporariamente dois conjuntos de escala com o mesmo valor enquanto as réplicas são migradas do original para o conjunto de escala atualizado.

O tempo de execução do Tecido de Serviço está instalado em cada máquina virtual na escala definida pela extensão *Microsoft.Azure.ServiceFabric* Virtual Machine. Pode escalar de forma independente cada nó para cima ou para baixo, alterar o OS SKU em cada nó de cluster, ter diferentes conjuntos de portas abertas e usar métricas de capacidade diferentes.

A figura que se segue mostra um cluster que tem dois tipos de nó, chamado *FrontEnd* e *BackEnd*. Cada nó tem cinco nós.

![Um aglomerado que tem dois tipos de nó][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Map virtual machine scale set instâncias para nós

Como mostrado na figura anterior, as instâncias de conjunto de escala começam na instância 0 e depois aumentam em 1. A numeração reflete-se nos nomes do nó. Por exemplo, o nó BackEnd_0 é a instância 0 do conjunto de escala BackEnd. Este conjunto de escala seleções em particular tem cinco instâncias, chamadas BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 e BackEnd_4.

Quando se escala um conjunto de escala, cria-se um novo exemplo. O nome da nova instância de escala normalmente é o nome definido de escala mais o número de instância seguinte. No nosso exemplo, é BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Conjunto de conjuntode balança de mapas para tipos de nó e conjuntos de escala

Se implementou o seu cluster no portal Azure ou usou o modelo de gestor de recursos Azure, todos os recursos sob um grupo de recursos estão listados. Pode ver os equilibradores de carga para cada conjunto de calcário ou tipo de nó. O nome do equilíbrio de carga utiliza o seguinte formato: **nome&lt;&gt;do tipo LB-nó**. Um exemplo é LB-sfcluster4doc-0, como mostra a seguinte figura:

![Recursos][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Extensão da máquina virtual de tecido de serviço

A extensão da máquina virtual de tecido de serviço é usada para botas de tecido de serviço para máquinas virtuais Azure e configurar a Segurança do Nó.

Segue-se um excerto da extensão da máquina virtual de tecido de serviço:

```json
"extensions": [
  {
    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
    "properties": {
      "type": "ServiceFabricLinuxNode",
      "autoUpgradeMinorVersion": true,
      "protectedSettings": {
        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
       },
       "publisher": "Microsoft.Azure.ServiceFabric",
       "settings": {
         "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
         "nodeTypeRef": "[variables('vmNodeType0Name')]",
         "durabilityLevel": "Silver",
         "enableParallelJobs": true,
         "nicPrefixOverride": "[variables('subnet0Prefix')]",
         "dataPath": "D:\\\\SvcFab",
         "certificate": {
           "commonNames": [
             "[parameters('certificateCommonName')]"
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
         }
       },
       "typeHandlerVersion": "1.1"
     }
   },
```

Seguem-se as descrições da propriedade:

| **Nome** | **Valores Permitidos** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
| nome | string | Nome único para extensão |
| tipo | "ServiceFabricLinuxNode" ou "ServiceFabricWindowsNode" | Identifica o Tecido de Serviço os si |
| autoUpgradeMinorVersion | true ou false | Ativar atualização automática de versões menores de prazo de execução SF |
| publicador | Microsoft.Azure.ServiceFabric | Nome do editor de extensão de tecido de serviço |
| clusterEndpont | string | URI:PORTO para ponto final de gestão |
| nóTypeRef | string | Nome do nóType |
| durabilidadeN | bronze, prata, ouro, platina | Tempo permitido para parar infraestrutura supreendível Azure |
| enableParallelJobs | true ou false | Ativar Compute ParallelJobs como remover VM e reiniciar VM na mesma escala definida em paralelo |
| nicPrefixOverride | string | Prefixo de sub-rede como "10.0.0.0.0/24" |
| nomes comuns | cadeia[] | Nomes comuns dos certificados de cluster instalados |
| x509Nome da loja | string | Nome da Loja onde está localizado certificado de cluster instalado |
| typeHandlerVersion | 1.1 | Versão da Extensão. 1.0 versão clássica da extensão é recomendada para atualizar para 1.1 |
| dataPath | string | Caminho para a unidade utilizada para salvar o estado dos serviços de sistema de tecido de serviço e dados de aplicação.

## <a name="next-steps"></a>Passos seguintes

* Veja a [visão geral da funcionalidade "Implementar em qualquer lugar" e uma comparação com clusters geridos pelo Azure](service-fabric-deploy-anywhere.md).
* Saiba mais sobre [a segurança do cluster.](service-fabric-cluster-security.md)
* [Ligação remota](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) a uma instância específica de conjunto de escala
* [Atualizar os valores da gama de porta rdp](./scripts/service-fabric-powershell-change-rdp-port-range.md) em VMs de cluster após a implantação
* Alterar o nome de utilizador e a [palavra-passe](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) do administrador para VMs de cluster

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png

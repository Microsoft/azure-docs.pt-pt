---
title: Tipos de nós e conjuntos de dimensionamento de máquinas virtuais
description: Saiba como os tipos de nós de tecido de serviço Azure se relacionam com conjuntos de balanças de máquinas virtuais e como ligar remotamente a uma instância definida de escala ou nó de cluster.
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: pepogors
ms.openlocfilehash: 9e30c02de54806006a1881448bcb9f788a57310c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97095258"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Tipos de nó de nó de tecido de serviço Azure e conjuntos de escala de máquina virtual

[Os conjuntos de escala de máquina virtual](../virtual-machine-scale-sets/index.yml) são um recurso de computação Azure. Pode utilizar conjuntos de escala para implantar e gerir uma coleção de máquinas virtuais como conjunto. Cada tipo de nó que define num cluster de tecido de serviço Azure configura exatamente um conjunto de escala: vários tipos de nós não podem ser apoiados pelo mesmo conjunto de escala e um tipo de nó não deve ser apoiado por conjuntos de escala múltipla.

O tempo de funcionamento do Tecido de Serviço é instalado em cada máquina virtual na escala definida pela extensão da Máquina Virtual *Microsoft.Azure.ServiceFabric.* Pode escalar independentemente cada tipo de nó para cima ou para baixo, alterar o SISTEMA SKU funcionando em cada nó de cluster, ter diferentes conjuntos de portas abertas e usar diferentes métricas de capacidade.

A figura a seguir mostra um cluster que tem dois tipos de nós, *chamados FrontEnd* e *BackEnd*. Cada nó tem cinco nós.

![Um cluster que tem dois tipos de nó][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Mapear instâncias de escala de máquina virtual para os nónádisscos

Como mostrado no valor anterior, as instâncias definidas em escala começam no exemplo 0 e, em seguida, aumentam em 1. A numeração reflete-se nos nomes dos nóns. Por exemplo, o nó BackEnd_0 é o exemplo 0 do conjunto de escala BackEnd. Este conjunto de escalas particulares tem cinco instâncias, denominada BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 e BackEnd_4.

Quando se escala um conjunto de escala, cria-se um novo exemplo. O nome de instância definida de nova escala normalmente é o nome definido da escala mais o número de instância seguinte. No nosso exemplo, é BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Balança de escala de mapas conjunto de balanças para tipos de nó e conjuntos de escala

Se implementou o seu cluster no portal Azure ou utilizou o modelo de gestor de recursos Azure, todos os recursos sob um grupo de recursos estão listados. Pode ver os balançadores de carga para cada conjunto de balanças ou tipo de nó. O nome do balançador de carga utiliza o seguinte formato: **&lt; &gt; nome do tipo de nó LB**. Um exemplo é LB-scluster4doc-0, como mostra a seguinte figura:

![O Screenshot mostra um grupo de recursos com dois equilibradores de carga em destaque.][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Extensão da máquina virtual do tecido de serviço

A extensão da máquina virtual do tecido de serviço de serviço é utilizada para o tecido de serviço de botas para a azure máquinas virtuais e configurar a Segurança do Nó.

Segue-se um corte da extensão da Máquina Virtual de Tecido de Serviço:

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
| name | string | Nome único para extensão |
| tipo | "ServiceFabricLinuxNode" ou "ServiceFabricWindowsNode" | Identifie o tecido de serviço oss está a ser armadilhada para |
| autoUpgradeMinorVersion | true ou false | Ativar a atualização automática das versões menores de tempo de execução SF |
| publicador | Microsoft.Azure.ServiceFabric | Nome da editora de extensão de Tecido de Serviço |
| clusterEndpont | string | URI:PORT para Management endpoint |
| nodeTypeRef | string | Nome do nóType |
| durabilidadeLevel | bronze, prata, ouro, platina | Tempo permitido para a infraestrutura azure imutável |
| ativar Os Trabalhos DeParallel | true ou false | Ativar Compute ParallelJobs como remover VM e reiniciar VM na mesma escala definida em paralelo |
| nicPrefixOverride | string | Prefixo de sub-rede como "10.0.0.0/24" |
| nomes comuns | corda[] | Nomes comuns dos certificados de agrupamento instalados |
| x509StoreName | string | Nome da Loja onde está localizado o certificado de cluster instalado |
| typeHandlerVersion | 1.1 | Versão de Extensão. 1.0 versão clássica de extensão são recomendadas para upgrade para 1.1 |
| dataPata | string | Caminho para a unidade usada para guardar o estado para serviços de sistema de tecido de serviço e dados de aplicação.

## <a name="next-steps"></a>Passos seguintes

* Consulte a [visão geral da funcionalidade "Implementar em qualquer lugar" e uma comparação com os clusters geridos pelo Azure.](service-fabric-deploy-anywhere.md)
* Conheça a [segurança do cluster.](service-fabric-cluster-security.md)
* [Ligação remota](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) a uma instância definida de escala específica
* [Atualizar os valores da gama de portas RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) nos VMs do cluster após a implantação
* [Alterar o nome de utilizador e a palavra-passe](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) do administrador para os VMs do cluster

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png

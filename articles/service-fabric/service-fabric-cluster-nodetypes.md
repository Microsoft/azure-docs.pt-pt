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
ms.openlocfilehash: a5f8735df2b230de2b0ddcdcccff09430bada9e3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64684679"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Conjuntos de dimensionamento de máquina virtual e tipos de nó do Service Fabric do Azure
[Os conjuntos de dimensionamento de máquinas virtuais](/azure/virtual-machine-scale-sets) são o recurso de computação do Azure. Pode utilizar os conjuntos de dimensionamento para implementar e gerir uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó que definem num cluster do Azure Service Fabric configura um dimensionamento separado.  O runtime do Service Fabric instalado em cada máquina virtual no conjunto de dimensionamento na extensão de máquina de Virtual Microsoft.Azure.ServiceFabric. Forma independente pode aumentar cada tipo de nó ou reduzir verticalmente, alterar o SKU de SO em execução em cada nó de cluster, têm conjuntos diferentes de portas abertas e utilize métricas de capacidade diferente.

A figura seguinte mostra um cluster que tem dois tipos de nó, com o nome front-end e back-end. Cada tipo de nó tem cinco nós.

![Um cluster que tem dois tipos de nó][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Mapear instâncias do conjunto de dimensionamento de máquina virtual para nós
Conforme mostrado na figura anterior, instâncias do conjunto de dimensionamento começam em 0 da instância e, em seguida, aumentam em 1. A numeração é refletida nos nomes de nó. Por exemplo, o nó BackEnd_0 é 0 da instância de conjunto de dimensionamento de back-end. Este conjunto de dimensionamento específico tem cinco instâncias, com o nome BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 e BackEnd_4.

Ao aumentar verticalmente um conjunto de dimensionamento, é criada uma nova instância. O novo nome de instância de conjunto de dimensionamento é, normalmente, que o conjunto de dimensionamento nome e o número seguinte de instância. No nosso exemplo, é BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Mapear balanceadores de carga do conjunto de dimensionamento para tipos de nós e conjuntos de dimensionamento
Se implementou o cluster no portal do Azure ou utilizar o modelo do Azure Resource Manager de exemplo, são listados todos os recursos num grupo de recursos. Pode ver os balanceadores de carga para cada tipo de nó ou conjunto de dimensionamento. O nome do Balanceador de carga utiliza o seguinte formato: **LB -&lt;nome do tipo de nó&gt;**. Um exemplo é LB-sfcluster4doc-0, conforme mostrado na figura a seguir:

![Recursos][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Extensão de Máquina Virtual do Service Fabric
Extensão de Máquina Virtual do Service Fabric é utilizado para iniciar o Service Fabric para máquinas de virtuais do Azure e configurar a segurança de nó.

Este é um trecho de código de extensão da máquina de Virtual de recursos de infraestrutura do serviço:

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

Seguem-se as descrições de propriedade:

| **Nome** | **Valores permitidos** | ** --- ** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
| nome | string | --- | nome exclusivo para a extensão |
| tipo | "ServiceFabricLinuxNode" or "ServiceFabricWindowsNode | --- | Identifica recursos de infraestrutura de serviço de sistema operacional é de inicialização para |
| autoUpgradeMinorVersion | VERDADEIRO ou FALSO | --- | Ativar atualização automática do Runtime de SF a versões secundárias |
| publicador | Microsoft.Azure.ServiceFabric | --- | nome do publicador da extensão de recursos de infraestrutura do serviço |
| clusterEndpont | string | --- | URI:Port para o ponto final de gestão |
| nodeTypeRef | string | --- | nome do nodeType |
| durabilityLevel | bronze, prata, ouro e platina | --- | tempo permitido para colocar em pausa infraestrutura imutável do Azure |
| enableParallelJobs | VERDADEIRO ou FALSO | --- | Ativar ParallelJobs de computação, como remover a VM e a VM no mesmo conjunto de dimensionamento em paralelo |
| nicPrefixOverride | string | --- | Prefixo de sub-rede, como "10.0.0.0/24" |
| commonNames | string[] | --- | Nomes comuns dos certificados de cluster instalado |
| x509StoreName | string | --- | Nome do Store onde está localizado o certificado de cluster instalado |
| typeHandlerVersion | 1.1 | --- | Versão da extensão. versão clássica 1.0 da extensão são recomendadas para atualizar para o 1.1 |

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

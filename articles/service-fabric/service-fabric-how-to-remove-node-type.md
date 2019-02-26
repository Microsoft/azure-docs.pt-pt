---
title: Remover um tipo de nó no Azure Service Fabric | Documentos da Microsoft
description: Saiba como remover um tipo de nó de cluster do Service Fabric em execução no Azure.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: aljo
ms.openlocfilehash: 2bde95b744ac136e8ba5c0517e0f749a6dce8a1e
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805278"
---
# <a name="remove-a-service-fabric-node-type"></a>Remover um tipo de nó do Service Fabric
Este artigo descreve como dimensionar um cluster do Azure Service Fabric através da remoção de um tipo de nó existente de um cluster. Um cluster do Service Fabric é um conjunto ligado à rede de máquinas virtuais ou físicas, no qual os microsserviços são implementados e geridos. Uma máquina ou VM que faça parte de um cluster é chamado de nó. Os conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure que utilizar para implementar e gerir uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó que está definido num cluster do Azure é [configurado como um conjunto de dimensionamento separado](service-fabric-cluster-nodetypes.md). Cada tipo de nó pode ser gerido separadamente. Depois de criar um cluster do Service Fabric, pode dimensionar um cluster horizontalmente ao remover um tipo de nó (conjunto de dimensionamento de máquina virtual) e todos nós do mesmo.  Pode dimensionar o cluster em qualquer altura, mesmo quando as cargas de trabalho em execução no cluster.  Como dimensiona o cluster, as aplicações são dimensionadas automaticamente também.

Uso [Remove-AzureRmServiceFabricNodeType](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype) para remover um tipo de nó do Service Fabric.

As três operações que ocorrem quando é chamado Remove-AzureRmServiceFabricNodeType são:
1.  O conjunto de dimensionamento atrás do tipo de nó é eliminado.
2.  O tipo de nó é removido do cluster.
3.  Para cada nó dentro desse tipo de nó, todo o estado para esse nó é removido do sistema. Se existirem serviços nesse nó, em seguida, os serviços são primeiro movidos para outro nó. Se o Gestor de clusters não é possível encontrar um nó para o serviço/réplica, em seguida, a operação é atrasado/bloqueado.

> [!WARNING]
> Utilizar Remove-AzureRmServiceFabricNodeType remover um tipo de nó de um cluster de produção não é recomendada a ser utilizada com frequência. É um comando perigoso como elimina o recurso de conjunto de dimensionamento de máquina virtual por trás do tipo de nó. 

## <a name="durability-characteristics"></a>Características de durabilidade
Segurança é priorizada em relação a velocidade, ao utilizar Remove-AzureRmServiceFabricNodeType o limite de tempo. O tipo de nó tem de ser Gold ou Silver [nível de durabilidade](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster), porque:
- Bronze não dão-lhe oferece garantias sobre ao guardar as informações de estado.
- Silver e Gold durabilidade interceptar todas as alterações ao conjunto de dimensionamento.
- Ouro também lhe dá controle sobre o Azure atualiza por baixo do conjunto de dimensionamento.

Service Fabric "orquestra" subjacentes de alterações e atualizações para que os dados não são perdidos. No entanto, ao remover um nó com durabilidade de Bronze, pode perder as informações de estado. Se está removendo a um tipo de nó primário e a sua aplicação está sem monitoração de estado, Bronze é aceitável. Ao executar cargas de trabalho com monitorização de estado em produção, a configuração mínima deve ser Silver. Da mesma forma, para cenários de produção do tipo de nó principal deve ser sempre Gold ou Silver.

### <a name="more-about-bronze-durability"></a>Mais informações sobre a durabilidade de Bronze

Quando remover um tipo de nó é Bronze, todos os nós num tipo de nó descer imediatamente. Service Fabric não intercetar quaisquer atualizações de conjunto de dimensionamento de nós de Bronze, portanto, todas as VMs descer imediatamente. Se tiver qualquer coisa com monitoração de estado em nós, os dados são perdidos. Agora, mesmo se fosse sem monitoração de estado, todos os nós nos recursos de infraestrutura do serviço de participam no anel, portanto, uma vizinhança inteira pode ser perdida, que poderá desestabilizar o próprio cluster.

## <a name="recommended-node-type-removal-process"></a>Recomendado o processo de remoção de tipo de nó

Para remover o tipo de nó, execute o [Remove-AzureRmServiceFabricNodeType](/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype) cmdlet.  O cmdlet demora algum tempo a concluir.  Em seguida, execute [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) em todos os nós que pretende que sejam removidos.

```powershell
$groupname = "mynodetype"
$nodetype = "nt2vm"
$clustername = "mytestcluster"

Remove-AzureRmServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mytestcluster.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My

$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre o cluster [características de durabilidade](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).
- Saiba mais sobre [tipos de nós e conjuntos de dimensionamento de máquinas virtuais](service-fabric-cluster-nodetypes.md).
- Saiba mais sobre [dimensionamento de clusters do Service Fabric](service-fabric-cluster-scaling.md).

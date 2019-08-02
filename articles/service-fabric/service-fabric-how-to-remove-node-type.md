---
title: Remover um tipo de nó no Azure Service Fabric | Microsoft Docs
description: Saiba como remover um tipo de nó de um Cluster Service Fabric em execução no Azure.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chakdan
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: atsenthi
ms.openlocfilehash: 44f25adf4168f4339a31e9270c2b23a8466a8889
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599485"
---
# <a name="remove-a-service-fabric-node-type"></a>Remover um tipo de nó Service Fabric
Este artigo descreve como dimensionar um cluster de Service Fabric do Azure removendo um tipo de nó existente de um cluster. Um Cluster Service Fabric é um conjunto de máquinas físicas ou virtuais conectadas à rede em que seus microserviços são implantados e gerenciados. Uma máquina ou VM que faz parte de um cluster é chamada de nó. Os conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure que você usa para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó definido em um cluster do Azure é [configurado como um conjunto de dimensionamento separado](service-fabric-cluster-nodetypes.md). Cada tipo de nó pode ser gerenciado separadamente. Depois de criar um Cluster Service Fabric, você pode dimensionar horizontalmente um cluster removendo um tipo de nó (conjunto de dimensionamento de máquinas virtuais) e todos os nós.  Você pode dimensionar o cluster a qualquer momento, mesmo quando as cargas de trabalho estiverem em execução no cluster.  À medida que o cluster é dimensionado, os aplicativos também são dimensionados automaticamente.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Use [Remove-AzServiceFabricNodeType](https://docs.microsoft.com/powershell/module/az.servicefabric/remove-azservicefabricnodetype) para remover um tipo de nó Service Fabric.

As três operações que ocorrem quando Remove-AzServiceFabricNodeType é chamado são:
1.  O conjunto de dimensionamento de máquinas virtuais por trás do tipo de nó é excluído.
2.  O tipo de nó é removido do cluster.
3.  Para cada nó dentro desse tipo de nó, todo o estado desse nó é removido do sistema. Se houver serviços nesse nó, os serviços serão movidos primeiro para outro nó. Se o Gerenciador de cluster não conseguir localizar um nó para a réplica/serviço, a operação será atrasada/bloqueada.

> [!WARNING]
> O uso de Remove-AzServiceFabricNodeType para remover um tipo de nó de um cluster de produção não é recomendado para ser usado com frequência. É um comando perigoso, pois exclui o recurso do conjunto de dimensionamento de máquinas virtuais por trás do tipo de nó. 

## <a name="durability-characteristics"></a>Características de durabilidade
A segurança é priorizada em velocidade ao usar remove-AzServiceFabricNodeType. O tipo de nó deve ser um [nível](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)de durabilidade prata ou ouro, porque:
- Bronze não oferece nenhuma garantia sobre salvar informações de estado.
- O ajuste de durabilidade prata e ouro faz qualquer alteração no conjunto de dimensionamento.
- O Gold também lhe dá controle sobre as atualizações do Azure em conjunto de dimensionamento.

Service Fabric "Orquestra" as alterações e atualizações subjacentes para que os dados não sejam perdidos. No entanto, quando você remove um nó com durabilidade bronze, você pode perder informações de estado. Se você estiver removendo um tipo de nó primário e seu aplicativo estiver sem estado, bronze será aceitável. Quando você executa cargas de trabalho com estado em produção, a configuração mínima deve ser prata. Da mesma forma, para cenários de produção, o tipo de nó primário sempre deve ser prata ou ouro.

### <a name="more-about-bronze-durability"></a>Mais sobre durabilidade de bronze

Ao remover um tipo de nó que é bronze, todos os nós no tipo de nó ficam inativos imediatamente. Service Fabric não intercepta quaisquer atualizações do conjunto de dimensionamento de nós bronze, portanto, todas as VMs ficam inativas imediatamente. Se você tiver algo com estado nesses nós, os dados serão perdidos. Agora, mesmo que você não tenha estado, todos os nós na Service Fabric participam do anel, de modo que uma vizinhança inteira pode ser perdida, o que pode desestabilizar o próprio cluster.

## <a name="recommended-node-type-removal-process"></a>Processo de remoção de tipo de nó recomendado

Para remover o tipo de nó, execute o cmdlet [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) .  O cmdlet leva algum tempo para ser concluído.  Depois que todas as VMs estiverem ausentes (representadas como "inativas"), o Fabric:/System/InfrastructureService/[NodeType Name] mostrará um estado de erro.

```powershell
$groupname = "mynodetype"
$nodetype = "nt2vm"
$clustername = "mytestcluster"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mytestcluster.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

Em seguida, você pode atualizar o recurso de cluster para remover o tipo de nó. Você pode usar a implantação de modelo ARM ou editar o recurso de cluster por meio do [Azure Resource Manager](https://resources.azure.com). Isso iniciará uma atualização de cluster, o que removerá o serviço Fabric:/System/InfrastructureService/[NodeType Name] que está em estado de erro.

Você ainda verá que os nós estão "inativos" na Service Fabric Explorer. Execute [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) em cada um dos nós que você deseja remover.


```powershell
$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre as [características](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)de durabilidade do cluster.
- Saiba mais sobre os [tipos de nó e conjuntos de dimensionamento de máquinas virtuais](service-fabric-cluster-nodetypes.md).
- Saiba mais sobre o dimensionamento de [cluster Service Fabric](service-fabric-cluster-scaling.md).

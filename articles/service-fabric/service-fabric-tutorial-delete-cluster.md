---
title: Eliminar um cluster de tecido de serviço em Azure
description: Neste tutorial, vai aprender a eliminar um cluster do Service Fabric alojado no Azure e todos os respetivos recursos. Pode eliminar o grupo de recursos que contém o cluster ou eliminar seletivamente os recursos.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: aa5b09b627db6bd3a3d573c32e7e68a359e2c04d
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92738986"
---
# <a name="tutorial-remove-a-service-fabric-cluster-running-in-azure"></a>Tutorial: remover um cluster do Service Fabric em execução no Azure

Este tutorial é parte cinco de uma série, e mostra-lhe como apagar um cluster de Tecido de Serviço em execução em Azure. Para eliminar completamente um cluster do Service Fabric também tem de eliminar os recursos utilizados pelo cluster. Tem duas opções: eliminar o grupo de recursos no qual está o cluster (o que elimina o recurso de cluster e todos os outros recursos no grupo de recursos) ou eliminar especificamente o recurso de cluster e os respetivos recursos associados (mas não outros recursos no grupo de recursos).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Eliminar um grupo de recursos e todos os respetivos recursos
> * Eliminar seletivamente os recursos de um grupo de recursos

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * Crie um [cluster windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) seguro no Azure usando um modelo
> * [Monitorize um cluster](service-fabric-tutorial-monitor-cluster.md)
> * [Reduzir ou aumentar um cluster horizontalmente](service-fabric-tutorial-scale-cluster.md)
> * [Atualizar o tempo de execução de um cluster](service-fabric-tutorial-upgrade-cluster.md)
> * Eliminar um cluster


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Instale [Azure Powershell](/powershell/azure//install-az-ps) ou [Azure CLI](/cli/azure/install-azure-cli).
* Criar um [cluster windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) seguro em Azure

## <a name="delete-the-resource-group-containing-the-service-fabric-cluster"></a>Eliminar o grupo de recursos que contém o cluster do Service Fabric
A forma mais simples de eliminar o cluster e todos os recursos que consome é eliminando o grupo de recursos.

Faça o súmis no Azure e selecione o ID de subscrição com o qual pretende remover o cluster.  Pode encontrar o ID da subscrição ao iniciar sessão no [portal do Azure](https://portal.azure.com). Elimine o grupo de recursos e todos os recursos de cluster utilizando o comando de eliminação do [grupo Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) ou [grupo az.](/cli/azure/group?view=azure-cli-latest)

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Remove-AzResourceGroup -Name $groupname -Force
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="selectively-delete-the-cluster-resource-and-the-associated-resources"></a>Eliminar seletivamente o recurso de cluster e os recursos associados
Se o grupo de recursos tiver apenas os recursos relacionados com o cluster do Service Fabric que pretende eliminar, é mais fácil eliminar todo o grupo de recursos. Se quiser eliminar seletivamente os recursos no grupo de recursos e manter os recursos que não estão associados ao cluster, siga estes passos.

Liste os recursos no grupo de recursos:

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Get-AzResource -ResourceGroupName $groupname | ft
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az resource list --resource-group $ResourceGroupName
```

Para cada um dos recursos que pretende eliminar, execute o seguinte script:

```powershell
Remove-AzResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "<Resource Type>" --resource-group $ResourceGroupName
```

Para eliminar o recurso do cluster, execute o seguinte script:

```powershell
Remove-AzResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "Microsoft.ServiceFabric/clusters" --resource-group $ResourceGroupName
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Eliminar um grupo de recursos e todos os respetivos recursos
> * Eliminar seletivamente os recursos de um grupo de recursos

Agora que concluiu este tutorial, experimente o seguinte:
* Saiba como inspecionar e gerir um cluster do Service Fabric com o [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
* Saiba como [corrigir o sistema operativo Windows](service-fabric-patch-orchestration-application.md) dos nós de cluster.
* Saiba como agregar e recolher eventos para [clusters Windows](service-fabric-diagnostics-event-aggregation-wad.md) e [configurar o Log Analytics](service-fabric-diagnostics-oms-setup.md) para monitorizar eventos de cluster.

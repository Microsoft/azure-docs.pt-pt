---
title: Adicione e remova os tipos de nó de um cluster gerido por tecido de serviço (pré-visualização)
description: Neste tutorial, aprenda a adicionar e remover os tipos de nó de um cluster gerido por Tecido de Serviço.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: bb33512652677fc4e46d8ba3668dca985bbcfe01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791244"
---
# <a name="tutorial-add-and-remove-node-types-from-a-service-fabric-managed-cluster-preview"></a>Tutorial: Adicione e remova os tipos de nós de um cluster gerido por Tecido de Serviço (pré-visualização)

Nesta série tutorial vamos discutir:

> [!div class="checklist"]
> * [Como implementar um cluster gerido por Tecido de Serviço](tutorial-managed-cluster-deploy.md)
> * [Como escalar um cluster gerido de tecido de serviço](tutorial-managed-cluster-scale.md)
> * Como adicionar e remover os nódes num cluster gerido por Tecido de Serviço
> * [Como implementar uma aplicação para um cluster gerido por Tecido de Serviço](tutorial-managed-cluster-deploy-app.md)

Esta parte da série abrange como:

> [!div class="checklist"]
> * Adicione um tipo de nó a um cluster gerido por Tecido de Serviço
> * Eliminar um tipo de nó de um cluster gerido por Tecido de Serviço

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster gerido por tecido de serviço (ver [*Implementar um cluster gerido).*](tutorial-managed-cluster-deploy.md)
* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) ou mais tarde (ver [*Instalar Azure PowerShell*](/powershell/azure/install-az-ps)).

## <a name="add-a-node-type-to-a-service-fabric-managed-cluster"></a>Adicione um tipo de nó a um cluster gerido por Tecido de Serviço

Pode adicionar um tipo de nó a um cluster gerido por Tecido de Serviço através de um modelo de Gestor de Recursos Azure, PowerShell ou CLI. Neste tutorial, vamos adicionar um nó usando o Azure PowerShell.

Para criar um novo tipo de nó, você precisa definir três propriedades:

* **Nome do tipo nó**: Nome único de quaisquer tipos de nó existentes no cluster.
* **Contagem de exemplos**: Número inicial de nós do novo tipo de nó.
* **Tamanho VM**: VM SKU para os nós. Se não for especificado, utiliza-se o valor predefinido *Standard_D2.*

> [!NOTE]
> Se o tipo de nó adicionado for o primeiro ou único tipo de nó no cluster, a propriedade primária deve ser usada.

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "mysfcluster"
$nodeTypeName = "nt2"
$vmSize = "Standard_D2_v2"

New-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -Name $nodeTypeName -InstanceCount 3 -vmSize $vmSize
```

## <a name="remove-a-node-type-from-a-service-fabric-managed-cluster"></a>Remova um tipo de nó de um cluster gerido por tecido de serviço

Para remover um tipo de nó de um cluster gerido por tecido de serviço, deve utilizar o PowerShell ou o CLI. Neste tutorial, removeremos um tipo de nó usando Azure PowerShell.

> [!NOTE]
> Não é possível remover um nó primário se for o único nó primário no cluster.  

Para remover um tipo de nó:

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "myCluster"
$nodeTypeName = "nt2"

Remove-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName  -Name $nodeTypeName
```

## <a name="next-steps"></a>Passos seguintes

 Nesta secção, adicionámos e apagamos os tipos de nó. Para aprender a implementar uma aplicação num cluster gerido por Tecidos de Serviço, consulte:

> [!div class="nextstepaction"]
> [Implementar uma aplicação para um cluster gerido por Tecido de Serviço](tutorial-managed-cluster-deploy-app.md)

---
title: Dimensione um cluster gerido por Tecido de Serviço (pré-visualização)
description: Neste tutorial, aprenda a escalar um tipo de nó de um cluster gerido por Tecido de Serviço.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: 769bcb339b2cc1419c7a3d92d0f08130029a9f95
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98785435"
---
# <a name="tutorial-scale-out-a-service-fabric-managed-cluster-preview"></a>Tutorial: Escale um cluster gerido por tecido de serviço (pré-visualização)

Nesta série tutorial vamos discutir:

> [!div class="checklist"]
> * [Como implantar um cluster gerido por Tecido de Serviço.](tutorial-managed-cluster-deploy.md)
> * Como escalar um cluster gerido de tecido de serviço
> * [Como adicionar e remover tipos de nó num cluster gerido por Tecido de Serviço](tutorial-managed-cluster-add-remove-node-type.md)
> * [Como implementar uma aplicação para um cluster gerido por Tecido de Serviço](tutorial-managed-cluster-deploy-app.md)

Esta parte da série abrange como:

> [!div class="checklist"]
> * Escalar um nó de cluster gerido de tecido de serviço

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster gerido por tecido de serviço (ver [*Implementar um cluster gerido).*](tutorial-managed-cluster-deploy.md)
* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) ou mais tarde (ver [*Instalar Azure PowerShell*](/powershell/azure/install-az-ps)).

## <a name="scale-a-service-fabric-managed-cluster"></a>Escalar um cluster gerido de tecido de serviço
Altere a contagem de casos para aumentar ou diminuir o número de nós no tipo de nó que gostaria de escalar. Pode encontrar nomes de tipo de nó no modelo Azure Resource Manager (modelo ARM) da sua implantação do cluster ou no Explorador de Tecidos de Serviço.  

> [!NOTE]
> Se o tipo de nó for primário, não será capaz de ir abaixo de 3 nós para um cluster SKU básico e 5 nós para um cluster SKU padrão.

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "mysfcluster"
$nodeTypeName = "FE"
$instanceCount = "7"

Set-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -name $nodeTypeName -InstanceCount $instanceCount -Verbose
```

O cluster começará a atualizar-se automaticamente e, passados alguns minutos, verá os nós adicionais.

## <a name="next-steps"></a>Próximos passos

Neste passo, escalamos um tipo de nó num cluster gerido por Tecido de Serviço. Para saber mais sobre a adição e remoção de tipos de nó, consulte:

> [!div class="nextstepaction"]
> [Adicione e remova os tipos de nó a um cluster gerido por Tecido de Serviço](tutorial-managed-cluster-add-remove-node-type.md)

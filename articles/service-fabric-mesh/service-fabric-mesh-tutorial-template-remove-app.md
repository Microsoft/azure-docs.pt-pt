---
title: Tutorial- Remova uma app em execução na malha de tecido de serviço Azure
description: Neste tutorial, vai ficar a saber como remover uma aplicação em execução no Service Fabric Mesh e como eliminar recursos.
author: georgewallace
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: d449343fd00ff958470a71ecb3a37d585d7ff8ed
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626615"
---
# <a name="tutorial-remove-an-application-and-resources"></a>Tutorial: Remover uma aplicação e os recursos

> [!IMPORTANT]
> A pré-estreia da Malha de Tecido de Serviço Azure foi reformada. As novas implementações deixarão de ser permitidas através da API de malha de malha de tecido de serviço. O apoio às implementações existentes continuará até 28 de abril de 2021.
> 
> Para mais detalhes, consulte [a pré-reforma da malha de malha de malha de tecido de serviço Azure.](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)

Este tutorial é a quarta parte de uma série. Vai ficar a saber como remover uma aplicação em execução que foi [implementada anteriormente no Service Fabric Mesh](service-fabric-mesh-tutorial-template-deploy-app.md). 

Na quarta parte da série, ficará a saber como:

> [!div class="checklist"]
> * Eliminar uma aplicação em execução no Service Fabric Mesh
> * Eliminar os recursos da aplicação

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * [Implementar uma aplicação no Service Fabric Mesh com um modelo](service-fabric-mesh-tutorial-template-deploy-app.md)
> * [Dimensionar uma aplicação em execução no Service Fabric Mesh](service-fabric-mesh-tutorial-template-scale-services.md)
> * [Atualizar uma aplicação em execução no Service Fabric Mesh](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * Remover uma aplicação

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Abra o [Azure Cloud Shell](service-fabric-mesh-howto-setup-cli.md) ou [instale a CLI do Azure e a CLI do Service Fabric Mesh localmente](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="delete-the-resource-group-and-all-the-resources"></a>Eliminar o grupo de recursos e todos os recursos

Quando já não forem necessários, elimine todos os recursos que criou. Anteriormente, [criava um novo grupo de recursos](service-fabric-mesh-tutorial-template-deploy-app.md#create-a-container-registry) para alojar a instância do Azure Container Registry e os recursos da aplicação do Service Fabric Mesh.  Pode eliminar este grupo de recursos, o qual vai eliminar todos os recursos associados.

```azurecli
az group delete --resource-group myResourceGroup
```

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="individually-delete-the-resources"></a>Eliminar individualmente os recursos
Também pode eliminar a instância do ACR, a aplicação do Service Fabric Mesh e os recursos de rede individualmente.

Para eliminar a instância do ACR:

```azurecli
az acr delete --resource-group myResourceGroup --name myContainerRegistry
```

Para eliminar a aplicação do Service Fabric Mesh:

```azurecli
az mesh app delete --resource-group myResourceGroup --name todolistapp
```

Para eliminar a rede:
```azurecli
az mesh network delete --resource-group myResourceGroup --name todolistappNetwork
```

## <a name="next-steps"></a>Passos seguintes

Nesta parte do tutorial, ficou a saber como:

> [!div class="checklist"]
> * Eliminar uma aplicação em execução no Service Fabric Mesh
> * Eliminar os recursos da aplicação

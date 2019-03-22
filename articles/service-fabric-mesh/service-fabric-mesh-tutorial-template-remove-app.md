---
title: Tutorial – Remover uma aplicação em execução no Azure Service Fabric Mesh | Microsoft Docs
description: Neste tutorial, vai ficar a saber como remover uma aplicação em execução no Service Fabric Mesh e como eliminar recursos.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/11/2019
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: adc5b96f29f610c63bcfa24a3b5f761c04d41d5b
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339674"
---
# <a name="tutorial-remove-an-application-and-resources"></a>Tutorial: Remover uma aplicação e de recursos

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

## <a name="next-steps"></a>Passos Seguintes

Nesta parte do tutorial, ficou a saber como:

> [!div class="checklist"]
> * Eliminar uma aplicação em execução no Service Fabric Mesh
> * Eliminar os recursos da aplicação
---
title: Tutorial- Cleanup Azure Service Malha de malha
description: Saiba como remover recursos do Azure Service Fabric Mesh, para que não lhe sejam cobrados recursos que já não está a utilizar.
author: dkkapur
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: d5e33099d58c9cb4c110a78f2576d6eac83a7932
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75351864"
---
# <a name="tutorial-remove-azure-resources"></a>Tutorial: Remover recursos do Azure

Este tutorial é a parte cinco de uma série e mostra-lhe como eliminar a aplicação e os respetivos recursos, para que não estes não lhe sejam cobrados.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Limpe os recursos utilizados pela aplicação para que estes não lhe sejam cobrados.

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * [Criar uma aplicação do Service Fabric Mesh no Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Depurar uma aplicação do Service Fabric Mesh em execução no seu cluster de desenvolvimento local](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Implementar uma aplicação do Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Atualizar uma aplicação do Service Fabric Mesh](service-fabric-mesh-tutorial-upgrade.md)
> * Limpar os recursos do Service Fabric Mesh

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se ainda não implementou a aplicação de tarefas pendentes, siga as instruções em [Publicar uma aplicação Web do Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="clean-up-resources"></a>Limpar recursos

Chegou ao fim do tutorial. Quando tiver concluído a utilização dos recursos que criou, elimine-os para que não lhe sejam cobrados recursos que já não está a utilizar. Isto é particularmente importante, porque o Mesh é um serviço sem servidor que é cobrado ao segundo. Para saber mais sobre os preços do Mesh, consulte https://aka.ms/sfmeshpricing.

Uma das conveniências que o Azure proporciona é que quando cria recursos que estão associados a um grupo de recursos específico, se eliminar o grupo de recursos, elimina todos os recursos associados. Dessa forma, não necessita de os eliminar individualmente.

Após criar um novo grupo de recursos para alojar a aplicação de tarefas pendentes, pode eliminar este grupo de recursos em segurança, o que eliminará todos os recursos associados.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Em alternativa, pode eliminar o grupo de recursos **sfmeshTutorial1RG** do [portal](../azure-resource-manager/management/manage-resource-groups-portal.md#delete-resource-groups). 

## <a name="next-steps"></a>Passos seguintes

Agora que concluiu a publicação de uma aplicação do Service Fabric Mesh no Azure, experimente o seguinte:

* Para ver outro exemplo de comunicação serviço a serviço, explore o [exemplo de aplicação de votação](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp)
* Para saber mais sobre o Modelo de Recursos do Service Fabric, consulte [Modelo de Recursos do Service Fabric Mesh](service-fabric-mesh-service-fabric-resources.md).
* Para saber mais sobre o Service Fabric Mesh, consulte [Descrição geral do Service Fabric Mesh](service-fabric-mesh-overview.md).
* Saiba mais sobre o [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)
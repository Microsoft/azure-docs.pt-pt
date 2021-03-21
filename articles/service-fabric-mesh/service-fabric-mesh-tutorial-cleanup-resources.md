---
title: Tutorial- Limpeza Azure Serviço Recursos de Malha de Tecido
description: Saiba como remover recursos do Azure Service Fabric Mesh, para que não lhe sejam cobrados recursos que já não está a utilizar.
author: georgewallace
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: 4d78e1e27d70b147bb52dff13675e63b79335d62
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625469"
---
# <a name="tutorial-remove-azure-resources"></a>Tutorial: Remover recursos do Azure

> [!IMPORTANT]
> A pré-estreia da Malha de Tecido de Serviço Azure foi reformada. As novas implementações deixarão de ser permitidas através da API de malha de malha de tecido de serviço. O apoio às implementações existentes continuará até 28 de abril de 2021.
> 
> Para mais detalhes, consulte [a pré-reforma da malha de malha de malha de tecido de serviço Azure.](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)

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

## <a name="clean-up-resources"></a>Limpar os recursos

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
* Saiba mais sobre o [Cloud Shell](../cloud-shell/overview.md)

---
title: Tutorial – Implementar uma aplicação do Service Fabric Mesh | Microsoft Docs
description: Saiba como utilizar o Visual Studio para publicar uma aplicação do Azure Service Fabric Mesh constituída por um site ASP.NET Core que comunica com um serviço Web de back-end.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/18/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: eef4cfaff38a96597794354cc991f5d3eeae9404
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806628"
---
# <a name="tutorial-deploy-a-service-fabric-mesh-application"></a>Tutorial: Implementar uma aplicação de malha do Service Fabric

Este tutorial é a terceira parte de uma série e mostra-lhe como publicar uma aplicação Web do Azure Service Fabric Mesh diretamente a partir do Visual Studio.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Publicar a aplicação no Azure com o Visual Studio.
> * Verificar o estado de implementação da aplicação.
> * Ver todas as aplicações implementadas na sua subscrição.

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * [Criar uma aplicação do Service Fabric Mesh no Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Depurar uma aplicação do Azure Service Fabric Mesh em execução no cluster de desenvolvimento local](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * Implementar uma aplicação do Service Fabric Mesh
> * [Atualizar uma aplicação do Service Fabric Mesh](service-fabric-mesh-tutorial-upgrade.md)
> * [Limpar os recursos do Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Certifique-se de que [configurou o ambiente de desenvolvimento](service-fabric-mesh-howto-setup-developer-environment-sdk.md), que inclui a instalação do runtime do Service Fabric, o SDK, o Docker e o Visual Studio 2017.

## <a name="download-the-to-do-sample-application"></a>Transferir a aplicação de tarefas de exemplo

Se não conseguiu criar a aplicação de tarefas de exemplo na [segunda parte desta série de tutoriais](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md), pode transferi-la. Numa janela do comando, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o seu computador local.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

A aplicação está no diretório `src\todolistapp`.

## <a name="publish-to-azure"></a>Publicar no Azure

Para publicar o seu projeto do Service Fabric Mesh no Azure, clique com o botão direito do rato em **todolistapp** no Visual Studio e selecione **Publicar...**

Em seguida, verá a caixa de diálogo **Publish Service Fabric Application** (Publicar Aplicação do Service Fabric).

![Caixa de diálogo de publicação do Service Fabric Mesh do Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Selecione a conta e a subscrição do Azure. Escolha uma **Localização**. Este artigo utiliza **E.U.A. Leste**.

Em **Resource group** (Grupo de recursos), selecione **\<Create New Resource Group...>** (Criar Novo Grupo de Recursos). É apresentada uma caixa de diálogo onde irá criar um novo grupo de recursos. Este artigo utiliza a localização **E.U.A. Leste** e atribui o nome **sfmeshTutorial1RG** ao grupo (se a sua organização tiver várias pessoas a utilizar a mesma subscrição, escolha um nome de grupo exclusivo).  Prima **Create** (Criar) para criar o grupo de recursos e voltar à caixa de diálogo de publicação.

![Caixa de diálogo do novo grupo de recursos do Service Fabric Mesh do Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-resource-group-dialog.png)

De volta à caixa de diálogo **Publish Service Fabric Application** (Publicar Aplicação do Service Fabric), em **Azure Container Registry**, selecione  **\<Create New Container Registry...>** (Criar Novo Registo de Contentor). Na caixa de diálogo **Create Container Registry** (Criar Registo de Contentor), utilize um nome exclusivo para o **Container registry name** (Nome do registo de contentor). Especifique uma **Localização** (este tutorial utiliza **E.U.A. Leste**). Selecione o **Grupo de recursos** que criou no passo anterior na lista pendente, por exemplo, **sfmeshTutorial1RG**. Defina o **SKU** como **Básico** e, em seguida, prima **Criar** para criar o registo de contentor do Azure privado e regressar à caixa de diálogo.

![Caixa de diálogo do novo registo de contentor do Service Fabric Mesh do Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-container-registry-dialog.png)

Se receber um erro de que o fornecedor de recursos não foi registado na sua subscrição, pode registá-lo. Primeiro, veja se o fornecedor de recursos está disponível para a sua subscrição:

```Powershell
Get-AzureRmResourceProvider -ListAvailable
```

Se o fornecedor de registo de contentor (`Microsoft.ContainerRegistry`) estiver disponível, registe-o a partir do Powershell:

```Powershell
Connect-AzureRmAccount
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ContainerRegistry
```

Na caixa de diálogo de publicação, prima o botão **Publish** (Publicar) para implementar a aplicação do Service Fabric no Azure.

Quando publica no Azure pela primeira vez, a imagem do docker é enviada para o Azure Container Registry (ACR), o que demora algum tempo consoante o tamanho da imagem. As publicações subsequentes do mesmo projeto serão mais rápidas. Pode monitorizar o progresso da implementação ao selecionar o painel **Service Fabric Tools** (Ferramentas do Service Fabric) na janela **Output** (Saída) do Visual Studio. Depois de concluída a implementação, a saída **Service Fabric Tools** (Ferramentas do Service Fabric) irá apresentar o endereço IP e a porta da aplicação sob a forma de um URL.

```
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registry...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Abra um browser e navegue para o URL para ver o site em execução no Azure.

## <a name="set-up-service-fabric-mesh-cli"></a>Configurar a CLI do Service Fabric mesh

Pode utilizar o Azure Cloud Shell ou uma instalação local da CLI do Azure para os passos restantes. Instale o módulo de extensão da CLI do Azure Service Fabric Mesh através destas [instruções](service-fabric-mesh-howto-setup-cli.md).

## <a name="check-application-deployment-status"></a>Verificar o estado de implementação da aplicação.

Neste momento, a aplicação foi implementada. Pode verificar o estado com o comando `app show`. 

O nome da aplicação do tutorial é `todolistapp`. Reúna os detalhes sobre a aplicação com o seguinte comando:

```azurecli-interactive
az mesh app show --resource-group $rg --name todolistapp
```

## <a name="get-the-ip-address-of-your-deployment"></a>Obter o endereço IP da sua implementação

Se quiser obter o endereço IP para a sua aplicação, utilize o seguinte comando:
  
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name todolistappGateway
```

## <a name="see-all-applications-currently-deployed-to-your-subscription"></a>Ver todas as aplicações implementadas na sua subscrição.

Pode utilizar o comando "app list" para obter uma lista das aplicações implementadas na sua subscrição.

```azurecli-interactive
az mesh app list --output table
```

## <a name="next-steps"></a>Passos Seguintes

Nesta parte do tutorial, ficou a saber como:
> [!div class="checklist"]
> * Publicar a aplicação no Azure.
> * Verificar o estado de implementação da aplicação.
> * Ver todas as aplicações atualmente implementadas na sua subscrição.

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Atualizar uma aplicação do Service Fabric Mesh](service-fabric-mesh-tutorial-upgrade.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest

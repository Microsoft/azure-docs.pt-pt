---
title: Quickstart - Implementar uma aplicação web para a malha de tecido de serviço Azure
description: Este quickstart mostra-lhe como criar um site ASP.NET Core e publicá-lo na Azure Service Fabric Mesh usando o Visual Studio.
author: georgewallace
ms.topic: quickstart
ms.date: 07/17/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: 665988f37d0afdb91bb074d8653cc3c24155966e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99627016"
---
# <a name="quickstart-create-and-deploy-a-web-app-to-azure-service-fabric-mesh"></a>Guia de Início Rápido: criar e implementar uma aplicação Web no Azure Service Fabric Mesh

> [!IMPORTANT]
> A pré-estreia da Malha de Tecido de Serviço Azure foi reformada. As novas implementações deixarão de ser permitidas através da API de malha de malha de tecido de serviço. O apoio às implementações existentes continuará até 28 de abril de 2021.
> 
> Para mais detalhes, consulte [a pré-reforma da malha de malha de malha de tecido de serviço Azure.](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)

O Azure Service Fabric Mesh é um serviço totalmente gerido que permite aos programadores implementar aplicações de microsserviços sem gerir máquinas virtuais, armazenamento ou redes.

Neste guia de início rápido, irá criar uma nova aplicação do Service Fabric Mesh, constituída por uma aplicação Web ASP.NET Core, executá-la no cluster de programação local e, em seguida, publicá-la para que seja executada no Azure.

Precisará de uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free/) facilmente antes de começar. Também terá de [configurar o seu ambiente de programação](service-fabric-mesh-howto-setup-developer-environment-sdk.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="create-a-service-fabric-mesh-project"></a>Criar um projeto do Service Fabric Mesh

Abra o Estúdio Visual e selecione **File**  >  **New**  >  **Project...**

Na caixa de diálogo **Novo Projeto**, na parte superior da caixa **Pesquisar**, escreva `mesh`. Selecione o modelo **Aplicação do Service Fabric Mesh**. (Se não vir o modelo, certifique-se de que instalou o SDK do Mesh e a pré-visualização das ferramentas do VS, conforme descrito em [configurar o ambiente de desenvolvimento](service-fabric-mesh-howto-setup-developer-environment-sdk.md). 

Na caixa **Name** (Nome), escreva **ServiceFabricMesh1** e, na caixa **Location** (Localização), defina o caminho da pasta onde os ficheiros do projeto serão armazenados.

Certifique-se de que **Criar diretório para a solução** está selecionado e clique em **OK** para criar o projeto do Service Fabric Mesh.

![Screenshot que mostra como criar um projeto de malha de tecido de serviço.](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-project.png)

### <a name="create-a-service"></a>Criar um serviço

Depois de clicar em **OK**, é apresentada a caixa de diálogo **New Service Fabric Service**(Novo Serviço do Service Fabric). Selecione o tipo de projeto **ASP.NET Core**, certifique-se de que o **SO do Contentor** está definido como **Windows** e clique em **OK** para criar o projeto ASP.NET Core. 

![Caixa de diálogo de novo projeto do Service Fabric Mesh do Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-service-fabric-service.png)

É apresentada a caixa de diálogo **New ASP.NET Core Web Application** (Nova Aplicação Web do ASP.NET Core). Selecione **Web Application** (Aplicação Web) e clique em **OK**.

![Nova aplicação ASP.NET Core do Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-aspnetcore-app.png)

O Visual Studio cria o projeto de aplicação do Service Fabric Mesh e o projeto ASP.NET Core.

## <a name="build-and-publish-to-your-local-cluster"></a>Criar e publicar no cluster local

É automaticamente criada e publicada uma imagem do Docker no seu cluster local, assim que o projeto for carregado. Este processo pode demorar algum tempo. Se quiser, pode monitorizar o progresso das ferramentas do Service Fabric na janela **Output** (Saída) ao selecionar o item **Service Fabric Tools** (Ferramentas do Service Fabric) no menu pendente da janela **Output** (Saída). Pode continuar a trabalhar enquanto a imagem do Docker está a ser implementada.

Após a criação do projeto, clique em **F5** para depurar o serviço localmente. Quando a implementação local estiver concluída e o Visual Studio estiver a executar o projeto, será aberta uma janela do browser com uma página Web de exemplo.

Quando terminar a navegação no serviço implementado, pare a depuração do projeto ao premir **Shift + F5** no Visual Studio.

## <a name="publish-to-azure"></a>Publicar no Azure

Para publicar o seu projeto do Service Fabric Mesh no Azure, clique com o botão direito do rato em **Service Fabric Mesh project** (Projeto do Service Fabric Mesh) no Visual Studio e selecione **Publish...** (Publicar...)

![Projeto do Service Fabric Mesh no Visual Studio ao clicar com o botão direito do rato](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-right-click-publish.png)

Irá ver a caixa de diálogo **Publish Service Fabric Application** (Publicar Aplicação do Service Fabric).

![Caixa de diálogo de publicação do Service Fabric Mesh do Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-dialog.png)

Selecione a conta e a subscrição do Azure. Escolha uma **localização**. Este artigo utiliza **E.U.A. Leste**.

No **grupo De Recursos,** selecione **\<Create New Resource Group...>** . É apresentada a caixa de diálogo **Create Resource Group** (Criar Grupo de Recursos). Defina o **Resource group name** (Nome do grupo de recursos) e a **Location** (Localização).  Este guia de início rápido utiliza a localização **E.U.A. Leste** e atribui o nome **sfmeshTutorial1RG** ao grupo (se a sua organização tiver várias pessoas a utilizar a mesma subscrição, escolha um nome de grupo de recursos exclusivo).  Clique em **Create** (Criar) para criar o grupo de recursos e voltar à caixa de diálogo de publicação.

![Screenshot que mostra como criar um novo grupo de recursos.](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-resource-group-dialog.png)

De volta ao diálogo **de aplicação de tecido de serviço de publicação,** sob **registo de contentores Azure,** selecione **\<Create New Container Registry...>** . Na caixa de diálogo **Create Container Registry** (Criar Registo de Contentor), utilize um nome exclusivo para o **Container registry name** (Nome do registo de contentor). Especifique uma **Location** (Localização) (este guia de início rápido utiliza **E.U.A. Leste**). Selecione o **Grupo de recursos** que criou no passo anterior na lista pendente, por exemplo, **sfmeshTutorial1RG**. Defina o **SKU** como **Basic** (Básico) e, em seguida, clique em **Create** (Criar) para voltar à caixa de diálogo de publicação.

![Caixa de diálogo do novo grupo de recursos do Service Fabric Mesh do Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-container-registry-dialog.png)

Na caixa de diálogo de publicação, clique no botão **Publish** (Publicar) para implementar a aplicação do Service Fabric Mesh no Azure.

Quando publica no Azure pela primeira vez, a imagem do docker é enviada para o Azure Container Registry (ACR), o que demora algum tempo consoante o tamanho da imagem. As publicações subsequentes do mesmo projeto serão mais rápidas. Pode monitorizar o progresso da implementação ao selecionar **Service Fabric Tools** no menu pendente da janela **Output** (Saída) do Visual Studio. Depois de concluída a implementação, a saída **Service Fabric Tools** (Ferramentas do Service Fabric) irá apresentar o endereço IP e a porta da aplicação sob a forma de um URL.

```
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMesh1\Web1\bin\Any CPU\Release\netcoreapp2.0\Web1.dll
Uploading the images to Azure Container Registry...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://...
```

Abra um browser e navegue para o URL para ver o site em execução no Azure:

![Executar uma aplicação Web do Service Fabric Mesh](media/service-fabric-mesh-tutorial-deploy-dotnetcore/deployed-web-project.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não forem necessários, elimine todos os recursos que criou para este guia de início rápido. Uma vez que criou um novo grupo de recursos para alojar o ACR e os recursos do serviço Service Fabric Mesh, pode eliminar este grupo de recursos em segurança, o que constitui uma forma fácil de eliminar todos os recursos associados ao mesmo.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Connect-AzureRmAccount
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Em alternativa, pode eliminar o grupo de recursos [do portal do Azure](https://portal.azure.com).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como criar e implementar aplicações do Service Fabric Mesh, avance para o tutorial.
> [!div class="nextstepaction"]
> [Criar, depurar e implantar uma aplicação web com vários serviços no Service Fabric Mesh](service-fabric-mesh-tutorial-create-dotnetcore.md)

---
title: 'Início rápido: executar um contêiner personalizado do Linux'
description: Introdução aos contêineres do Linux no serviço de Azure App implantando seu primeiro contêiner personalizado usando registros de contêiner do Azure.
author: msangapu-msft
ms.author: msangapu
ms.date: 08/28/2019
ms.topic: quickstart
ms.openlocfilehash: 14ea5444d3d8827919405a4d5aa8a176746c2e72
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688949"
---
# <a name="deploy-a-custom-linux-container-to-azure-app-service"></a>Implantar um contêiner personalizado do Linux no serviço Azure App

O serviço de aplicativo no Linux fornece pilhas de aplicativos predefinidas no Linux com suporte para linguagens como .NET, PHP, Node. js e outros. Também pode utilizar uma imagem personalizada do Docker para executar a aplicação Web numa pilha de aplicação que ainda não esteja definida no Azure. Este guia de início rápido mostra como implantar uma imagem de um ACR ( [registro de contêiner do Azure](/azure/container-registry) ) para o serviço de aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

* Uma [conta do Azure](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)
* [Docker](https://www.docker.com/community-edition)
* [Visual Studio Code](https://code.visualstudio.com/)
* A [extensão de serviço de Azure app para vs Code](vscode:extension/ms-azuretools.vscode-azureappservice). Você pode usar essa extensão para criar, gerenciar e implantar aplicativos Web do Linux na plataforma como um serviço (PaaS) do Azure.
* A [extensão do Docker para vs Code](vscode:extension/ms-azuretools.vscode-docker). Você pode usar essa extensão para simplificar o gerenciamento de imagens e comandos do Docker local e implantar imagens de aplicativo criadas no Azure.

## <a name="create-an-image"></a>Criar uma imagem

Para concluir este guia de início rápido, você precisará de uma imagem de aplicativo Web adequada armazenada em um [registro de contêiner do Azure](/azure/container-registry). Siga as instruções em [início rápido: criar um registro de contêiner privado usando o portal do Azure](/azure/container-registry/container-registry-get-started-portal), mas use a imagem de `mcr.microsoft.com/azuredocs/go` em vez da imagem de `hello-world`.

> [!IMPORTANT]
> Certifique-se de definir a opção de **usuário administrador** para **habilitar** ao criar o registro de contêiner. Você também pode defini-lo na seção **chaves de acesso** da página do registro na portal do Azure. Essa configuração é necessária para o acesso ao serviço de aplicativo.

## <a name="sign-in"></a>Iniciar sessão

Em seguida, inicie VS Code e faça logon em sua conta do Azure usando a extensão do serviço de aplicativo. Para fazer isso, selecione o logotipo do Azure na barra de atividades, navegue até o Gerenciador do **serviço de aplicativo** e selecione **entrar no Azure** e siga as instruções.

![entrar no Azure](./media/quickstart-docker/sign-in.png)

## <a name="check-prerequisites"></a>Verificar pré-requisitos

Agora você pode verificar se você tem todos os pré-requisitos instalados e configurados corretamente.

No VS Code, você deve ver seu endereço de email do Azure na barra de status e sua assinatura no Gerenciador do **serviço de aplicativo** .

Em seguida, verifique se você tem o Docker instalado e em execução. O comando a seguir exibirá a versão do Docker, se estiver em execução.

```bash
docker --version
```

Por fim, verifique se o registro de contêiner do Azure está conectado. Para fazer isso, selecione o logotipo do Docker na barra de atividade e, em seguida, navegue até **registros**.

![Registos](./media/quickstart-docker/registries.png)

## <a name="deploy-the-image-to-azure-app-service"></a>Implantar a imagem no serviço Azure App

Agora que tudo está configurado, você pode implantar sua imagem para [Azure app serviço](https://azure.microsoft.com/services/app-service/) diretamente do Gerenciador de extensões do Docker.

Localize a imagem no nó **registros** no Gerenciador do **Docker** e expanda-a para mostrar suas marcas. Clique com o botão direito do mouse em uma marca e selecione **implantar imagem para Azure app serviço**.

A partir daqui, siga os prompts para escolher uma assinatura, um nome de aplicativo exclusivo globalmente, um grupo de recursos e um plano do serviço de aplicativo. Escolha o **básico B1** para o tipo de preço e uma região.

Após a implantação, seu aplicativo estará disponível em `http://<app name>.azurewebsites.net`.

Um **grupo de recursos** é uma coleção nomeada de todos os recursos do seu aplicativo no Azure. Por exemplo, um grupo de recursos pode conter uma referência a um site, um banco de dados e uma função do Azure.

Um **plano do serviço de aplicativo** define os recursos físicos que serão usados para hospedar seu site. Este guia de início rápido usa um plano de hospedagem **básico** na infraestrutura do **Linux** , o que significa que o site será hospedado em um computador Linux junto com outros sites. Se você começar com o plano **básico** , poderá usar o portal do Azure para escalar verticalmente para que seu seja o único site em execução em um computador.

## <a name="browse-the-website"></a>Navegar no site

O painel **saída** será aberto durante a implantação para indicar o status da operação. Quando a operação for concluída, localize o aplicativo criado no Gerenciador do **serviço de aplicativo** , clique nele com o botão direito do mouse e selecione **procurar site** para abrir o site no navegador.

> [!div class="nextstepaction"]
> [Ocorreu um problema](https://www.research.net/r/PWZWZ52?tutorial=quickstart-docker&step=deploy-app)

## <a name="next-steps"></a>Passos seguintes

Parabéns, você concluiu com êxito este guia de início rápido!

Em seguida, Confira as outras extensões do Azure.

* [BD do Cosmos](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Funções do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Ferramentas de CLI do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Ferramentas de Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Ou então, faça tudo isso instalando o pacote de extensão das [Ferramentas do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) .

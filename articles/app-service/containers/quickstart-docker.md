---
title: 'Quickstart: Executar um recipiente linux personalizado'
description: Inicie com os contentores Linux no Serviço de Aplicações Azure, implantando o seu primeiro recipiente personalizado utilizando registos de contentores Azure.
author: msangapu-msft
ms.author: msangapu
ms.date: 08/28/2019
ms.topic: quickstart
ms.openlocfilehash: 62e6b007e89fc6be726d3d971ca838770db9cb6e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "75422141"
---
# <a name="deploy-a-custom-linux-container-to-azure-app-service"></a>Desloque um recipiente linux personalizado para o Serviço de Aplicações Azure

O Serviço de Aplicações no Linux fornece pilhas de aplicações pré-definidas no Linux com suporte para idiomas como .NET, PHP, Node.js e outros. Também pode utilizar uma imagem personalizada do Docker para executar a aplicação Web numa pilha de aplicação que ainda não esteja definida no Azure. Este quickstart mostra-lhe como implementar uma imagem de um Registo de [Contentores Azure](/azure/container-registry) (ACR) para o Serviço de Aplicações.

## <a name="prerequisites"></a>Pré-requisitos

* Uma [conta Azure](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)
* [Estivador](https://www.docker.com/community-edition)
* [Código de estúdio visual](https://code.visualstudio.com/)
* A extensão do Serviço de [Aplicações Azure para o Código VS](vscode:extension/ms-azuretools.vscode-azureappservice). Pode utilizar esta extensão para criar, gerir e implementar aplicações Web Linux na Plataforma Azure como serviço (PaaS).
* A [extensão do Docker para o Código VS](vscode:extension/ms-azuretools.vscode-docker). Pode utilizar esta extensão para simplificar a gestão de imagens e comandos locais do Docker e para implementar imagens de aplicações construídas para o Azure.

## <a name="create-an-image"></a>Criar uma imagem

Para completar este arranque rápido, necessitará de uma imagem de aplicação web adequada armazenada num Registo de [Contentores Azure](/azure/container-registry). Siga as instruções em [Quickstart: Crie um registo de contentores privado utilizando o portal Azure,](/azure/container-registry/container-registry-get-started-portal)mas utilize a `mcr.microsoft.com/azuredocs/go` imagem em vez da `hello-world` imagem. Para referência, a [amostra Dockerfile encontra-se em Azure Samples repo](https://github.com/Azure-Samples/go-docs-hello-world).

> [!IMPORTANT]
> Certifique-se de definir a opção **De utilizador de administrador** para **ativar** quando criar o registo do recipiente. Também pode defini-lo a partir da secção de chaves de **acesso** da sua página de registo no portal Azure. Esta definição é necessária para o acesso ao Serviço de Aplicações.

## <a name="sign-in"></a>Iniciar sessão

Em seguida, lance o Código VS e inicie sessão na sua conta Azure utilizando a extensão do Serviço de Aplicações. Para isso, selecione o logótipo Azure na Barra de Atividades, navegue para o explorador de **SERVIÇO SAL,** em seguida, selecione **Iniciar sessão no Azure** e siga as instruções.

![assinar em Azure](./media/quickstart-docker/sign-in.png)

## <a name="check-prerequisites"></a>Verificar pré-requisitos

Agora pode verificar se tem todos os pré-requisitos instalados e configurados corretamente.

No Código VS, deverá consultar o seu endereço de e-mail Azure na Barra de Estado e a sua subscrição no explorador de **SERVIÇOS APP.**

Em seguida, verifique se tem o Docker instalado e a correr. O seguinte comando irá exibir a versão Docker se estiver em execução.

```bash
docker --version
```

Por fim, certifique-se de que o registo de contentores Azure está ligado. Para isso, selecione o logótipo Does na Barra de Atividades e, em seguida, navegue para **REGISTRIES**.

![Registos](./media/quickstart-docker/registries.png)

## <a name="deploy-the-image-to-azure-app-service"></a>Desloque a imagem para o Serviço de Aplicações Azure

Agora que tudo está configurado, pode implementar a sua imagem para o [Azure App Service](https://azure.microsoft.com/services/app-service/) diretamente do explorador de extensão Docker.

Encontre a imagem sob o nó **dos Registos** no explorador **do DOCKER** e expanda-a para mostrar as suas etiquetas. Clique na direita numa etiqueta e, em seguida, selecione Implementar imagem para o Serviço de **Aplicações Azure**.

A partir daqui, siga as instruções para escolher uma subscrição, um nome de aplicação globalmente único, um Grupo de Recursos e um Plano de Serviço de Aplicações. Escolha **B1 Basic** para o nível de preços, e uma região.

Após a implementação, `http://<app name>.azurewebsites.net`a sua aplicação está disponível em .

A **Resource Group** é uma coleção nomeada de todos os recursos da sua aplicação em Azure. Por exemplo, um Grupo de Recursos pode conter uma referência a um website, uma base de dados e uma Função Azure.

Um Plano de Serviço de **Aplicações** define os recursos físicos que serão usados para hospedar o seu website. Este quickstart usa um plano **básico** de hospedagem na infraestrutura **Linux,** o que significa que o site será hospedado numa máquina Linux ao lado de outros sites. Se começar com o plano **Basic,** pode usar o portal Azure para escalar para que o seu seja o único site a funcionar numa máquina.

## <a name="browse-the-website"></a>Navegue no site

O painel **de saída** será aberto durante a implantação para indicar o estado da operação. Quando a operação estiver concluída, encontre a aplicação criada no explorador de **SERVIÇOS APP,** clique-a com o botão direito e, em seguida, selecione **O Website Browse** para abrir o site no seu navegador.

> [!div class="nextstepaction"]
> [Deparei-me com um problema.](https://www.research.net/r/PWZWZ52?tutorial=quickstart-docker&step=deploy-app)

## <a name="next-steps"></a>Passos seguintes

Parabéns, conseguiste ter conseguido este arranque rápido!

Em seguida, confira as outras extensões Azure.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Funções do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Ferramentas Azure CLI](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Ferramentas de Gestor de Recursos Azure](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Ou obtenha-os todos instalando o pacote de extensão [Azure Tools.](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)

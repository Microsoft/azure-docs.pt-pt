---
title: 'Quickstart: Executar um recipiente personalizado no Serviço de Aplicações'
description: Começa com os contentores no Azure App Service implantando o seu primeiro recipiente personalizado.
author: msangapu-msft
ms.author: msangapu
ms.date: 10/21/2019
ms.topic: quickstart
ms.custom: devx-track-csharp
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 421f9dc26d184d16a2fe563aefa72062c5d8c7e6
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608406"
---
# <a name="run-a-custom-container-in-azure"></a>Executar um recipiente personalizado em Azure

::: zone pivot="container-windows"
O [Serviço de Aplicações do Azure](overview.md) disponibiliza pilhas de aplicações predefinidas no Windows, como ASP.NET ou Node.js, em execução no IIS. O ambiente pré-configurado do contentor do Windows bloqueia o sistema operativo a partir de acesso administrativo, instalações de software, alterações na cache de montagem global, e assim por diante. Para mais informações, consulte [a funcionalidade do sistema operativo no Azure App Service](operating-system-functionality.md). Se a sua aplicação precisar de mais acesso do que aquele que o ambiente pré-configurado permite, pode implementar um contentor do Windows personalizado.

Este quickstart mostra como implementar uma aplicação ASP.NET, numa imagem do Windows, para [Docker Hub](https://hub.docker.com/) do Visual Studio. Você executou a aplicação em um recipiente personalizado no Azure App Service.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- <a href="https://hub.docker.com/" target="_blank">Inscrever numa conta do Docker Hub</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Instale o Docker para Windows</a>.
- <a href="/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Mude o Docker para executar contentores do Windows</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Instale o Visual Studio 2019</a> com as **ASP.NET e desenvolvimento web** e cargas de trabalho de desenvolvimento **Azure.** Se já instalou o Visual Studio 2019:

    - Instale as últimas atualizações no Estúdio Visual selecionando **o Help** Check  >  **for Updates**.
    - Adicione as cargas de trabalho no Estúdio Visual selecionando **Ferramentas**  >  **Obter Ferramentas e Funcionalidades**.

## <a name="create-an-aspnet-web-app"></a>Criar uma aplicação Web ASP.NET

Crie uma aplicação web ASP.NET seguindo estes passos:

1. Abra o Estúdio Visual e, em seguida, **selecione Criar um novo projeto.**

1. Na **Criar um novo projeto,** encontrar e escolher ASP.NET **Aplicação Web (.NET Framework)** para C#, em seguida, selecione **Next**.

1. Em **Configurar o seu novo projeto**, nomeie a aplicação _myfirstazurewebapp_, e, em seguida, selecione **Create**.

   ![Configure o seu projeto de aplicação web](./media/quickstart-custom-container/configure-web-app-project-container.png)

1. Pode implementar qualquer tipo de aplicação Web ASP.NET no Azure. Para este arranque rápido, escolha o modelo **MVC.**

1. Selecione **o suporte do Docker** e certifique-se de que a autenticação está definida para Não **Autenticação**. Selecione **Criar**.

   ![Criar aplicação web ASP.NET](./media/quickstart-custom-container/select-mvc-template-for-container.png)

1. Se o ficheiro _Dockerfile_ não se abrir automaticamente, abra-o a partir do **Explorador de Soluções**.

1. Precisa de uma [imagem dos pais apoiada.](configure-custom-container.md#supported-parent-images) Altere a imagem principal, substituindo a linha `FROM` pelo código seguinte e guarde o ficheiro:

   ```dockerfile
   FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
   ```

1. A partir do menu Visual Studio, selecione **Debug**  >  **Start Without Debugging** para executar a aplicação web localmente.

   ![Executar a aplicação localmente](./media/quickstart-custom-container/local-web-app.png)

## <a name="publish-to-docker-hub"></a>Publicar no Hub do Docker

1. No **Solution Explorer,** clique com o botão direito no projeto **myfirstazurewebapp** e selecione **Publicar**.

1. Escolha **o Serviço de Aplicações** e, em seguida, selecione **Publicar.**

1. Em Pick a **publish target**, selecione **Container Registry** and **Docker Hub**, e, em seguida, clique em **Publicar**.

   ![Publicar a partir da página de descrição geral do projeto](./media/quickstart-custom-container/publish-to-docker-vs2019.png)

1. Forneça as suas credenciais de conta Docker Hub e **selecione Save**.

   Aguarde pela conclusão da implementação. A página **publicar** agora mostra o nome do repositório para usar mais tarde.

   ![Screenshot que realça o nome do repositório.](./media/quickstart-custom-container/published-docker-repository-vs2019.png)

1. Copie este nome de repositório para utilizar mais tarde.

## <a name="create-a-windows-container-app"></a>Criar uma aplicação de contentor do Windows

1. Inicie sessão no [portal do Azure]( https://portal.azure.com).

1. Selecione **Criar um recurso**, no canto superior esquerdo do portal do Azure.

1. Na caixa de pesquisa acima da lista de recursos do Azure Marketplace, procure **a Web App para contentores,** e selecione **Create**.

1. Na **Web App Create,** escolha a sua subscrição e um **Grupo de Recursos.** Pode criar um novo grupo de recursos, se necessário.

1. Forneça um nome de aplicação, como *win-container-demo* e escolha **o Windows** for **Operating System**. Selecione **Seguinte: Docker** para continuar.

   ![Criar uma aplicação web para contentores](media/quickstart-custom-container/create-web-app-continer.png)

1. Para **Image Source**, escolha Docker **Hub** e para Imagem **e marque,** insira o nome do repositório que copiou em [Publicar para Docker Hub](#publish-to-docker-hub).

   ![Configurar você é uma web app para contentores](media/quickstart-custom-container/configure-web-app-continer.png)

    Se tiver uma imagem personalizada noutra localização para a sua aplicação Web, como no [Azure Container Registry](../container-registry/index.yml) ou noutro repositório privado, pode configurá-la aí.

1. Selecione **Rever e criar** e, em seguida, **criar** e esperar que a Azure crie os recursos necessários.

## <a name="browse-to-the-container-app"></a>Navegar para a aplicação de contentor

Quando a operação do Azure estiver concluída, é apresentada uma caixa de notificação.

![Implementação conseguiu](media/quickstart-custom-container/portal-create-finished.png)

1. Clique em **Ir para recurso**.

1. Na visão geral deste recurso, siga o link ao lado **do URL**.

Uma nova página do navegador abre para a seguinte página:

![Aplicativo de contentor do Windows começando](media/quickstart-custom-container/app-starting.png)

Aguarde alguns minutos e tente novamente, até ver a home page de ASP.NET predefinida:

![Aplicação de contentores do Windows em execução](media/quickstart-custom-container/app-running-vs.png)

**Parabéns!** Está a executar o seu primeiro contentor do Windows personalizado no Serviço de Aplicações do Azure.

## <a name="see-container-start-up-logs"></a>Ver os registos de arranque do contentor

O contentor do Windows poderá demorar algum tempo até ser carregado. Para ver o progresso, navegue para o seguinte URL substituindo *\<app_name>* pelo nome da sua aplicação.
```
https://<app_name>.scm.azurewebsites.net/api/logstream
```

Os registos transmitidos têm o seguinte aspeto:

```
2018-07-27T12:03:11  Welcome, you are now connected to log-streaming service.
27/07/2018 12:04:10.978 INFO - Site: win-container-demo - Start container succeeded. Container: facbf6cb214de86e58557a6d073396f640bbe2fdec88f8368695c8d1331fc94b
27/07/2018 12:04:16.767 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.017 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.020 INFO - Site: win-container-demo - Container started successfully
```

## <a name="update-locally-and-redeploy"></a>Atualizar localmente e reimplementar

1. No Estúdio Visual, no **Solution Explorer,** open **Views**  >  **Home**  >  **Index.cshtml**.

1. Localize a etiqueta HTML `<div class="jumbotron">` na parte superior e substitua todo o elemento pelo código seguinte:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Para recolocar a Azure, clique com a direita no projeto **myfirstazurewebapp** no **Solution Explorer** e escolha **Publicar**.

1. Na página de publicação, selecione **Publicar** e aguarde até a publicação estar concluída.

1. Para dizer ao Serviço de Aplicações para obter a nova imagem do Hub do Docker, reinicie a aplicação. De volta à página da aplicação no portal, clique em **Reiniciar**  >  **Sim**.

   ![Reiniciar a aplicação Web no Azure](./media/quickstart-custom-container/portal-restart-app.png)

[Navegue novamente para a aplicação de contentor](#browse-to-the-container-app). Quando atualiza a página Web, primeiro, a aplicação deve reverter para a página "A Iniciar" e, em seguida, volta a apresentar a página Web atualizada ao fim de alguns minutos.

![Aplicação Web atualizada no Azure](./media/quickstart-custom-container/azure-web-app-updated.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Migrar para o contentor do Windows no Azure](tutorial-custom-container.md)

Ou, confira outros recursos:

> [!div class="nextstepaction"]
> [Configurar um contentor personalizado](configure-custom-container.md)

::: zone-end  

::: zone pivot="container-linux"
O Serviço de Aplicações no Linux fornece pilhas de aplicações pré-definidas no Linux com suporte para idiomas como .NET, PHP, Node.js e outros. Também pode utilizar uma imagem personalizada do Docker para executar a aplicação Web numa pilha de aplicação que ainda não esteja definida no Azure. Este quickstart mostra-lhe como implementar uma imagem de um Registo de [Contentores Azure](../container-registry/index.yml) (ACR) para o Serviço de Aplicações.

## <a name="prerequisites"></a>Pré-requisitos

* Uma [conta Azure](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)
* [Docker](https://www.docker.com/community-edition)
* [Visual Studio Code](https://code.visualstudio.com/)
* [A extensão do Serviço de Aplicações Azure para o Código VS.](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) Pode utilizar esta extensão para criar, gerir e implementar aplicações Web Linux na Plataforma Azure como um serviço (PaaS).
* A [extensão do Docker para o Código VS.](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker) Pode utilizar esta extensão para simplificar a gestão de imagens e comandos locais do Docker e implementar imagens de aplicações construídas para o Azure.

## <a name="create-an-image"></a>Criar uma imagem

Para completar este arranque rápido, você precisará de uma imagem de aplicação web adequada armazenada num [Registo de Contentores Azure.](../container-registry/index.yml) Siga as instruções em [Quickstart: Crie um registo de contentores privados utilizando o portal Azure,](../container-registry/container-registry-get-started-portal.md)mas utilize a `mcr.microsoft.com/azuredocs/go` imagem em vez da `hello-world` imagem. Para referência, a [amostra Dockerfile encontra-se em Azure Samples repo](https://github.com/Azure-Samples/go-docs-hello-world).

> [!IMPORTANT]
> Certifique-se de que define a opção **Admin User** para **Ativar** quando criar o registo do contentor. Também pode defini-lo a partir da secção de **teclas de acesso** da sua página de registo no portal Azure. Esta definição é necessária para o acesso ao Serviço de Aplicações.

## <a name="sign-in"></a>Iniciar sessão

Em seguida, lance o Código VS e inicie sessão na sua conta Azure utilizando a extensão do Serviço de Aplicações. Para isso, selecione o logótipo Azure na Barra de Atividades, navegue para o explorador **APP SERVICE,** em seguida, selecione **Iniciar súpido no Azure** e siga as instruções.

![assinar para Azure](./media/quickstart-docker/sign-in.png)

## <a name="check-prerequisites"></a>Verificar pré-requisitos

Agora pode verificar se tem todos os pré-requisitos instalados e configurados corretamente.

No Código VS, deverá ver o seu endereço de e-mail Azure na Barra de Estado e a sua subscrição no explorador do **SERVIÇO APP.**

Em seguida, verifique se tem Docker instalado e funcionando. O seguinte comando mostrará a versão Docker se estiver em funcionamento.

```bash
docker --version
```

Por último, certifique-se de que o registo do seu contentor Azure está ligado. Para isso, selecione o logótipo Docker na Barra de Atividades e, em seguida, navegue para **REGISTRIES**.

![O screenshot mostra o valor dos Registos com o Azure expandido e um ficheiro com a extensão do nome de ficheiros do ponto i o.](./media/quickstart-docker/registries.png)

## <a name="deploy-the-image-to-azure-app-service"></a>Implementar a imagem para o Serviço de Aplicações Azure

Agora que tudo está configurado, pode implementar a sua imagem no [Azure App Service](https://azure.microsoft.com/services/app-service/) diretamente do explorador de extensão Docker.

Encontre a imagem sob o nó **registros** no explorador **DOCKER** e expanda-a para mostrar as suas etiquetas. Clique com o botão direito numa etiqueta e, em seguida, **selecione Implementar imagem para o Serviço de Aplicações Azure**.

A partir daqui, siga as instruções para escolher uma subscrição, um nome de aplicação globalmente único, um Grupo de Recursos e um Plano de Serviço de Aplicações. Escolha **B1 Basic** para o nível de preços, e uma região.

Após a implementação, a sua aplicação está disponível em `http://<app name>.azurewebsites.net` .

Um **Grupo de Recursos** é uma coleção nomeada de todos os recursos da sua aplicação em Azure. Por exemplo, um Grupo de Recursos pode conter uma referência a um website, uma base de dados e uma Função Azure.

Um Plano de Serviço de **Aplicações** define os recursos físicos que serão utilizados para hospedar o seu website. Este quickstart usa um plano **básico** de hospedagem na infraestrutura **Linux,** o que significa que o site será hospedado numa máquina Linux ao lado de outros sites. Se começar com o plano **Básico,** pode utilizar o portal Azure para escalar de modo a que o seu seja o único site a funcionar numa máquina.

## <a name="browse-the-website"></a>Navegue no site

O painel **de saída** abrir-se-á durante a colocação para indicar o estado da operação. Quando a operação estiver concluída, encontre a aplicação que criou no explorador do **SERVIÇO APP,** clique com o botão direito e, em seguida, selecione **Browse Website** para abrir o site no seu navegador.

> [!div class="nextstepaction"]
> [Dei conta de um problema.](https://www.research.net/r/PWZWZ52?tutorial=quickstart-docker&step=deploy-app)

## <a name="next-steps"></a>Passos seguintes

Parabéns, conseguiste completar esta rapidinha!

Em seguida, confira as outras extensões do Azure.

* [BD do Cosmos](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Funções do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Ferramentas Azure CLI](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Ferramentas de gestor de recursos Azure](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Ou obtenha-os todos instalando o pacote de extensões [Azure Tools.](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)

Confira outros recursos:

> [!div class="nextstepaction"]
> [Configurar um contentor personalizado](configure-custom-container.md)

::: zone-end

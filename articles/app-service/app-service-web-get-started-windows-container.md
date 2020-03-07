---
title: 'QuickStart: Recipiente windows (Pré-visualização)'
description: Desloque o seu primeiro recipiente personalizado para o Serviço de Aplicações Azure. Aproveite a contentorização, personalize o recipiente Windows da forma que quiser.
ms.topic: quickstart
ms.date: 08/30/2019
ms.custom: seodec18
ms.openlocfilehash: 7901498772b8e746fb2c87a5237f06ab279e3b64
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374023"
---
# <a name="run-a-custom-windows-container-in-azure-preview"></a>Executar um contentor do Windows personalizado no Azure (Pré-visualização)

O [Serviço de Aplicações do Azure](overview.md) disponibiliza pilhas de aplicações predefinidas no Windows, como ASP.NET ou Node.js, em execução no IIS. O ambiente do Windows reconfigurado bloqueia o sistema operativo a partir de acesso administrativo, instalações de software, alterações na cache de montagem global, e assim por diante. Para mais informações, consulte a funcionalidade do sistema operativo no Serviço de [Aplicações Azure](operating-system-functionality.md). Se a sua aplicação precisar de mais acesso do que aquele que o ambiente pré-configurado permite, pode implementar um contentor do Windows personalizado.

Este quickstart mostra como implementar uma aplicação ASP.NET, numa imagem do Windows, para [Docker Hub](https://hub.docker.com/) do Visual Studio. Executa a aplicação num contentor personalizado no Serviço de Aplicações Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- <a href="https://hub.docker.com/" target="_blank">Inscrever numa conta do Docker Hub</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Instale o Docker para Windows</a>.
- <a href="https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Mude o Docker para executar contentores do Windows</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Instale o Visual Studio 2019</a> com o **ASP.NET e desenvolvimento web** e cargas de trabalho de **desenvolvimento azure.** Se já instalou o Visual Studio 2019:

    - Instale as últimas atualizações no Estúdio Visual selecionando **ajuda** > **verificar se há atualizações**.
    - Adicione as cargas de trabalho no Estúdio Visual selecionando **Ferramentas** > **Obter Ferramentas e Funcionalidades**.

## <a name="create-an-aspnet-web-app"></a>Criar uma aplicação Web ASP.NET

Crie uma aplicação web ASP.NET seguindo estes passos:

1. Abra o Estúdio Visual e, em seguida, selecione **Criar um novo projeto**.

1. Em **Criar um novo projeto,** encontre e escolha ASP.NET **Aplicação Web (.QUADRO NET)** para, C#em seguida, selecione **Next**.

1. No **Configure o seu novo projeto,** nomeie a aplicação _myFirstAzureWebApp_, e depois selecione **Create**.

   ![Configure o seu projeto de aplicação web](./media/app-service-web-get-started-windows-container/configure-web-app-project-container.png)

1. Pode implementar qualquer tipo de aplicação Web ASP.NET no Azure. Para este arranque rápido, escolha o modelo **MVC.**

1. Selecione **suporte Docker**, e certifique-se de que a autenticação está definida para **não autenticação**. Selecione **Criar**.

   ![Criar ASP.NET aplicação web](./media/app-service-web-get-started-windows-container/select-mvc-template-for-container.png)

1. Se o ficheiro _Dockerfile_ não se abrir automaticamente, abra-o a partir do **Explorador de Soluções**.

1. Precisa de uma [imagem de pai apoiada.](#use-a-different-parent-image) Altere a imagem principal, substituindo a linha `FROM` pelo código seguinte e guarde o ficheiro:

   ```Dockerfile
   FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
   ```

1. A partir do menu do Estúdio Visual, selecione **Debug** > **Start Without Debugging** para executar a aplicação web localmente.

   ![Executar a aplicação localmente](./media/app-service-web-get-started-windows-container/local-web-app.png)

## <a name="publish-to-docker-hub"></a>Publicar no Hub do Docker

1. No **Solution Explorer,** clique à direita no projeto **myFirstAzureWebApp** e selecione **Publish**.

1. Escolha **o Serviço de Aplicações** e, em seguida, selecione **Publicar**.

1. Em Pick a **publish target**, selecione Registo de **Contentores** e **Docker Hub**, e, em seguida, clique em **Publicar**.

   ![Publicar a partir da página de descrição geral do projeto](./media/app-service-web-get-started-windows-container/publish-to-docker-vs2019.png)

1. Forneça as credenciais da sua conta Docker Hub e selecione **Save**.

   Aguarde pela conclusão da implementação. A página **Publish** mostra agora o nome do repositório para usar mais tarde.

   ![Publicar a partir da página de descrição geral do projeto](./media/app-service-web-get-started-windows-container/published-docker-repository-vs2019.png)

1. Copie este nome de repositório para utilizar mais tarde.

## <a name="create-a-windows-container-app"></a>Criar uma aplicação de contentor do Windows

1. Inicie sessão no [portal do Azure]( https://portal.azure.com).

1. Selecione **Criar um recurso**, no canto superior esquerdo do portal do Azure.

1. Na caixa de pesquisa acima da lista de recursos do Azure Marketplace, procure por **Web App para contentores,** e selecione **Create**.

1. Na **Web App Create,** escolha a sua subscrição e um **Grupo de Recursos**. Pode criar um novo grupo de recursos, se necessário.

1. Forneça um nome de aplicativo, como *win-container-demo* e escolha **Windows** para **Sistema Operativo**. Selecione **Seguinte: Docker** para continuar.

   ![Criar uma Aplicação Web para contentores](media/app-service-web-get-started-windows-container/create-web-app-continer.png)

1. Para **Fonte de Imagem**, escolha Docker **Hub** e para Imagem **e etiqueta,** introduza o nome de repositório copiado em [Publish to Docker Hub](#publish-to-docker-hub).

   ![Configure que é uma Aplicação Web para contentores](media/app-service-web-get-started-windows-container/configure-web-app-continer.png)

    Se tiver uma imagem personalizada noutra localização para a sua aplicação Web, como no [Azure Container Registry](/azure/container-registry/) ou noutro repositório privado, pode configurá-la aí.

1. Selecione **Rever e Criar** e, em seguida, **Criar** e esperar que o Azure crie os recursos necessários.

## <a name="browse-to-the-container-app"></a>Navegar para a aplicação de contentor

Quando a operação do Azure estiver concluída, é apresentada uma caixa de notificação.

![Implantação conseguiu](media/app-service-web-get-started-windows-container/portal-create-finished.png)

1. Clique em **Ir para recurso**.

1. Na visão geral deste recurso, siga o link ao lado do **URL**.

Uma nova página do navegador abre para a seguinte página:

![Aplicativo de recipiente do windows começando](media/app-service-web-get-started-windows-container/app-starting.png)

Aguarde alguns minutos e tente novamente, até ver a home page de ASP.NET predefinida:

![Aplicativo de contentor estoque do Windows em execução](media/app-service-web-get-started-windows-container/app-running-vs.png)

**Parabéns!** Está a executar o seu primeiro contentor do Windows personalizado no Serviço de Aplicações do Azure.

## <a name="see-container-start-up-logs"></a>Ver os registos de arranque do contentor

O contentor do Windows poderá demorar algum tempo até ser carregado. Para ver o progresso, navegue para o seguinte URL, substituindo *\<app_name>* pelo nome da sua aplicação.
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

1. No Visual Studio, no **Solution Explorer,** abra **vistas** > **Home** > **Index.cshtml**.

1. Localize a etiqueta HTML `<div class="jumbotron">` na parte superior e substitua todo o elemento pelo código seguinte:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Para reimplantar para o Azure, clique à direita no projeto **myFirstAzureWebApp** no **Solution Explorer** e escolha **Publicar**.

1. Na página de publicação, selecione **Publicar** e aguarde até a publicação estar concluída.

1. Para dizer ao Serviço de Aplicações para obter a nova imagem do Hub do Docker, reinicie a aplicação. Na página da aplicação no portal, clique em **Reiniciar** > **Sim**.

   ![Reiniciar a aplicação Web no Azure](./media/app-service-web-get-started-windows-container/portal-restart-app.png)

[Navegue novamente para a aplicação de contentor](#browse-to-the-container-app). Quando atualiza a página Web, primeiro, a aplicação deve reverter para a página "A Iniciar" e, em seguida, volta a apresentar a página Web atualizada ao fim de alguns minutos.

![Aplicação Web atualizada no Azure](./media/app-service-web-get-started-windows-container/azure-web-app-updated.png)

## <a name="use-a-different-parent-image"></a>Utilizar uma imagem principal diferente

Você é livre de usar uma imagem personalizada do Docker diferente para executar a sua aplicação. No entanto, deve escolher a [imagem dos pais (imagem base)](https://docs.docker.com/develop/develop-images/baseimages/) certa para o quadro que deseja:

- Para implementar aplicações .NET Framework, utilize uma imagem dos pais com base na versão do Canal de Manutenção de [Longo Prazo (LTSC)](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) do Windows Server Core 2019. 
- Para implementar aplicações .NET Core, utilize uma imagem dos pais com base na versão do Canal [de Assistência Semi-Anual (SAC)](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) do Windows Server Nano 1809. 

O carregamento das imagens principais durante o arranque da aplicação demora algum tempo. No entanto, pode utilizar uma das seguintes imagens principais que já estão em cache no Serviço de Aplicações do Azure para reduzir o tempo de arranque:

- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/):4.7.2 windowsservercore-ltsc2019
- [mcr.microsoft.com/windows/nanoserver](https://hub.docker.com/_/microsoft-windows-nanoserver/):1809 - esta imagem é o recipiente base utilizado em imagens do Microsoft [ASP.NET Core](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/) Microsoft Windows Nano Server.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Migrar para o contentor do Windows no Azure](app-service-web-tutorial-windows-containers-custom-fonts.md)

---
title: 'QuickStart: Recipiente windows (pré-visualização)'
description: Implemente o seu primeiro recipiente windows personalizado para o Serviço de Aplicações Azure. Aproveite a contentorização, personalize o recipiente do Windows da forma que quiser.
ms.topic: quickstart
ms.date: 08/30/2019
ms.custom: mvc, seodec18
ms.openlocfilehash: 0689e46556c4ddfddde073d79886d5cb556fde49
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85205667"
---
# <a name="run-a-custom-windows-container-in-azure-preview"></a>Executar um contentor do Windows personalizado no Azure (Pré-visualização)

O [Serviço de Aplicações do Azure](overview.md) disponibiliza pilhas de aplicações predefinidas no Windows, como ASP.NET ou Node.js, em execução no IIS. O ambiente pré-configurado do Windows bloqueia o sistema operativo a partir de acesso administrativo, instalações de software, alterações na cache de montagem global, e assim por diante. Para mais informações, consulte [a funcionalidade do sistema operativo no Azure App Service](operating-system-functionality.md). Se a sua aplicação precisar de mais acesso do que aquele que o ambiente pré-configurado permite, pode implementar um contentor do Windows personalizado.

Este quickstart mostra como implementar uma aplicação ASP.NET, numa imagem do Windows, para [Docker Hub](https://hub.docker.com/) do Visual Studio. Você executou a aplicação em um recipiente personalizado no Azure App Service.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- <a href="https://hub.docker.com/" target="_blank">Inscrever numa conta do Docker Hub</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Instale o Docker para Windows</a>.
- <a href="https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Mude o Docker para executar contentores do Windows</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Instale o Visual Studio 2019</a> com as **ASP.NET e desenvolvimento web** e cargas de trabalho de desenvolvimento **Azure.** Se já instalou o Visual Studio 2019:

    - Instale as últimas atualizações no Estúdio Visual selecionando **o Help**Check  >  **for Updates**.
    - Adicione as cargas de trabalho no Estúdio Visual selecionando **Ferramentas**  >  **Obter Ferramentas e Funcionalidades**.

## <a name="create-an-aspnet-web-app"></a>Criar uma aplicação Web ASP.NET

Crie uma aplicação web ASP.NET seguindo estes passos:

1. Abra o Estúdio Visual e, em seguida, **selecione Criar um novo projeto.**

1. Na **Criar um novo projeto,** encontrar e escolher ASP.NET **Aplicação Web (.NET Framework)** para C#, em seguida, selecione **Next**.

1. Em **Configurar o seu novo projeto**, nomeie a aplicação _myfirstazurewebapp_, e, em seguida, selecione **Create**.

   ![Configure o seu projeto de aplicação web](./media/app-service-web-get-started-windows-container/configure-web-app-project-container.png)

1. Pode implementar qualquer tipo de aplicação Web ASP.NET no Azure. Para este arranque rápido, escolha o modelo **MVC.**

1. Selecione **o suporte do Docker**e certifique-se de que a autenticação está definida para Não **Autenticação**. Selecione **Criar**.

   ![Criar aplicação web ASP.NET](./media/app-service-web-get-started-windows-container/select-mvc-template-for-container.png)

1. Se o ficheiro _Dockerfile_ não se abrir automaticamente, abra-o a partir do **Explorador de Soluções**.

1. Precisa de uma [imagem dos pais apoiada.](#use-a-different-parent-image) Altere a imagem principal, substituindo a linha `FROM` pelo código seguinte e guarde o ficheiro:

   ```dockerfile
   FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
   ```

1. A partir do menu Visual Studio, selecione **Debug**  >  **Start Without Debugging** para executar a aplicação web localmente.

   ![Executar a aplicação localmente](./media/app-service-web-get-started-windows-container/local-web-app.png)

## <a name="publish-to-docker-hub"></a>Publicar no Hub do Docker

1. No **Solution Explorer,** clique com o botão direito no projeto **myfirstazurewebapp** e selecione **Publicar**.

1. Escolha **o Serviço de Aplicações** e, em seguida, selecione **Publicar.**

1. Em Pick a **publish target**, selecione **Container Registry** and **Docker Hub**, e, em seguida, clique em **Publicar**.

   ![Publicar a partir da página de descrição geral do projeto](./media/app-service-web-get-started-windows-container/publish-to-docker-vs2019.png)

1. Forneça as suas credenciais de conta Docker Hub e **selecione Save**.

   Aguarde pela conclusão da implementação. A página **publicar** agora mostra o nome do repositório para usar mais tarde.

   ![Publicar a partir da página de descrição geral do projeto](./media/app-service-web-get-started-windows-container/published-docker-repository-vs2019.png)

1. Copie este nome de repositório para utilizar mais tarde.

## <a name="create-a-windows-container-app"></a>Criar uma aplicação de contentor do Windows

1. Inicie sessão no [portal do Azure]( https://portal.azure.com).

1. Selecione **Criar um recurso**, no canto superior esquerdo do portal do Azure.

1. Na caixa de pesquisa acima da lista de recursos do Azure Marketplace, procure **a Web App para contentores,** e selecione **Create**.

1. Na **Web App Create,** escolha a sua subscrição e um **Grupo de Recursos.** Pode criar um novo grupo de recursos, se necessário.

1. Forneça um nome de aplicação, como *win-container-demo* e escolha **o Windows** for **Operating System**. Selecione **Seguinte: Docker** para continuar.

   ![Criar uma aplicação web para contentores](media/app-service-web-get-started-windows-container/create-web-app-continer.png)

1. Para **Image Source**, escolha Docker **Hub** e para Imagem **e marque,** insira o nome do repositório que copiou em [Publicar para Docker Hub](#publish-to-docker-hub).

   ![Configurar você é uma web app para contentores](media/app-service-web-get-started-windows-container/configure-web-app-continer.png)

    Se tiver uma imagem personalizada noutra localização para a sua aplicação Web, como no [Azure Container Registry](/azure/container-registry/) ou noutro repositório privado, pode configurá-la aí.

1. Selecione **Rever e criar** e, em seguida, **criar** e esperar que a Azure crie os recursos necessários.

## <a name="browse-to-the-container-app"></a>Navegar para a aplicação de contentor

Quando a operação do Azure estiver concluída, é apresentada uma caixa de notificação.

![Implementação conseguiu](media/app-service-web-get-started-windows-container/portal-create-finished.png)

1. Clique em **Ir para recurso**.

1. Na visão geral deste recurso, siga o link ao lado **do URL**.

Uma nova página do navegador abre para a seguinte página:

![Aplicativo de contentor do Windows começando](media/app-service-web-get-started-windows-container/app-starting.png)

Aguarde alguns minutos e tente novamente, até ver a home page de ASP.NET predefinida:

![Aplicação de contentores do Windows em execução](media/app-service-web-get-started-windows-container/app-running-vs.png)

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

   ![Reiniciar a aplicação Web no Azure](./media/app-service-web-get-started-windows-container/portal-restart-app.png)

[Navegue novamente para a aplicação de contentor](#browse-to-the-container-app). Quando atualiza a página Web, primeiro, a aplicação deve reverter para a página "A Iniciar" e, em seguida, volta a apresentar a página Web atualizada ao fim de alguns minutos.

![Aplicação Web atualizada no Azure](./media/app-service-web-get-started-windows-container/azure-web-app-updated.png)

## <a name="use-a-different-parent-image"></a>Utilizar uma imagem principal diferente

Você é livre de usar uma imagem personalizada diferente do Docker para executar a sua aplicação. No entanto, deve escolher a imagem dos pais certos [(imagem base)](https://docs.docker.com/develop/develop-images/baseimages/) para a estrutura que deseja:

- Para implementar aplicações .NET Framework, utilize uma imagem-mãe baseada na versão do Windows Server Core 2019 [Canal de Manutenção a Longo Prazo (LTSC).](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) 
- Para implementar aplicações .NET Core, utilize uma imagem-mãe baseada na versão [semi-anual do Windows](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) Server Nano 1809 (SAC). 

O carregamento das imagens principais durante o arranque da aplicação demora algum tempo. No entanto, pode utilizar uma das seguintes imagens principais que já estão em cache no Serviço de Aplicações do Azure para reduzir o tempo de arranque:

- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/):4.7.2-windowsservercore-ltsc2019
- [mcr.microsoft.com/windows/nanoserver](https://hub.docker.com/_/microsoft-windows-nanoserver/):1809 - esta imagem é o recipiente base utilizado em todas as imagens [do Microsoft ASP.NET Core](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/) Microsoft Windows Nano Server.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Migrar para o contentor do Windows no Azure](app-service-web-tutorial-windows-containers-custom-fonts.md)

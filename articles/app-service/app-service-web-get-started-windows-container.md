---
title: Executar o contentor personalizado do Windows (pré-visualização) - serviço de aplicações do Azure | Documentos da Microsoft
description: Saiba como implementar um contentor do Windows personalizado no Serviço de Aplicações do Azure.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/12/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 089334d32ca54035abfbf59446366ecbe3378c15
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64919746"
---
# <a name="run-a-custom-windows-container-in-azure-preview"></a>Executar um contentor do Windows personalizado no Azure (Pré-visualização)

O [Serviço de Aplicações do Azure](overview.md) disponibiliza pilhas de aplicações predefinidas no Windows, como ASP.NET ou Node.js, em execução no IIS. O ambiente pré-configurado do Windows bloqueia o sistema operativo contra acesso administrativo, instalações de software, alterações ao Global Assembly Cache, etc. (veja [Operating system functionality on Azure App Service](operating-system-functionality.md) (Funcionalidade do sistema operativo no Serviço de Aplicações do Azure). Se a sua aplicação precisar de mais acesso do que aquele que o ambiente pré-configurado permite, pode implementar um contentor do Windows personalizado. Este início rápido mostra como implementar uma aplicação ASP.NET numa imagem do Windows para [Docker Hub](https://hub.docker.com/) a partir do Visual Studio e executá-lo num contentor personalizado no Serviço de Aplicações do Azure.

![](media/app-service-web-get-started-windows-container/app-running-vs.png)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- <a href="https://hub.docker.com/" target="_blank">Inscrever numa conta do Docker Hub</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Instale o Docker para Windows</a>.
- <a href="https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Mude o Docker para executar contentores do Windows</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Instale o Visual Studio 2017</a> com as cargas de trabalho **ASP.NET e de desenvolvimento na Web** e de **desenvolvimento no Azure**. Se já instalou o Visual Studio 2017:
    - Instale as atualizações mais recentes do Visual Studio ao clicar em **Ajuda** > **Procurar Atualizações**.
    - Adicione as cargas de trabalho ao Visual Studio, ao clicar em **Ferramentas** > **Obter Ferramentas e Funcionalidades**.

## <a name="create-an-aspnet-web-app"></a>Criar uma aplicação Web ASP.NET

No Visual Studio, crie um projeto ao selecionar **Ficheiro > Novo > Projeto**. 

Na caixa de diálogo **Novo projeto**, clique em **Visual C# > Web > Aplicação Web ASP.NET (.NET Framework)**.

Atribua à aplicação o nome _myFirstAzureWebApp_ e clique em **OK**.
   
![Caixa de diálogo Novo Projeto](./media/app-service-web-get-started-windows-container/new-project.png)

Pode implementar qualquer tipo de aplicação Web ASP.NET no Azure. Neste guia de início rápido, selecione o modelo **MVC** e confirme se a autenticação está definida como **Sem Autenticação**.

Selecione **Ativar suporte de Docker Compose** .

Selecione **OK**.

![Caixa de diálogo Novo Projeto ASP.NET](./media/app-service-web-get-started-windows-container/select-mvc-template.png)

Se o ficheiro _Dockerfile_ não se abrir automaticamente, abra-o a partir do **Explorador de Soluções**.

Tem de utilizar uma [ imagem principal suportada](#use-a-different-parent-image). Altere a imagem principal, substituindo a linha `FROM` pelo código seguinte e guarde o ficheiro:

```Dockerfile
FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
```

No menu, selecione **Depurar > Iniciar sem depuração** para executar a aplicação Web localmente.

![Executar a aplicação localmente](./media/app-service-web-get-started-windows-container/local-web-app.png)

## <a name="publish-to-docker-hub"></a>Publicar no Hub do Docker

No **Explorador de Soluções**, clique com o botão direito do rato no projeto **myFirstAzureWebApp** e selecione **Publicar**.

![Publicar a partir do Explorador de Soluções](./media/app-service-web-get-started-windows-container/solution-explorer-publish.png)

O assistente de publicação é iniciado automaticamente. Selecione **Registo de Contentores** > **Hub do Docker** > **Publicar**.

![Publicar a partir da página de descrição geral do projeto](./media/app-service-web-get-started-windows-container/publish-to-docker.png)

Forneça as suas credenciais da conta do Hub de Docker e clique em **Guardar**. 

Aguarde pela conclusão da implementação. A página **Publicar** mostra agora o nome do repositório que irá utilizar mais tarde no Serviço de Aplicações.

![Publicar a partir da página de descrição geral do projeto](./media/app-service-web-get-started-windows-container/published-docker-repository.png)

Copie este nome de repositório para utilizar mais tarde.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-windows-container-app"></a>Criar uma aplicação de contentor do Windows

1. Selecione **Criar um recurso**, no canto superior esquerdo do portal do Azure.

2. Na caixa de pesquisa acima da lista de recursos do Azure Marketplace, procure e selecione **Aplicação Web para Contentores**.

3. Indique um nome para a aplicação, como *win-container-demo*, aceite as predefinições para criar um grupo de recursos novo e clique em **Windows (Pré-visualização)**, na caixa **SO**.

    ![](media/app-service-web-get-started-windows-container/portal-create-page.png)

4. Clique em **Plano/localização do Serviço de Aplicações** > **Criar novo** para criar um plano do Serviço de Aplicações. Dê um nome ao plano novo, aceite as predefinições e clique em **OK**.

    ![](media/app-service-web-get-started-windows-container/portal-create-plan.png)

5. Clique em **Configurar contentor**. Em **Etiqueta de imagem ou opcional**, utilize o nome do repositório que copiou em [Publicar no Hub do Docker](#publish-to-docker-hub)e, em seguida, clique em **OK**.

    ![](media/app-service-web-get-started-windows-container/portal-configure-container-vs.png)

    Se tiver uma imagem personalizada noutra localização para a sua aplicação Web, como no [Azure Container Registry](/azure/container-registry/) ou noutro repositório privado, pode configurá-la aí.

6. Clique em **Criar** e aguarde que o Azure crie os recursos necessários.

## <a name="browse-to-the-container-app"></a>Navegar para a aplicação de contentor

Quando a operação do Azure estiver concluída, é apresentada uma caixa de notificação.

![](media/app-service-web-get-started-windows-container/portal-create-finished.png)

1. Clique em **Ir para recurso**.

2. Na página da aplicação, clique na ligação em **URL**.

É aberta uma página nova do browser na seguinte página:

![](media/app-service-web-get-started-windows-container/app-starting.png)

Aguarde alguns minutos e tente novamente, até ver a home page de ASP.NET predefinida:

![](media/app-service-web-get-started-windows-container/app-running-vs.png)

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

No **Explorador de Soluções**, abra _Views\Home\Index.cshtml_.

Localize a etiqueta HTML `<div class="jumbotron">` na parte superior e substitua todo o elemento pelo código seguinte:

```HTML
<div class="jumbotron">
    <h1>ASP.NET in Azure!</h1>
    <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
</div>
```

Para voltar a implementar no Azure, clique com o botão direito do rato no projeto **myFirstAzureWebApp**, no **Explorador de Soluções** e selecione **Publicar**.

Na página de publicação, selecione **Publicar** e aguarde até a publicação estar concluída.

Para dizer ao Serviço de Aplicações para obter a nova imagem do Hub do Docker, reinicie a aplicação. Na página da aplicação no portal, clique em **Reiniciar** > **Sim**.

![Reiniciar a aplicação Web no Azure](./media/app-service-web-get-started-windows-container/portal-restart-app.png)

[Navegue novamente para a aplicação de contentor](#browse-to-the-container-app). Quando atualiza a página Web, primeiro, a aplicação deve reverter para a página "A Iniciar" e, em seguida, volta a apresentar a página Web atualizada ao fim de alguns minutos.

![Aplicação Web atualizada no Azure](./media/app-service-web-get-started-windows-container/azure-web-app-updated.png)

## <a name="use-a-different-parent-image"></a>Utilizar uma imagem principal diferente

Pode utilizar outra imagem do Docker personalizada para executar a sua aplicação. Contudo, tem de escolher a [imagem principal](https://docs.docker.com/develop/develop-images/baseimages/) certa para a arquitetura que pretende: 

- Para implementar aplicações de .NET Framework, utilizar uma imagem de principal com base no núcleo de servidor Windows 2019 [canal de manutenção de longo prazo (LTSC)](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) de versão. 
- Para implementar aplicações de .NET Core, utilize uma imagem principal com base no 1809 de Nano do Windows Server [canal semi-anual de manutenção (SAC)](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) de versão. 

O carregamento das imagens principais durante o arranque da aplicação demora algum tempo. No entanto, pode utilizar uma das seguintes imagens principais que já estão em cache no Serviço de Aplicações do Azure para reduzir o tempo de arranque:

- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/):4.7.2-windowsservercore-ltsc2019
- [MCR.microsoft.com/Windows/nanoserver](https://hub.docker.com/_/microsoft-windows-nanoserver/): 1809 - este é o contentor de base utilizado em todo o Microsoft [ASP.NET Core](https://hub.docker.com/_microsoft-dotnet-cores-aspnet) imagens do servidor de Nano do Microsoft Windows.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Migrar para o contentor do Windows no Azure](app-service-web-tutorial-windows-containers-custom-fonts.md)

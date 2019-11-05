---
title: Criar C# ASP.NET Core aplicativo web-Azure app serviço | Microsoft Docs
description: Saiba como executar aplicativos Web no serviço de Azure App implantando o aplicativo C# web padrão ASP.NET Core.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/30/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 919e717811b3b04ca7407772fcf11f293d58508c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73471599"
---
# <a name="create-an-aspnet-core-web-app-in-azure"></a>Criar uma aplicação Web ASP.NET Core no Azure

> [!NOTE]
> Este artigo implementa uma aplicação no Serviço de Aplicações no Windows. Para implementar um Serviço de Aplicações no _Linux_, consulte [Criar uma aplicação Web .NET Core no Serviço de Aplicações no Linux](./containers/quickstart-dotnetcore.md).
>

O [Serviço de Aplicações do Azure](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática.

Este guia de início rápido mostra como implantar seu primeiro aplicativo Web ASP.NET Core no serviço Azure App. Quando tiver terminado, você terá um grupo de recursos que consiste em um plano do serviço de aplicativo e um aplicativo do serviço de aplicativo com um aplicativo Web implantado.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, instale o <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> com a carga de trabalho de **desenvolvimento da web e do ASP.net** .

Se você já instalou o Visual Studio 2019:

- Instale as atualizações mais recentes no Visual Studio selecionando **ajuda** > **verificar se há atualizações**.
- Adicione a carga de trabalho selecionando **ferramentas** > **obter ferramentas e recursos**.

## <a name="create-an-aspnet-core-web-app"></a>Criar uma aplicação Web ASP.NET Core

Crie um aplicativo Web ASP.NET Core seguindo estas etapas:

1. Abra o Visual Studio e, em seguida, selecione **criar um novo projeto**.

1. Em **criar um novo projeto**, localize e escolha **ASP.NET Core aplicativo Web** para C#e, em seguida, selecione **Avançar**.

1. Em **configurar seu novo projeto**, nomeie o aplicativo _myFirstAzureWebApp_e, em seguida, selecione **criar**.

   ![Configurar seu projeto de aplicativo Web](./media/app-service-web-get-started-dotnet/configure-web-app-project.png)

1. Para este guia de início rápido, escolha o modelo de **aplicativo Web** . Verifique se a autenticação está definida como **sem autenticação** e se nenhuma outra opção está selecionada. Selecione **Criar**.

   ![Selecione ASP.NET Core Razor Pages para este tutorial](./media/app-service-web-get-started-dotnet/aspnet-razor-pages-app.png)

    Pode implementar qualquer tipo de aplicação Web ASP.NET Core no Azure.

1. No menu do Visual Studio, selecione **depurar** > **Iniciar sem Depurar** para executar o aplicativo Web localmente.

   ![Executar a aplicação localmente](./media/app-service-web-get-started-dotnet/razor-web-app-running-locally.png)

## <a name="publish-your-web-app"></a>Publicar seu aplicativo Web

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto **MyFirstAzureWebApp** e selecione **publicar**.

1. Escolha **serviço de aplicativo** e, em seguida, selecione **publicar**.

   ![Publicar a partir da página de descrição geral do projeto](./media/app-service-web-get-started-dotnet/publish-app-vs2019.png)

1. No **serviço de aplicativo criar novo**, suas opções dependem se você já entrou no Azure e se tem uma conta do Visual Studio vinculada a uma conta do Azure. Selecione **Adicionar uma conta** ou **entrar** para entrar em sua assinatura do Azure. Se você já tiver entrado, selecione a conta desejada.

   > [!NOTE]
   > Se já tiver sessão iniciada, não selecione ainda **Criar**.
   >

   ![Iniciar sessão no Azure](./media/app-service-web-get-started-dotnet/sign-in-azure-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. Para **grupo de recursos**, selecione **novo**.

1. Em **novo nome do grupo de recursos**, insira *MyResource* Group e selecione **OK**.

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. Para o **plano de hospedagem**, selecione **novo**.

1. Na caixa de diálogo **Configurar plano de hospedagem** , insira os valores da tabela a seguir e selecione **OK**.

   | Definição | Valor sugerido | Descrição |
   |-|-|-|
   |Plano do Serviço de Aplicações| myAppServicePlan | Nome do plano de serviço de aplicações. |
   | Localização | Europa Ocidental | O centro de dados onde o a aplicação Web está alojada. |
   | Tamanho | Gratuito | O [escalão de preço](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) determina as funcionalidades do alojamento. |

   ![Criar plano do App Service](./media/app-service-web-get-started-dotnet/app-service-plan-vs2019.png)

1. Em **nome**, insira um nome de aplicativo exclusivo que inclua somente os caracteres válidos são `a-z`, `A-Z`, `0-9`e `-`. Você pode aceitar o nome exclusivo gerado automaticamente. O URL da aplicação Web é `http://<app_name>.azurewebsites.net`, em que `<app_name>` é o nome da aplicação.

   ![Configurar o nome da aplicação](./media/app-service-web-get-started-dotnet/web-app-name-vs2019.png)

1. Selecione **Criar** para começar a criar os recursos do Azure.

Depois de concluir o assistente, este publica a aplicação Web ASP.NET Core no Azure e, em seguida, inicia a aplicação no browser predefinido.

![Aplicação Web ASP.NET publicada no Azure](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

O nome do aplicativo especificado na página **criar novo do serviço de aplicativo** é usado como o prefixo de URL no formato `http://<app_name>.azurewebsites.net`.

**Parabéns!** Seu aplicativo Web ASP.NET Core está em execução no serviço Azure App.

## <a name="update-the-app-and-redeploy"></a>Atualizar a aplicação e reimplementar

1. No **Gerenciador de soluções**, em seu projeto, abra **páginas** > **index. cshtml**.

1. Substitua as duas tags `<div>` pelo seguinte código:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Para voltar a implementar no Azure, clique com o botão direito do rato no projeto **myFirstAzureWebApp**, no **Explorador de Soluções** e selecione **Publicar**.

1. Na página Resumo da **publicação** , selecione **publicar**.

   ![Página de resumo da publicação do Visual Studio](./media/app-service-web-get-started-dotnet/publish-summary-page-vs2019.png)

Quando a publicação estiver concluída, o Visual Studio inicia um browser para o URL da aplicação Web.

![Aplicação Web ASP.NET atualizada no Azure](./media/app-service-web-get-started-dotnet/web-app-running-live-updated.png)

## <a name="manage-the-azure-app"></a>Gerenciar o aplicativo do Azure

Para gerenciar o aplicativo Web, vá para o [portal do Azure](https://portal.azure.com)e procure e selecione serviços de **aplicativos**.

![Selecionar serviços de aplicativos](./media/app-service-web-get-started-dotnet/app-services.png)

Na página **serviços de aplicativos** , selecione o nome do seu aplicativo Web.

![Navegação do portal para a aplicação do Azure](./media/app-service-web-get-started-dotnet/access-portal-vs2019.png)

É apresentada a página de descrição geral da sua aplicação Web. Aqui, você pode fazer gerenciamento básico, como procurar, parar, iniciar, reiniciar e excluir.

![Serviço de aplicativo no portal do Azure](./media/app-service-web-get-started-dotnet/web-app-general-vs2019.png)

O menu à esquerda fornece diferentes páginas para configurar a sua aplicação.

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [ASP.NET Core com a Base de Dados SQL](app-service-web-tutorial-dotnetcore-sqldb.md)

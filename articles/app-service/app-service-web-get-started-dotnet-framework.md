---
title: Criar C# aplicativo web do ASP.NET Framework-serviço de Azure app | Microsoft Docs
description: Saiba como executar aplicações Web no serviço de aplicações do Azure com a implementação da aplicação Web ASP.NET C# predefinida.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 04a1becf-7756-4d4e-92d8-d9471c263d23
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/30/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 4f479ad60d74f1c7381b5fb776c5508aaa0785f1
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242152"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>Criar uma aplicação Web ASP.NET Framework no Azure

O [Serviço de Aplicações do Azure](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática.

Este guia de início rápido mostra como implantar seu primeiro aplicativo Web ASP.NET no serviço Azure App. Quando tiver terminado, você terá um plano do serviço de aplicativo. Você também terá um aplicativo do serviço de aplicativo com um aplicativo Web implantado.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, instale o <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> com a carga de trabalho de **desenvolvimento da web e do ASP.net** .

Se você já instalou o Visual Studio 2019:

- Instale as atualizações mais recentes no Visual Studio selecionando **ajuda** > **para verificar**se há atualizações.
- Adicione a carga de trabalho selecionando **ferramentas** > **obter ferramentas e recursos**.

## Criar um aplicativo Web ASP.NET<a name="create-and-publish-the-web-app"></a>

Crie um aplicativo Web ASP.NET seguindo estas etapas:

1. Abra o Visual Studio e, em seguida, selecione **criar um novo projeto**.

1. Em **criar um novo projeto**, localize e escolha **aplicativo Web ASP.net (.NET Framework)** para C#e, em seguida, selecione **Avançar**.

1. Em **configurar seu novo projeto**, nomeie o aplicativo _myFirstAzureWebApp_e, em seguida, selecione **criar**.

   ![Configurar seu projeto de aplicativo Web](./media/app-service-web-get-started-dotnet-framework/configure-web-app-project-framework.png)

1. Pode implementar qualquer tipo de aplicação Web ASP.NET no Azure. Para este guia de início rápido, escolha o modelo **MVC** . 

1. Verifique se a autenticação está definida como **sem autenticação**. Selecione **Criar**.

   ![Criar aplicativo Web ASP.NET](./media/app-service-web-get-started-dotnet-framework/select-mvc-template-vs2019.png)

1. No menu do Visual Studio, selecione **depurar** > **Iniciar sem Depurar** para executar o aplicativo Web localmente.

   ![Executar a aplicação localmente](./media/app-service-web-get-started-dotnet-framework/local-web-app.png)

## Publicar seu aplicativo Web<a name="launch-the-publish-wizard"></a>

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto **MyFirstAzureWebApp** e selecione **publicar**.

1. Escolha **serviço de aplicativo** e, em seguida, selecione **publicar**.

   ![Publicar a partir da página de descrição geral do projeto](./media/app-service-web-get-started-dotnet-framework/publish-app-framework-vs2019.png)

1. No **serviço de aplicativo criar novo**, suas opções dependem se você já entrou no Azure e se tem uma conta do Visual Studio vinculada a uma conta do Azure. Selecione **Adicionar uma conta** ou **entrar** para entrar em sua assinatura do Azure. Se você já tiver entrado, selecione a conta desejada.

   > [!NOTE]
   > Se já tiver sessão iniciada, não selecione ainda **Criar**.
   >
   >

   ![Iniciar sessão no Azure](./media/app-service-web-get-started-dotnet-framework/sign-in-azure-framework-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. Para **grupo de recursos**, selecione **novo**.

1. Em **novo nome do grupo de recursos**, insira MyResource Group e selecione **OK**.

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. Para o **plano de hospedagem**, selecione **novo**.

1. Na caixa de diálogo **Configurar plano de hospedagem** , insira os valores da tabela a seguir e selecione **OK**.

   | Definição | Valor sugerido | Descrição |
   |-|-|-|
   |Plano do Serviço de Aplicações| myAppServicePlan | Nome do plano de serviço de aplicações. |
   | Location | Europa Ocidental | O centro de dados onde o a aplicação Web está alojada. |
   | Size | Gratuito | O [escalão de preço](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) determina as funcionalidades do alojamento. |

   ![Criar plano do App Service](./media/app-service-web-get-started-dotnet-framework/app-service-plan-framework-vs2019.png)

1. Em **nome**, insira um nome de aplicativo exclusivo que inclua somente os caracteres válidos `a-z`são `A-Z`, `0-9`, e `-`. Você pode aceitar o nome exclusivo gerado automaticamente. O URL da aplicação Web é `http://<app_name>.azurewebsites.net`, em que `<app_name>` é o nome da aplicação.

   ![Configurar o nome da aplicação](./media/app-service-web-get-started-dotnet-framework/web-app-name-framework-vs2019.png)

1. Selecione **Criar** para começar a criar os recursos do Azure.

Depois de concluir o assistente, este publica a aplicação Web ASP.NET no Azure e, em seguida, inicia a aplicação no browser predefinido.

![Aplicação Web ASP.NET publicada no Azure](./media/app-service-web-get-started-dotnet-framework/published-azure-web-app.png)

O nome do aplicativo especificado na página **criar novo do serviço de aplicativo** é usado como o prefixo de URL `http://<app_name>.azurewebsites.net`no formato.

**Parabéns!** Seu aplicativo Web ASP.NET está em execução no serviço Azure App.

## <a name="update-the-app-and-redeploy"></a>Atualizar a aplicação e reimplementar

1. No **Gerenciador de soluções**, em seu projeto, abra **views** > **Home** > **index. cshtml**.

1. Localize a etiqueta HTML `<div class="jumbotron">` na parte superior e substitua todo o elemento pelo código seguinte:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Para voltar a implementar no Azure, clique com o botão direito do rato no projeto **myFirstAzureWebApp**, no **Explorador de Soluções** e selecione **Publicar**.

1. Na página Resumo da **publicação** , selecione **publicar**.

   ![Página de resumo do Visual Studio para publicação](./media/app-service-web-get-started-dotnet-framework/publish-summary-page-framework-vs2019.png)

Quando a publicação estiver concluída, o Visual Studio inicia um browser para o URL da aplicação Web.

![Aplicação Web ASP.NET atualizada no Azure](./media/app-service-web-get-started-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-app"></a>Gerenciar o aplicativo do Azure

1. Aceda ao <a href="https://portal.azure.com" target="_blank">portal do Azure</a> para gerir a aplicação Web.

1. No menu à esquerda, selecione **serviços de aplicativos**e, em seguida, selecione o nome do seu aplicativo do Azure.

   ![Navegação do portal para a aplicação do Azure](./media/app-service-web-get-started-dotnet-framework/access-portal-framework-vs2019.png)

   É apresentada a página de descrição geral da sua aplicação Web. Aqui, você pode fazer gerenciamento básico, como procurar, parar, iniciar, reiniciar e excluir.

   ![Visão geral do serviço de aplicativo no portal do Azure](./media/app-service-web-get-started-dotnet-framework/web-app-general-framework-vs2019.png)

   O menu à esquerda fornece diferentes páginas para configurar a sua aplicação.

## <a name="video"></a>Vídeo

Assista ao vídeo para ver este guia de introdução em ação e, em seguida, siga os passos para publicar a sua primeira aplicação .NET no Azure.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-for-NET-Developers/Create-a-NET-app-in-Azure-Quickstart/player]

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [ASP.NET com Base de Dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md)

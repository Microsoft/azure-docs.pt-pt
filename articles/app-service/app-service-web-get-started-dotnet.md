---
title: 'Quickstart: Criar uma app C# ASP.NET Core'
description: Saiba como executar aplicações web no Azure App Service implementando o modelo de aplicação web C# ASP.NET Core padrão do Visual Studio.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 03/17/2020
ms.custom: mvc, devcenter, vs-azure, seodec18
ms.openlocfilehash: be7c4a2fb65f913bc97617af1a0f21ee8fcca714
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313286"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Quickstart: Criar uma aplicação web ASP.NET Core em Azure

Neste arranque rápido, aprenderá a criar e implementar a sua primeira ASP.NET aplicação web Core para [o Azure App Service.](overview.md) 

Quando terminar, terá um grupo de recursos Azure composto por um plano de hospedagem do Serviço de Aplicações e um Serviço de Aplicações com uma aplicação web implementada.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/dotnet/)
- Este quickstart implementa uma aplicação para o Serviço de Aplicações no Windows. Para implementar um Serviço de Aplicações no _Linux_, consulte [Criar uma aplicação Web .NET Core no Serviço de Aplicações no Linux](./containers/quickstart-dotnetcore.md).
- Instale o <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> com a **carga de trabalho de ASP.NET e desenvolvimento web.**

  Se já instalou o Visual Studio 2019:

  - Instale as últimas atualizações no Estúdio Visual selecionando **A Verificação de Ajuda** > **para Atualizações**.
  - Adicione a carga de trabalho selecionando **ferramentas** > **obter ferramentas e funcionalidades**.


## <a name="create-an-aspnet-core-web-app"></a>Criar uma aplicação Web ASP.NET Core

Crie uma aplicação web ASP.NET Core no Estúdio Visual seguindo estes passos:

1. Open Visual Studio e selecione **Criar um novo projeto.**

1. Em **Criar um novo projeto,** selecione ASP.NET Core Web **Application** e confirme que **C#** está listado nos idiomas para essa escolha e, em seguida, selecione **Next**.

1. Na **Configuração do seu novo projeto,** nomeie o seu projeto de aplicação web *myFirstAzureWebApp*, e selecione **Create**.

   ![Configure o seu projeto de aplicação web](./media/app-service-web-get-started-dotnet/configure-web-app-project.png)

1. Pode implementar qualquer tipo de ASP.NET aplicação web Core para o Azure, mas para este arranque rápido, escolha o modelo de **Aplicação Web.** Certifique-se de que a **Autenticação** está definida para **não autenticação,** e que nenhuma outra opção é selecionada. Em seguida, selecione **Criar**.

   ![Criar uma nova ASP.NET web app Core](./media/app-service-web-get-started-dotnet/create-aspnet-core-web-app.png) 
   
1. A partir do menu do Estúdio Visual, selecione **Debug** > **Start Without Debugging** para executar a sua aplicação web localmente.

   ![Aplicativo web funcionando localmente](./media/app-service-web-get-started-dotnet/web-app-running-locally.png)

## <a name="publish-your-web-app"></a>Publique a sua aplicação web

Para publicar a sua aplicação web, primeiro tem de criar e configurar um novo Serviço de Aplicações para o que pode publicar a sua aplicação. 

Como parte da configuração do Serviço de Aplicações, irá criar:

- Um novo grupo de [recursos](https://docs.microsoft.com/azure/azure-resource-manager/management/overview#terminology) para conter todos os recursos do Azure para o serviço.
- Um novo Plano de [Hospedagem](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) que especifica a localização, tamanho e funcionalidades da quinta do servidor web que acolhe a sua aplicação.

Siga estes passos para criar o seu Serviço de Aplicações e publique a sua aplicação web:

1. No **Solution Explorer,** clique à direita no projeto **myFirstAzureWebApp** e selecione **Publish**. Se ainda não se inscreveu na sua conta Azure do Visual Studio, selecione **adicionar uma conta** ou iniciar **sessão**. Também pode criar uma conta Azure gratuita.

1. Na caixa de diálogo de **alvo Pick,** escolha **o Serviço de Aplicações,** selecione **Criar Novo**e, em seguida, selecione **Criar Perfil**.

   ![Escolher um destino de publicação](./media/app-service-web-get-started-dotnet/pick-publish-target-vs2019.png)

1. No Serviço de **Aplicações: Crie um novo** diálogo, forneça um **Nome** globalmente único para a sua aplicação, aceitando o nome predefinido ou inserindo um novo nome. Os caracteres `a-z`válidos são: , `A-Z`, `0-9`e `-`. Este **Nome** é usado como prefixo URL para `http://<app_name>.azurewebsites.net`a sua aplicação web no formato .

1. Para **Subscrição**, aceite a subscrição listada ou selecione uma nova da lista de drop-down.

1. No **grupo Recursos,** selecione **New**. Em novo nome de **grupo de recursos,** insira *o myResourceGroup* e selecione **OK**. 

1. Para plano de **hospedagem,** selecione **New**. 

1. No **Plano de Acolhimento: Criar um novo** diálogo, introduza os valores especificados no quadro seguinte:

   | Definição  | Valor sugerido | Descrição |
   | -------- | --------------- | ----------- |
   | **Plano de Hospedagem**  | *myFirstAzureWebAppPlan* | Nome do plano de serviço de aplicações. |
   | **Localização**      | *Europa ocidental* | O centro de dados onde o a aplicação Web está alojada. |
   | **Tamanho**          | *Gratuito* | O [escalão de preço](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) determina as funcionalidades do alojamento. |
   
   ![Criar novo Plano de Hospedagem](./media/app-service-web-get-started-dotnet/create-new-hosting-plan-vs2019.png)

1. Deixe **as informações de aplicação** *definidas*para nenhuma .

1. No **Serviço de Aplicações: Criar uma nova** caixa de diálogo, selecione **Criar** para começar a criar os recursos Azure.

   ![Criar um novo serviço de aplicações](./media/app-service-web-get-started-dotnet/create-new-app-service-vs2019.png)

1. Assim que o assistente estiver concluído, **selecione Publicar**.

   ![Publicar web app para Azure](./media/app-service-web-get-started-dotnet/publish-web-app-vs2019.png)

   O Visual Studio publica a sua ASP.NET aplicação web Core para o Azure, e lança a aplicação no seu navegador padrão. 

   ![Publicado ASP.NET aplicação web em funcionamento no Azure](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

**Parabéns!** A sua aplicação web ASP.NET Core está a funcionar ao vivo no Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Atualizar a aplicação e reimplementar

Siga estes passos para atualizar e reimplementar a sua aplicação web:

1. No **Solution Explorer,** no âmbito do seu projeto, abra o**Índice de Páginas.cshtml** **Pages** > .

1. Substitua as duas tags `<div>` pelo seguinte código:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Para voltar a implementar no Azure, clique com o botão direito do rato no projeto **myFirstAzureWebApp**, no **Explorador de Soluções** e selecione **Publicar**.

1. Na página resumo da **Publicação,** selecione **Publicar**.

   ![Publicar atualização para a web app](./media/app-service-web-get-started-dotnet/publish-update-to-web-app-vs2019.png)

Quando a publicação estiver concluída, o Visual Studio inicia um browser para o URL da aplicação Web.

![App web atualizada ASP.NET em execução em Azure](./media/app-service-web-get-started-dotnet/updated-web-app-running-live.png)

## <a name="manage-the-azure-app"></a>Gerir a app Azure

Para gerir a sua aplicação web, vá ao [portal Azure](https://portal.azure.com)e procure e selecione Serviços de **Aplicações.**

![Selecione Serviços de Aplicações](./media/app-service-web-get-started-dotnet/app-services.png)

Na página de Serviços de **Aplicações,** selecione o nome da sua aplicação web.

![Navegação do portal para a aplicação do Azure](./media/app-service-web-get-started-dotnet/select-app-service.png)

A página **'Overview'** da sua aplicação web contém opções para a gestão básica como navegar, parar, começar, reiniciar e eliminar. O menu esquerdo fornece mais páginas para configurar a sua aplicação.

![Serviço de Aplicações no portal Azure](./media/app-service-web-get-started-dotnet/web-app-overview-page.png)

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, usou o Visual Studio para criar e implementar uma aplicação web ASP.NET Core para o Azure App Service.

Avançar para o próximo artigo para aprender a criar uma aplicação .NET Core e conectá-la a uma Base de Dados SQL:

> [!div class="nextstepaction"]
> [ASP.NET Core com a Base de Dados SQL](app-service-web-tutorial-dotnetcore-sqldb.md)

---
title: 'Quickstart: Criar uma aplicação C# ASP.NET Core'
description: Aprenda a executar aplicações web no Azure App Service implementando o modelo de aplicação web C# C# padrão ASP.NET Core do Visual Studio.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 08/06/2020
ms.custom: mvc, devcenter, vs-azure, seodec18
ms.openlocfilehash: 77ff1e907b15b129ef03d7ce799631d6d0a9671d
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87986190"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Quickstart: Criar uma aplicação web core ASP.NET em Azure

Neste arranque rápido, você vai aprender a criar e implementar a sua primeira aplicação web core ASP.NET para [o Azure App Service](overview.md). 

Quando terminar, terá um grupo de recursos Azure composto por um plano de hospedagem do App Service e um Serviço de Aplicações com uma aplicação web implementada.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/dotnet/)
- Este quickstart implementa uma aplicação para o Serviço de Aplicações no Windows. Para implementar um Serviço de Aplicações no _Linux_, consulte [Criar uma aplicação Web .NET Core no Serviço de Aplicações no Linux](./containers/quickstart-dotnetcore.md).
- Instale <a href="https://www.visualstudio.com/downloads/" target="_blank">o Visual Studio 2019</a> com a ASP.NET e a carga de trabalho **de desenvolvimento web.**

  Se já instalou o Visual Studio 2019:

  - Instale as últimas atualizações no Estúdio Visual selecionando **o Help**Check  >  **for Updates**.
  - Adicione a carga de trabalho selecionando **Ferramentas**  >  **Obter Ferramentas e Funcionalidades**.


## <a name="create-an-aspnet-core-web-app"></a>Criar uma aplicação Web ASP.NET Core

Crie uma aplicação web core ASP.NET no Estúdio Visual seguindo estes passos:

1. Abra o Estúdio Visual e selecione **Criar um novo projeto.**

1. In **Create a new project**, selecione ASP.NET Core Web **Application** e confirme que **C#** está listado nos idiomas para essa escolha e, em seguida, selecione **Next**.

1. Em **Configurar o seu novo projeto,** nomeie o seu projeto de aplicação web *myFirstAzureWebApp*e selecione **Create**.

   ![Configure o seu projeto de aplicação web](./media/app-service-web-get-started-dotnet/configure-web-app-project.png)

1. Você pode implementar qualquer tipo de ASP.NET web app Core para Azure, mas para este arranque rápido, escolha o modelo **de Aplicação Web.** Certifique-se de que a **autenticação** está definida para **Não Autenticação**e que não está selecionada nenhuma outra opção. Em seguida, selecione **Criar**.

   ![Criar uma nova aplicação web core ASP.NET](./media/app-service-web-get-started-dotnet/create-aspnet-core-web-app.png) 
   
1. A partir do menu Visual Studio, selecione **Debug**  >  **Start Without Debugging** para executar a sua aplicação web localmente.

   ![Aplicativo web correndo localmente](./media/app-service-web-get-started-dotnet/web-app-running-locally.png)

## <a name="publish-your-web-app"></a>Publique a sua aplicação web

Para publicar a sua aplicação web, tem primeiro de criar e configurar um novo Serviço de Aplicações para o quais pode publicar a sua aplicação. 

Como parte da criação do Serviço de Aplicações, irá criar:

- Um novo grupo de [recursos](https://docs.microsoft.com/azure/azure-resource-manager/management/overview#terminology) para conter todos os recursos da Azure para o serviço.
- Um novo [Plano de Hospedagem](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) que especifica a localização, tamanho e funcionalidades da fazenda de servidores web que hospeda a sua aplicação.

Siga estes passos para criar o seu Serviço de Aplicações e publique a sua aplicação web:

1. No **Solution Explorer,** clique com o botão direito no **projeto myFirstAzureWebApp** e selecione **Publicar**. Se ainda não se inscreveu na sua conta Azure do Visual Studio, selecione **Adicione uma conta** ou **inscreva-se em**. Também pode criar uma conta Azure gratuita.

1. Na **caixa de** diálogo alvo de publicação, escolha **o Serviço de Aplicações,** selecione **Create New**e, em seguida, selecione **Criar Perfil**.

   ![Escolher um destino de publicação](./media/app-service-web-get-started-dotnet/pick-publish-target-vs2019.png)

1. No Serviço de **Aplicações: Crie** um novo diálogo, forneça um **Nome** globalmente único para a sua aplicação, aceitando o nome padrão ou introduzindo um novo nome. Os caracteres válidos são: `a-z` `A-Z` , , e `0-9` `-` . Este **Nome** é usado como prefixo URL para a sua aplicação web no formato `http://<app_name>.azurewebsites.net` .

1. Para **Subscrição**, aceite a subscrição listada ou selecione uma nova da lista de drop-down.

1. No **grupo de recursos**, selecione **New**. No **nome do grupo de novos recursos,** insira o *myResourceGroup* e selecione **OK**. 

1. Para **o Plano de Hospedagem**, selecione **New**. 

1. No **Plano de Hospedagem: Criar novo** diálogo, introduzir os valores especificados no quadro seguinte:

   | Definição  | Valor sugerido | Descrição |
   | -------- | --------------- | ----------- |
   | **Plano de Alojamento**  | *myFirstAzureWebAppPlan* | Nome do plano de serviço de aplicações. |
   | **Localização**      | *Europa Ocidental* | O centro de dados onde o a aplicação Web está alojada. |
   | **Tamanho**          | *Gratuito* | O [escalão de preço](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) determina as funcionalidades do alojamento. |
   
   ![Criar novo Plano de Hospedagem](./media/app-service-web-get-started-dotnet/create-new-hosting-plan-vs2019.png)

1. Deixe **os Insights de Aplicação** *definidos*para Nenhum .

1. No Serviço de **Aplicações: Crie uma nova** caixa de diálogo, selecione **Criar** para começar a criar os recursos Azure.

   ![Criar novo serviço de aplicações](./media/app-service-web-get-started-dotnet/create-new-app-service-vs2019.png)

1. Assim que o assistente estiver completo, **selecione Publicar**.

   ![Publique aplicativo web para a Azure](./media/app-service-web-get-started-dotnet/publish-web-app-vs2019.png)

   O Visual Studio publica a sua aplicação web Core ASP.NET para o Azure e lança a aplicação no seu navegador padrão. 

   ![Publicado ASP.NET web app em execução em Azure](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

**Parabéns!** A sua aplicação web core ASP.NET está a ser executada ao vivo no Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Atualizar a aplicação e reimplementar

Siga estes passos para atualizar e redistribuir a sua aplicação web:

1. No **Solution Explorer,** no âmbito do seu projeto, abra **as páginas**  >  **Index.cshtml**.

1. Substitua toda a `<div>` etiqueta pelo seguinte código:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Para voltar a implementar no Azure, clique com o botão direito do rato no projeto **myFirstAzureWebApp**, no **Explorador de Soluções** e selecione **Publicar**.

1. Na página do resumo da **Publicação,** **selecione Publicar**.

   ![Publicar atualização para aplicação web](./media/app-service-web-get-started-dotnet/publish-update-to-web-app-vs2019.png)

Quando a publicação estiver concluída, o Visual Studio inicia um browser para o URL da aplicação Web.

![Atualizado ASP.NET aplicação web em execução em Azure](./media/app-service-web-get-started-dotnet/updated-web-app-running-live.png)

## <a name="manage-the-azure-app"></a>Gerir a app Azure

Para gerir a sua aplicação web, vá ao [portal Azure](https://portal.azure.com)e procure e selecione **Serviços de Aplicações.**

![Selecione Serviços de Aplicações](./media/app-service-web-get-started-dotnet/app-services.png)

Na página **serviços de aplicações,** selecione o nome da sua aplicação web.

![Navegação do portal para a aplicação do Azure](./media/app-service-web-get-started-dotnet/select-app-service.png)

A página **'Vista Geral'** para a sua aplicação web contém opções para gestão básica como navegar, parar, iniciar, reiniciar e excluir. O menu esquerdo fornece mais páginas para configurar a sua aplicação.

![Serviço de Aplicações no portal Azure](./media/app-service-web-get-started-dotnet/web-app-overview-page.png)

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, você usou o Visual Studio para criar e implementar uma aplicação web core ASP.NET para o Azure App Service.

Avance para o próximo artigo para aprender a criar uma aplicação .NET Core e conectá-la a uma Base de Dados SQL:

> [!div class="nextstepaction"]
> [ASP.NET Core com a Base de Dados SQL](app-service-web-tutorial-dotnetcore-sqldb.md)

> [!div class="nextstepaction"]
> [Configurar ASP.NET aplicação Core](configure-language-dotnetcore.md)

Quer otimizar e economizar nos gastos na nuvem?

> [!div class="nextstepaction"]
> [Comece a analisar custos com a Gestão de Custos](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

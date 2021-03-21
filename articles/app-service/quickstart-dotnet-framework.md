---
title: 'Quickstart: Criar uma aplicação de ASP.NET C#'
description: Aprenda a executar aplicações web no Azure App Service implementando o modelo de aplicação web C# padrão ASP.NET do Visual Studio.
ms.assetid: 04a1becf-7756-4d4e-92d8-d9471c263d23
ms.topic: quickstart
ms.date: 11/20/2020
ms.custom: devx-track-csharp, mvc, devcenter, seodec18
ms.openlocfilehash: a4f7ba288bc27d6079deea9caf0ea315a55d0745
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96004102"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>Criar uma aplicação Web ASP.NET Framework no Azure

O [Serviço de Aplicações do Azure](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática.

Este quickstart mostra como implementar a sua primeira aplicação web ASP.NET para o Azure App Service. Quando terminar, terá um plano de Serviço de Aplicações. Você também terá uma aplicação de Serviço de Aplicações com uma aplicação web implementada.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, instale <a href="https://www.visualstudio.com/downloads/" target="_blank">o Visual Studio 2019</a> com a ASP.NET e a carga de trabalho **de desenvolvimento web.**

Se já instalou o Visual Studio 2019:

- Instale as últimas atualizações no Estúdio Visual selecionando **o Help** Check  >  **for Updates**.
- Adicione a carga de trabalho selecionando **Ferramentas**  >  **Obter Ferramentas e Funcionalidades**.

## <a name="create-an-aspnet-web-app"></a>Criar uma aplicação web ASP.NET <a name="create-and-publish-the-web-app"></a>

Crie uma aplicação web ASP.NET seguindo estes passos:

1. Abra o Estúdio Visual e, em seguida, **selecione Criar um novo projeto.**

2. Na **Criação de um novo projeto,** encontre e escolha **ASP.NET Aplicação Web (.NET Framework)** e, em seguida, selecione **Next**.

3. Em **Configurar o seu novo projeto**, nomeie a aplicação _myFirstAzureWebApp_ e, em seguida, selecione **Create**.

   ![Configure o seu projeto de aplicação web](./media/quickstart-dotnet-framework/configure-web-app-project-framework.png)

4. Pode implementar qualquer tipo de aplicação Web ASP.NET no Azure. Para este arranque rápido, escolha o modelo **MVC.**

5. Certifique-se de que a autenticação está definida para **Não Autenticação**. Selecione **Criar**.

   ![Criar aplicação web ASP.NET](./media/quickstart-dotnet-framework/select-mvc-template-vs2019.png)

6. A partir do menu Visual Studio, selecione **Debug**  >  **Start Without Debugging** para executar a aplicação web localmente.

   ![Executar a aplicação localmente](./media/quickstart-dotnet-framework/local-web-app.png)

## <a name="publish-your-web-app"></a>Publique a sua aplicação web <a name="launch-the-publish-wizard"></a>

1. No **Solution Explorer,** clique com o botão direito no **projeto myFirstAzureWebApp** e selecione **Publicar**.

1. Em **Publicar**, selecione **Azure** e clique em **Seguinte**.

1. Selecione **O Serviço de Aplicações Azure (Windows)** e clique em **Seguinte**.

   <!-- ![Publish from project overview page](./media/quickstart-dotnet-framework/publish-app-framework-vs2019.png) -->

1. As suas opções dependem se já está inscrito no Azure e se tem uma conta visual studio ligada a uma conta Azure. Selecione **Adicione uma conta** ou **inscreva-se** para iniciar súmis na sua subscrição Azure. Se já fez a sua assinatura, selecione a conta que pretende.

   ![Iniciar sessão no Azure](./media/quickstart-dotnet-framework/sign-in-azure-framework-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. À direita das instâncias do Serviço de **Aplicações,** clique **+** em .

   ![Nova app de Serviço de Aplicações](./media/quickstart-dotnet-framework/publish-new-app-service.png)

1. Para **o grupo de recursos**, selecione **New**.

1. No **nome do grupo de novos recursos,** insira o *myResourceGroup* e selecione **OK**.

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. Para **o Plano de Hospedagem**, selecione **New**.

1. No diálogo **do Plano de Hospedagem,** insira os valores a partir da tabela seguinte e, em seguida, selecione **OK**.

   | Definição | Valor sugerido | Description |
   |-|-|-|
   | Plano de Alojamento| myAppServicePlan | Nome do plano de serviço de aplicações. |
   | Localização | Europa Ocidental | O centro de dados onde o a aplicação Web está alojada. |
   | Tamanho | Gratuito | O [escalão de preço](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) determina as funcionalidades do alojamento. |

   ![Criar plano do App Service](./media/quickstart-dotnet-framework/app-service-plan-framework-vs2019.png)

1. In **Name**, insira um nome de aplicação único que inclui apenas os caracteres válidos são `a-z` , e `A-Z` `0-9` `-` . Pode aceitar o nome único gerado automaticamente. O URL da aplicação Web é `http://<app-name>.azurewebsites.net`, em que `<app-name>` é o nome da aplicação.

2. Selecione **Criar** para criar os recursos Azure.

   ![Configurar o nome da aplicação](./media/quickstart-dotnet-framework/web-app-name-framework-vs2019.png)

    Assim que o assistente estiver completo, os recursos do Azure são criados para si e está pronto para publicar.

3. **Selecione Acabamento** para fechar o assistente.

3. Na página **publicar,** clique em **Publicar.** Visual Studio constrói, embala e publica a app para o Azure, e depois lança a aplicação no navegador padrão.

    ![Aplicação Web ASP.NET publicada no Azure](./media/quickstart-dotnet-framework/published-azure-web-app.png)

O nome da aplicação especificado no Serviço de **Aplicações Criar nova** página é usado como prefixo URL no formato `http://<app-name>.azurewebsites.net` .

**Parabéns!** A sua aplicação web ASP.NET está a funcionar ao vivo no Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Atualizar a aplicação e reimplementar

1. No **Solution Explorer,** no âmbito do seu projeto, abra **o Views**  >  **Home**  >  **Index.cshtml**.

1. Localize a etiqueta HTML `<div class="jumbotron">` na parte superior e substitua todo o elemento pelo código seguinte:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Para voltar a implementar no Azure, clique com o botão direito do rato no projeto **myFirstAzureWebApp**, no **Explorador de Soluções** e selecione **Publicar**. Em seguida, **selecione Publicar.**

    Quando a publicação estiver concluída, o Visual Studio inicia um browser para o URL da aplicação Web.

    ![Aplicação Web ASP.NET atualizada no Azure](./media/quickstart-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-app"></a>Gerir a app Azure

1. Para gerir a aplicação web, vá ao [portal Azure](https://portal.azure.com)e procure e selecione **Serviços de Aplicações.**

   ![Selecione serviços de aplicações](./media/quickstart-dotnet-framework/app-services.png)

2. Na página **serviços de aplicações,** selecione o nome da sua aplicação web.

   ![Navegação do portal para a aplicação do Azure](./media/quickstart-dotnet-framework/access-portal-framework-vs2019.png)

   É apresentada a página de descrição geral da sua aplicação Web. Aqui, pode fazer gestão básica como navegar, parar, iniciar, reiniciar e apagar.

   ![Visão geral do Serviço de Aplicações no portal Azure](./media/quickstart-dotnet-framework/web-app-general-framework-vs2019.png)

   O menu à esquerda fornece diferentes páginas para configurar a sua aplicação.

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [ASP.NET com Base de Dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md)

> [!div class="nextstepaction"]
> [Configurar ASP.NET app](configure-language-dotnet-framework.md)

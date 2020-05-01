---
title: 'Quickstart: Criar uma app c# ASP.NET'
description: Saiba como executar aplicações web no Azure App Service implementando o modelo de aplicação web Padrão C# ASP.NET do Visual Studio.
ms.assetid: 04a1becf-7756-4d4e-92d8-d9471c263d23
ms.topic: quickstart
ms.date: 04/21/2020
ms.custom: mvc, devcenter, seodec18
ms.openlocfilehash: 056c96807fed7d5acca85635440c8fe4d989c933
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82592401"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>Criar uma aplicação Web ASP.NET Framework no Azure

O [Serviço de Aplicações do Azure](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática.

Este quickstart mostra como implementar a sua primeira aplicação web ASP.NET para o Azure App Service. Quando terminar, terá um plano de Serviço de Aplicações. Você também terá uma app de Serviço de Aplicações com uma aplicação web implementada.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, instale o <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> com a ASP.NET e a carga de trabalho de **desenvolvimento web.**

Se já instalou o Visual Studio 2019:

- Instale as últimas atualizações no Estúdio Visual selecionando **A Verificação de Ajuda** > **para Atualizações**.
- Adicione a carga de trabalho selecionando **ferramentas** > **obter ferramentas e funcionalidades**.

## <a name="create-an-aspnet-web-app"></a>Criar uma aplicação web ASP.NET<a name="create-and-publish-the-web-app"></a>

Crie uma aplicação web ASP.NET seguindo estes passos:

1. Abra o Estúdio Visual e, em seguida, selecione **Criar um novo projeto**.

2. Em **Criar um novo projeto,** encontre e escolha ASP.NET **Aplicação Web (.QUADRO NET)** e, em seguida, selecione **Next**.

3. No **Configure o seu novo projeto,** nomeie a aplicação _myFirstAzureWebApp_, e depois selecione **Create**.

   ![Configure o seu projeto de aplicação web](./media/app-service-web-get-started-dotnet-framework/configure-web-app-project-framework.png)

4. Pode implementar qualquer tipo de aplicação Web ASP.NET no Azure. Para este arranque rápido, escolha o modelo **MVC.**

5. Certifique-se de que a autenticação está definida para **não autenticação.** Selecione **Criar**.

   ![Criar ASP.NET aplicação web](./media/app-service-web-get-started-dotnet-framework/select-mvc-template-vs2019.png)

6. A partir do menu do Estúdio Visual, selecione **Debug** > **Start Without Debugging** para executar a aplicação web localmente.

   ![Executar a aplicação localmente](./media/app-service-web-get-started-dotnet-framework/local-web-app.png)

## <a name="publish-your-web-app"></a>Publique a sua aplicação web<a name="launch-the-publish-wizard"></a>

1. No **Solution Explorer,** clique à direita no projeto **myFirstAzureWebApp** e selecione **Publish**.

1. Escolha **o Serviço de Aplicações** e selecione Criar o **perfil**.

   ![Publicar a partir da página de descrição geral do projeto](./media/app-service-web-get-started-dotnet-framework/publish-app-framework-vs2019.png)

1. No **Serviço de Aplicações Criar novas**, as suas opções dependem se já está inscrito no Azure e se tem uma conta visual studio ligada a uma conta Azure. Selecione **Adicionar uma conta** ou **iniciar** sessão para iniciar sessão na subscrição do Azure. Se já assinou, selecione a conta que deseja.

   > [!NOTE]
   > Se já tiver sessão iniciada, não selecione ainda **Criar**.
   >
   >

   ![Iniciar sessão no Azure](./media/app-service-web-get-started-dotnet-framework/sign-in-azure-framework-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. Para **o grupo Recursos,** selecione **New**.

1. Em novo nome de **grupo de recursos,** insira *o myResourceGroup* e selecione **OK**.

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. Para **o Plano de Hospedagem,** selecione **New**.

1. No diálogo do Plano de **Hospedagem Configurar,** introduza os valores a partir da tabela seguinte e, em seguida, selecione **OK**.

   | Definição | Valor sugerido | Descrição |
   |-|-|-|
   | Plano de Alojamento| myAppServicePlan | Nome do plano de serviço de aplicações. |
   | Localização | Europa ocidental | O centro de dados onde o a aplicação Web está alojada. |
   | Tamanho | Gratuito | O [escalão de preço](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) determina as funcionalidades do alojamento. |

   ![Criar plano do App Service](./media/app-service-web-get-started-dotnet-framework/app-service-plan-framework-vs2019.png)

1. Em **Nome,** introduza um nome de aplicação `A-Z` `0-9`único `-`que inclua apenas os caracteres válidos, `a-z`e . Pode aceitar o nome único gerado automaticamente. O URL da aplicação Web é `http://<app-name>.azurewebsites.net`, em que `<app-name>` é o nome da aplicação.

2. Selecione **Criar** para começar a criar os recursos do Azure.

   ![Configurar o nome da aplicação](./media/app-service-web-get-started-dotnet-framework/web-app-name-framework-vs2019.png)

    Assim que o assistente estiver concluído, os recursos Azure são criados para si e está pronto para publicar.

3. Na página **Publicar,** clique em **Publicar**. O Visual Studio constrói, embala e publica a app para o Azure, e depois lança a app no navegador padrão.

    ![Aplicação Web ASP.NET publicada no Azure](./media/app-service-web-get-started-dotnet-framework/published-azure-web-app.png)

O nome da aplicação especificado no **Serviço de Aplicações Criar nova** página é usado como prefixo URL no formato `http://<app-name>.azurewebsites.net`.

**Parabéns!** A sua ASP.NET aplicação web está a funcionar ao vivo no Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Atualizar a aplicação e reimplementar

1. No **Solution Explorer,** no âmbito do seu projeto, abra **views** > **home** > **index.cshtml**.

1. Localize a etiqueta HTML `<div class="jumbotron">` na parte superior e substitua todo o elemento pelo código seguinte:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Para voltar a implementar no Azure, clique com o botão direito do rato no projeto **myFirstAzureWebApp**, no **Explorador de Soluções** e selecione **Publicar**. Em seguida, selecione **Publicar**.

    Quando a publicação estiver concluída, o Visual Studio inicia um browser para o URL da aplicação Web.

    ![Aplicação Web ASP.NET atualizada no Azure](./media/app-service-web-get-started-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-app"></a>Gerir a app Azure

1. Para gerir a aplicação web, vá ao [portal Azure](https://portal.azure.com)e procure e selecione Serviços de **Aplicações.**

   ![Selecione serviços de Aplicações](./media/app-service-web-get-started-dotnet-framework/app-services.png)

2. Na página de Serviços de **Aplicações,** selecione o nome da sua aplicação web.

   ![Navegação do portal para a aplicação do Azure](./media/app-service-web-get-started-dotnet-framework/access-portal-framework-vs2019.png)

   É apresentada a página de descrição geral da sua aplicação Web. Aqui, pode fazer gestão básica como navegar, parar, começar, reiniciar e apagar.

   ![Visão geral do Serviço de Aplicações no portal Azure](./media/app-service-web-get-started-dotnet-framework/web-app-general-framework-vs2019.png)

   O menu à esquerda fornece diferentes páginas para configurar a sua aplicação.

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [ASP.NET com Base de Dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md)

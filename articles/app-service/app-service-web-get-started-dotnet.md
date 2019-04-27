---
title: Criar C# aplicação web do ASP.NET Core - serviço de aplicações do Azure | Documentos da Microsoft
description: Saiba como executar aplicações web no serviço de aplicações do Azure ao implementar o padrão C# aplicação web ASP.NET Core.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/05/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 2762a81c9f366ad4a5d0d6d87cf04490fc10b23c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60854635"
---
# <a name="create-an-aspnet-core-web-app-in-azure"></a>Criar uma aplicação Web ASP.NET Core no Azure

> [!NOTE]
> Este artigo implementa uma aplicação no Serviço de Aplicações no Windows. Para implementar um Serviço de Aplicações no _Linux_, consulte [Criar uma aplicação Web .NET Core no Serviço de Aplicações no Linux](./containers/quickstart-dotnetcore.md). 
>

O [Serviço de Aplicações do Azure](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática.  Este início rápido mostra como implementar a sua primeira ASP.NET Core, aplicação web no App Service do Azure. Quando tiver terminado, terá um grupo de recursos que consiste num plano do serviço de aplicações e uma aplicação de serviço de aplicações com uma aplicação web implementada.

![](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, instale o <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> com a carga de trabalho de **desenvolvimento na Web e em ASP.NET**.

Se já instalou o Visual Studio 2017:

- Instale as atualizações mais recentes no Visual Studio ao clicar em **Ajuda** > **Procurar Atualizações**.
- Adicione a carga de trabalho ao clicar em **Ferramentas** > **Obter Ferramentas e Funcionalidades**.

## <a name="create-an-aspnet-core-web-app"></a>Criar uma aplicação Web ASP.NET Core

No Visual Studio, crie um projeto ao selecionar **Ficheiro > Novo > Projeto**. 

Na caixa de diálogo **Novo projeto**, clique em **Visual C# > Web > Aplicação Web ASP.NET Core**.

Atribua à aplicação o nome _myFirstAzureWebApp_ e clique em **OK**.
   
![Caixa de diálogo Novo Projeto](./media/app-service-web-get-started-dotnet/new-project.png)

Pode implementar qualquer tipo de aplicação Web ASP.NET Core no Azure. Neste guia de início rápido, selecione o modelo **Aplicação Web** e confirme se a autenticação está definida como **Sem Autenticação** e sem outras opções selecionadas.
      
Selecione **OK**.

![Caixa de diálogo Novo Projeto ASP.NET](./media/app-service-web-get-started-dotnet/razor-pages-aspnet-dialog.png)

No menu, selecione **Depurar > Iniciar sem depuração** para executar a aplicação Web localmente.

![Executar a aplicação localmente](./media/app-service-web-get-started-dotnet/razor-web-app-running-locally.png)

## <a name="launch-the-publish-wizard"></a>Iniciar o assistente de publicação

No **Explorador de Soluções**, clique com o botão direito do rato no projeto **myFirstAzureWebApp** e selecione **Publicar**.

![Publicar a partir do Explorador de Soluções](./media/app-service-web-get-started-dotnet/right-click-publish.png)

O assistente de publicação é iniciado automaticamente. Selecione **Serviço de Aplicações** > **Publicar** para abrir a caixa de diálogo **Criar Serviço de Aplicações**.

![Publicar a partir da página de descrição geral do projeto](./media/app-service-web-get-started-dotnet/publish-to-app-service.png)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Na caixa de diálogo **Criar Serviço de Aplicações**, clique em **Adicionar uma conta** e inicie sessão na sua subscrição do Azure. Se ainda tiver a sessão iniciada, seleciona a conta que quer a partir da lista pendente.

> [!NOTE]
> Se já tiver sessão iniciada, não selecione ainda **Criar**.
>
   
![Iniciar sessão no Azure](./media/app-service-web-get-started-dotnet/sign-in-azure.png)

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [resource group intro text](../../includes/resource-group.md)]

Junto a **Grupo de recursos**, selecione **Novo**.

Atribua ao grupo de recursos o nome **myResourceGroup** e selecione **OK**.

## <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

Junto a **Plano de Alojamento**, selecione **Novo**. 

Na caixa de diálogo **Configurar Plano do Alojamento**, use as definições na tabela a seguir à captura de ecrã.

![Criar plano do App Service](./media/app-service-web-get-started-dotnet/configure-app-service-plan.png)

| Definição | Valor sugerido | Descrição |
|-|-|-|
|Plano do Serviço de Aplicações| myAppServicePlan | Nome do plano de serviço de aplicações. |
| Location | Europa Ocidental | O centro de dados onde o a aplicação Web está alojada. |
| Tamanho | Gratuito | O [escalão de preço](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) determina as funcionalidades do alojamento. |

Selecione **OK**.

## <a name="create-and-publish-the-web-app"></a>Criar e publicar a aplicação Web

No **Nome da Aplicação**, escreva um nome único para a aplicação (os carateres válidos são `a-z`, `0-9` e `-`) ou aceite o nome exclusivo gerado automaticamente. O URL da aplicação Web é `http://<app_name>.azurewebsites.net`, em que `<app_name>` é o nome da aplicação.

Selecione **Criar** para começar a criar os recursos do Azure.

![Configurar o nome da aplicação](./media/app-service-web-get-started-dotnet/web-app-name.png)

Depois de concluir o assistente, este publica a aplicação Web ASP.NET Core no Azure e, em seguida, inicia a aplicação no browser predefinido.

![Aplicação Web ASP.NET publicada no Azure](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

O nome da aplicação especificado no [passo para criar e publicar](#create-and-publish-the-web-app) é utilizado como o prefixo de URL no formato `http://<app_name>.azurewebsites.net`.

Parabéns, a primeira aplicação Web ASP.NET Core está em execução em direto no Serviço de Aplicações do Azure.

## <a name="update-the-app-and-redeploy"></a>Atualizar a aplicação e reimplementar

No **Explorador de Soluções**, abra _Pages/Index.cshtml_.

Substitua as duas tags `<div>` pelo seguinte código:

```HTML
<div class="jumbotron">
    <h1>ASP.NET in Azure!</h1>
    <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
</div>
```

Para voltar a implementar no Azure, clique com o botão direito do rato no projeto **myFirstAzureWebApp**, no **Explorador de Soluções** e selecione **Publicar**.

Na página de resumo de publicação, selecione **Publicar**.
![Página de resumo de publicação do Visual Studio](./media/app-service-web-get-started-dotnet/publish-summary-page.png)

Quando a publicação estiver concluída, o Visual Studio inicia um browser para o URL da aplicação Web.

![Aplicação Web ASP.NET atualizada no Azure](./media/app-service-web-get-started-dotnet/web-app-running-live-updated.png)

## <a name="manage-the-azure-app"></a>Gerir a aplicação do Azure

Aceda ao <a href="https://portal.azure.com" target="_blank">portal do Azure</a> para gerir a aplicação Web.

No menu à esquerda, selecione **dos serviços de aplicações**e, em seguida, selecione o nome da sua aplicação do Azure.

![Navegação do portal para a aplicação do Azure](./media/app-service-web-get-started-dotnet/access-portal.png)

É apresentada a página de descrição geral da sua aplicação Web. Aqui, pode realizar tarefas de gestão básicas, como navegar, parar, iniciar, reiniciar e eliminar. 

![Painel Serviço de Aplicações no portal do Azure](./media/app-service-web-get-started-dotnet/web-app-blade.png)

O menu à esquerda fornece diferentes páginas para configurar a sua aplicação. 

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [ASP.NET Core com a Base de Dados SQL](app-service-web-tutorial-dotnetcore-sqldb.md)

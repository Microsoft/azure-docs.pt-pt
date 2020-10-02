---
title: 'Quickstart: Criar uma aplicação C# ASP.NET Core'
description: Aprenda a executar aplicações web no Azure App Service implementando a sua primeira aplicação core ASP.NET.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 09/24/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperfq1
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: aa8eb945ba77e1a4ac5215acf3bdbc12cac0c4c9
ms.sourcegitcommit: 487a9f5272300d60df2622c3d13e794d54680f90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91661130"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Quickstart: Criar uma aplicação web core ASP.NET em Azure

::: zone pivot="platform-windows"  

Neste arranque rápido, você vai aprender a criar e implementar a sua primeira aplicação web core ASP.NET para [o Azure App Service](overview.md). 

Quando terminar, terá um grupo de recursos Azure composto por um plano de hospedagem do App Service e um Serviço de Aplicações com uma aplicação web implementada.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/dotnet/)
- Este quickstart implementa uma aplicação para o Serviço de Aplicações no Windows. Para implementar no Serviço de Aplicações em _Linux,_ consulte [criar uma aplicação web .NET Core no Serviço de Aplicações.](./quickstart-dotnetcore.md)
- Instale <a href="https://www.visualstudio.com/downloads/" target="_blank">o Visual Studio 2019</a> com a ASP.NET e a carga de trabalho **de desenvolvimento web.**

  Se já instalou o Visual Studio 2019:

  - Instale as últimas atualizações no Estúdio Visual selecionando **o Help**Check  >  **for Updates**.
  - Adicione a carga de trabalho selecionando **Ferramentas**  >  **Obter Ferramentas e Funcionalidades**.


## <a name="create-an-aspnet-core-web-app"></a>Criar uma aplicação Web ASP.NET Core

Crie uma aplicação web core ASP.NET no Estúdio Visual seguindo estes passos:

1. Abra o Estúdio Visual e selecione **Criar um novo projeto.**

1. In **Create a new project**, selecione ASP.NET Core Web **Application** e confirme que **C#** está listado nos idiomas para essa escolha e, em seguida, selecione **Next**.

1. Em **Configurar o seu novo projeto,** nomeie o seu projeto de aplicação web *myFirstAzureWebApp*e selecione **Create**.

   ![Configure o seu projeto de aplicação web](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. Você pode implementar qualquer tipo de ASP.NET web app Core para Azure, mas para este arranque rápido, escolha o modelo **de Aplicação Web.** Certifique-se de que a **autenticação** está definida para **Não Autenticação**e que não está selecionada nenhuma outra opção. Em seguida, selecione **Criar**.

   ![Criar uma nova aplicação web core ASP.NET](./media/quickstart-dotnetcore/create-aspnet-core-web-app.png) 
   
1. A partir do menu Visual Studio, selecione **Debug**  >  **Start Without Debugging** para executar a sua aplicação web localmente.

   ![Aplicativo web correndo localmente](./media/quickstart-dotnetcore/web-app-running-locally.png)

## <a name="publish-your-web-app"></a>Publique a sua aplicação web

Para publicar a sua aplicação web, tem primeiro de criar e configurar um novo Serviço de Aplicações para o quais pode publicar a sua aplicação. 

Como parte da criação do Serviço de Aplicações, irá criar:

- Um novo grupo de [recursos](../azure-resource-manager/management/overview.md#terminology) para conter todos os recursos da Azure para o serviço.
- Um novo [Plano de Hospedagem](./overview-hosting-plans.md) que especifica a localização, tamanho e funcionalidades da fazenda de servidores web que hospeda a sua aplicação.

Siga estes passos para criar o seu Serviço de Aplicações e publique a sua aplicação web:

1. No **Solution Explorer,** clique com o botão direito no **projeto myFirstAzureWebApp** e selecione **Publicar**. Se ainda não se inscreveu na sua conta Azure do Visual Studio, selecione **Adicione uma conta** ou **inscreva-se em**. Também pode criar uma conta Azure gratuita.

1. Na **caixa de** diálogo alvo de publicação, escolha **o Serviço de Aplicações,** selecione **Create New**e, em seguida, selecione **Criar Perfil**.

   ![Escolher um destino de publicação](./media/quickstart-dotnetcore/pick-publish-target-vs2019.png)

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
   
   ![Criar novo Plano de Hospedagem](./media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png)

1. Deixe **os Insights de Aplicação** *definidos*para Nenhum .

1. No Serviço de **Aplicações: Crie uma nova** caixa de diálogo, selecione **Criar** para começar a criar os recursos Azure.

   ![Criar novo serviço de aplicações](./media/quickstart-dotnetcore/create-new-app-service-vs2019.png)

1. Assim que o assistente estiver completo, **selecione Publicar**.

   ![Publique aplicativo web para a Azure](./media/quickstart-dotnetcore/publish-web-app-vs2019.png)

   O Visual Studio publica a sua aplicação web Core ASP.NET para o Azure e lança a aplicação no seu navegador padrão. 

   ![Publicado ASP.NET web app em execução em Azure](./media/quickstart-dotnetcore/web-app-running-live.png)

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

   ![Publicar atualização para aplicação web](./media/quickstart-dotnetcore/publish-update-to-web-app-vs2019.png)

Quando a publicação estiver concluída, o Visual Studio inicia um browser para o URL da aplicação Web.

![Atualizado ASP.NET aplicação web em execução em Azure](./media/quickstart-dotnetcore/updated-web-app-running-live.png)

## <a name="manage-the-azure-app"></a>Gerir a app Azure

Para gerir a sua aplicação web, vá ao [portal Azure](https://portal.azure.com)e procure e selecione **Serviços de Aplicações.**

![Selecione Serviços de Aplicações](./media/quickstart-dotnetcore/app-services.png)

Na página **serviços de aplicações,** selecione o nome da sua aplicação web.

:::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Screenshot da página serviços de aplicações com uma aplicação web de exemplo selecionada.":::

A página **'Vista Geral'** para a sua aplicação web contém opções para gestão básica como navegar, parar, iniciar, reiniciar e excluir. O menu esquerdo fornece mais páginas para configurar a sua aplicação.

![Serviço de Aplicações no portal Azure](./media/quickstart-dotnetcore/web-app-overview-page.png)

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Próximas etapas

Neste quickstart, você usou o Visual Studio para criar e implementar uma aplicação web core ASP.NET para o Azure App Service.

Avance para o próximo artigo para aprender a criar uma aplicação .NET Core e conectá-la a uma Base de Dados SQL:

> [!div class="nextstepaction"]
> [ASP.NET Core com a Base de Dados SQL](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Configurar ASP.NET aplicação Core](configure-language-dotnetcore.md)

::: zone-end  

::: zone pivot="platform-linux"
[O Serviço de Aplicações no Linux](overview.md#app-service-on-linux) fornece um serviço de hospedagem web altamente escalável e auto-remendado utilizando o sistema operativo Linux. Este guia de introdução mostra como criar uma aplicação [.NET Core](/aspnet/core/) no Serviço de Aplicações no Linux. Cria a aplicação utilizando o [Azure CLI](/cli/azure/get-started-with-azure-cli)e utiliza o Git para implementar o código .NET Core para a aplicação.

![Aplicação Web de exemplo em execução no Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

Pode seguir os passos deste artigo num computador Mac, Windows ou Linux.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

* <a href="https://git-scm.com/" target="_blank">Instalar o Git</a>
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">Instale o mais recente .NET Core 3.1 SDK</a>

[Tendo problemas? Deixe-nos saber.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="create-the-app-locally"></a>Criou a aplicação localmente

Numa janela de terminal no seu computador, crie um diretório com o nome `hellodotnetcore` e altere o diretório atual para o mesmo.

```bash
mkdir hellodotnetcore
cd hellodotnetcore
```

Criar uma nova aplicação .NET Core.

```bash
dotnet new web
```

## <a name="run-the-app-locally"></a>Executar a aplicação localmente

Execute a aplicação localmente, para ver que aspeto deveria ter quando a implemente no Azure. 

Restaure os pacotes NuGet e execute a aplicação.

```bash
dotnet run
```

Abra um browser e navegue para a aplicação em `http://localhost:5000`.

Pode ver a mensagem **Hello World** da aplicação de exemplo apresentada na página.

![Testar com o browser](media/quickstart-dotnetcore/dotnet-browse-local.png)

Na janela do terminal, prima **Ctrl+C** para sair do servidor Web. Inicialize um repositório de Git para o projeto .NET Core.

```bash
git init
git add .
git commit -m "first commit"
```

[Tendo problemas? Deixe-nos saber.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[Tendo problemas? Deixe-nos saber.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

[Tendo problemas? Deixe-nos saber.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]

[Tendo problemas? Deixe-nos saber.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux.md)]

[Tendo problemas? Deixe-nos saber.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="create-a-web-app"></a>Criar uma aplicação Web

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)]

Navegue pela sua aplicação recém-criada. Substitua _ &lt; o nome da aplicação>_ pelo nome da sua aplicação.

```bash
https://<app-name>.azurewebsites.net
```

Aqui está como deve ser a sua nova aplicação:

![Página de aplicativo vazio](media/quickstart-dotnetcore/dotnet-browse-created.png)

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure.md)] 

<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;app-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;app-name&gt;.scm.azurewebsites.net:443/&lt;app-name&gt;.git
   d87e6ca..d6b5447  master -> master
</pre>

[Tendo problemas? Deixe-nos saber.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="browse-to-the-app"></a>Navegar para a aplicação

Utilize o browser para navegar para a aplicação implementada.

```bash
http://<app_name>.azurewebsites.net
```

O código de amostra .NET Core está a ser em execução no Serviço de Aplicações em Linux com uma imagem incorporada.

![Aplicação Web de exemplo em execução no Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

**Parabéns!** Implementou a sua primeira aplicação .NET Core no Serviço de Aplicações no Linux.

[Tendo problemas? Deixe-nos saber.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="update-and-redeploy-the-code"></a>Atualizar e voltar a implementar o código

No diretório local, abra o ficheiro _Startup.cs_. Faça uma pequena alteração no texto da chamada de método `context.Response.WriteAsync`:

```csharp
await context.Response.WriteAsync("Hello Azure!");
```

Consolide as suas alterações no Git e envie as alterações ao código para o Azure.

```bash
git commit -am "updated output"
git push azure master
```

Uma vez concluída a implementação, volte para a janela do navegador que abriu na **Navegação para o** passo da aplicação e aprovam a atualização.

![Aplicação de exemplo atualizada em execução no Azure](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

[Tendo problemas? Deixe-nos saber.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="manage-your-new-azure-app"></a>Gerencie a sua nova app Azure

Vá ao <a href="https://portal.azure.com" target="_blank">portal Azure</a> para gerir a app que criou.

A partir do menu esquerdo, clique em **Serviços de Aplicação**e, em seguida, clique no nome da sua aplicação Azure.

:::image type="content" source="./media/quickstart-dotnetcore/portal-app-service-list.png" alt-text="Screenshot da página serviços de aplicações com uma aplicação web de exemplo selecionada.":::

Veja a página geral da sua aplicação. Aqui, pode realizar tarefas de gestão básicas, como navegar, parar, iniciar, reiniciar e eliminar. 

![Página Serviço de Aplicações no portal do Azure](media/quickstart-dotnetcore/portal-app-overview.png)

O menu à esquerda fornece diferentes páginas para configurar a sua aplicação. 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

[Tendo problemas? Deixe-nos saber.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: ASP.NET Core app com base de dados SQL](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Configurar ASP.NET aplicação Core](configure-language-dotnetcore.md)

::: zone-end
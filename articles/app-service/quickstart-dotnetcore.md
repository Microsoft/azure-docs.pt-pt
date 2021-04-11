---
title: 'Quickstart: Criar uma aplicação C# ASP.NET Core'
description: Aprenda a executar aplicações web no Azure App Service implementando a sua primeira aplicação core ASP.NET.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 11/23/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-dotnetcore-uiex
ms.openlocfilehash: 77e0768a617ef79ab8510f88bfdcd41d5647f9bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101701642"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Quickstart: Criar uma aplicação web core ASP.NET em Azure

::: zone pivot="platform-windows"  

Neste arranque rápido, você vai aprender a criar e implementar a sua primeira aplicação web core ASP.NET para [o Azure App Service](overview.md). O Serviço de Aplicações suporta aplicações .NET 5.0.

Quando terminar, terá um grupo de recursos Azure composto por um plano de hospedagem do App Service e um Serviço de Aplicações com uma aplicação web implementada.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/dotnet/)
- Instale <a href="https://www.visualstudio.com/downloads/" target="_blank">o Visual Studio 2019</a> com a ASP.NET e a carga de trabalho **de desenvolvimento web.**

  Se já instalou o Visual Studio 2019:

  - Instale as últimas atualizações no Estúdio Visual selecionando **o Help** Check  >  **for Updates**. As últimas atualizações contêm o .NET 5.0 SDK.
  - Adicione a carga de trabalho selecionando **Ferramentas**  >  **Obter Ferramentas e Funcionalidades**.


## <a name="create-an-aspnet-core-web-app"></a>Criar uma aplicação Web ASP.NET Core

Crie uma aplicação web core ASP.NET no Estúdio Visual seguindo estes passos:

# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

1. Abra o Estúdio Visual e selecione **Criar um novo projeto.**

1. In **Create a new project**, selecione ASP.NET Core Web **Application** e confirme que **C#** está listado nos idiomas para essa escolha e, em seguida, selecione **Next**.

1. Em **Configurar o seu novo projeto,** nomeie o seu projeto de aplicação web *myFirstAzureWebApp* e selecione **Create**.

   ![Configure o seu projeto de aplicação web](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. Você pode implementar qualquer tipo de ASP.NET web app Core para Azure, mas para este arranque rápido, escolha o modelo **de Aplicação Web.** Certifique-se de que a **autenticação** está definida para **Não Autenticação** e que não está selecionada nenhuma outra opção. Em seguida, selecione **Criar**.

   ![Criar uma nova aplicação web core ASP.NET](./media/quickstart-dotnetcore/create-aspnet-core-web-app.png) 
   
1. A partir do menu Visual Studio, selecione **Debug**  >  **Start Without Debugging** para executar a sua aplicação web localmente.

   ![Aplicativo web correndo localmente](./media/quickstart-dotnetcore/web-app-running-locally.png)

# <a name="net-50"></a>[.NET 5.0](#tab/net50)

1. Abra o Estúdio Visual e selecione **Criar um novo projeto.**

1. In **Create a new project**, selecione ASP.NET Core Web **Application** e confirme que **C#** está listado nos idiomas para essa escolha e, em seguida, selecione **Next**.

1. Em **Configurar o seu novo projeto,** nomeie o seu projeto de aplicação web *myFirstAzureWebApp* e selecione **Create**.

   ![Configure o seu projeto de aplicação web](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. Para uma aplicação .NET 5.0, selecione **ASP.NET Core 5.0** no dropdown.

1. Você pode implementar qualquer tipo de ASP.NET web app Core para Azure, mas para este arranque rápido, escolha o **modelo de ASP.NET Core Web App.** Certifique-se de que a **autenticação** está definida para **Não Autenticação** e que não está selecionada nenhuma outra opção. Em seguida, selecione **Criar**.

   ![Criar uma nova aplicação web core ASP.NET](./media/quickstart-dotnetcore/create-aspnet-core-web-app-5.png) 
   
1. A partir do menu Visual Studio, selecione **Debug**  >  **Start Without Debugging** para executar a sua aplicação web localmente.

   ![Aplicativo web correndo localmente](./media/quickstart-dotnetcore/web-app-running-locally.png)

---

## <a name="publish-your-web-app"></a>Publique a sua aplicação web

Para publicar a sua aplicação web, tem primeiro de criar e configurar um novo Serviço de Aplicações para o quais pode publicar a sua aplicação. 

Como parte da criação do Serviço de Aplicações, irá criar:

- Um novo grupo de [recursos](../azure-resource-manager/management/overview.md#terminology) para conter todos os recursos da Azure para o serviço.
- Um novo [Plano de Hospedagem](./overview-hosting-plans.md) que especifica a localização, tamanho e funcionalidades da fazenda de servidores web que hospeda a sua aplicação.

Siga estes passos para criar o seu Serviço de Aplicações e publique a sua aplicação web:

1. No **Solution Explorer,** clique com o botão direito no **projeto myFirstAzureWebApp** e selecione **Publicar**. 

1. Em **Publicar**, selecione **Azure** e clique em **Seguinte**.

1. As suas opções dependem se já está inscrito no Azure e se tem uma conta visual studio ligada a uma conta Azure. Selecione **Adicione uma conta** ou **inscreva-se** para iniciar súmis na sua subscrição Azure. Se já fez a sua assinatura, selecione a conta que pretende.

   ![Iniciar sessão no Azure](./media/quickstart-dotnetcore/sign-in-azure-vs2019.png)

1. À direita das instâncias do Serviço de **Aplicações,** clique **+** em .

   ![Nova app de Serviço de Aplicações](./media/quickstart-dotnetcore/publish-new-app-service.png)

1. Para **Subscrição**, aceite a subscrição listada ou selecione uma nova da lista de drop-down.

1. Para **o grupo de recursos**, selecione **New**. No **nome do grupo de novos recursos,** insira o *myResourceGroup* e selecione **OK**. 

1. Para **o Plano de Hospedagem**, selecione **New**. 

1. No **Plano de Hospedagem: Criar novo** diálogo, introduzir os valores especificados no quadro seguinte:

   | Definição  | Valor sugerido | Description |
   | -------- | --------------- | ----------- |
   | **Plano de Alojamento**  | *myFirstAzureWebAppPlan* | Nome do plano de serviço de aplicações. |
   | **Localização**      | *Europa Ocidental* | O centro de dados onde o a aplicação Web está alojada. |
   | **Tamanho**          | *Gratuito* | O [escalão de preço](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) determina as funcionalidades do alojamento. |
   
   ![Criar novo Plano de Hospedagem](./media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png)

1. In **Name**, insira um nome de aplicação único que inclui apenas os caracteres válidos são `a-z` , e `A-Z` `0-9` `-` . Pode aceitar o nome único gerado automaticamente. O URL da aplicação Web é `http://<app-name>.azurewebsites.net`, em que `<app-name>` é o nome da aplicação.

2. Selecione **Criar** para criar os recursos Azure.

   ![Criar recursos de aplicativos](./media/quickstart-dotnetcore/web-app-name-vs2019.png)

   Assim que o assistente estiver completo, os recursos do Azure são criados para si e está pronto para publicar.

3. **Selecione Acabamento** para fechar o assistente.

1. Na página **publicar,** clique em **Publicar.** Visual Studio constrói, embala e publica a app para o Azure, e depois lança a aplicação no navegador padrão.

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

   <!-- ![Publish update to web app](./media/quickstart-dotnetcore/publish-update-to-web-app-vs2019.png) -->

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

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, você usou o Visual Studio para criar e implementar uma aplicação web core ASP.NET para o Azure App Service.

Avance para o próximo artigo para aprender a criar uma aplicação .NET Core e conectá-la a uma Base de Dados SQL:

> [!div class="nextstepaction"]
> [ASP.NET Core com a Base de Dados SQL](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Configurar ASP.NET aplicação Core](configure-language-dotnetcore.md)

::: zone-end  

::: zone pivot="platform-linux"
[O Serviço de Aplicações no Linux](overview.md#app-service-on-linux) fornece um serviço de hospedagem web altamente escalável e auto-remendado utilizando o sistema operativo Linux. Este quickstart mostra como criar uma aplicação [.NET Core](/aspnet/core/) e implementar para um Linux hospedado No Serviço de Aplicações usando o [Azure CLI](/cli/azure/get-started-with-azure-cli).

![Aplicação Web de exemplo em execução no Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

Pode seguir os passos deste artigo num computador Mac, Windows ou Linux.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-initial-environment"></a>Configurar o seu ambiente inicial

# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Para concluir este guia de início rápido:

* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">Instale o mais recente .NET Core 3.1 SDK</a>.
* <a href="/cli/azure/install-azure-cli" target="_blank">Instalar a CLI do Azure mais recente</a>.

# <a name="net-50"></a>[.NET 5.0](#tab/net50)

Para concluir este guia de início rápido:

* <a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank">Instale o mais recente .NET 5.0 SDK</a>.
* <a href="/cli/azure/install-azure-cli" target="_blank">Instalar a CLI do Azure mais recente</a>.

---

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

```bash
dotnet run
```

Abra um browser e navegue para a aplicação em `http://localhost:5000`.

Pode ver a mensagem **Hello World** da aplicação de exemplo apresentada na página.

![Testar com o browser](media/quickstart-dotnetcore/dotnet-browse-local.png)

[Tendo problemas? Deixe-nos saber.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="sign-into-azure"></a>Iniciar sessão no Azure
Na sua janela terminal, inicie sessão em Azure com o seguinte comando:

```azurecli
az login
```

## <a name="deploy-the-app"></a>Implementar a aplicação

Desloque o código na sua pasta local *(hellodotnetcore)* utilizando o `az webapp up` comando:

```azurecli
az webapp up --sku F1 --name <app-name>
```

- Se o `az` comando não for reconhecido, certifique-se de que tem o CLI Azure instalado como descrito no [Conjunto do seu ambiente inicial](#set-up-your-initial-environment).
- Substitua `<app-name>` por um nome único em todo o Azure *(caracteres válidos `a-z` `0-9` são, e `-`*. Um bom padrão é usar uma combinação do nome da sua empresa e um identificador de aplicativos.
- O `--sku F1` argumento cria a aplicação web no nível de preços gratuitos. Omita este argumento para usar um nível premium mais rápido, que incorre num custo de hora a hora.
- Pode opcionalmente incluir o argumento `--location <location-name>` onde `<location-name>` está uma região de Azure disponível. Pode recuperar uma lista de regiões admissíveis para a sua conta Azure, executando o [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) comando.

O comando pode demorar alguns minutos a ser concluído. Durante a execução, fornece mensagens sobre a criação do grupo de recursos, o plano de Serviço de Aplicações e a aplicação de hospedagem, configurando o registo e, em seguida, executando a implementação zip. Em seguida, dá a mensagem: "Pode lançar a app em http:// &lt; nome de app &gt; .azurewebsites.net", que é o URL da aplicação no Azure.

# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

![Exemplo de saída do comando az webapp up](./media/quickstart-dotnetcore/az-webapp-up-output-3.1.png)

# <a name="net-50"></a>[.NET 5.0](#tab/net50)

<!-- Deploy the code in your local folder (*hellodotnetcore*) using the `az webapp up` command:

```azurecli
az webapp up --sku B1 --name <app-name> --os-type linux
```

- If the `az` command isn't recognized, be sure you have the Azure CLI installed as described in [Set up your initial environment](#set-up-your-initial-environment).
- Replace `<app-name>` with a name that's unique across all of Azure (*valid characters are `a-z`, `0-9`, and `-`*). A good pattern is to use a combination of your company name and an app identifier.
- The `--sku B1` argument creates the web app in the Basic pricing tier, which incurs an hourly cost. Omit this argument to use a faster premium tier, which costs more.
- You can optionally include the argument `--location <location-name>` where `<location-name>` is an available Azure region. You can retrieve a list of allowable regions for your Azure account by running the [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) command.

The command may take a few minutes to complete. While running, it provides messages about creating the resource group, the App Service plan and hosting app, configuring logging, then performing ZIP deployment. It then gives the message, "You can launch the app at http://&lt;app-name&gt;.azurewebsites.net", which is the app's URL on Azure. -->

![Exemplo de saída do comando az webapp up](./media/quickstart-dotnetcore/az-webapp-up-output-5.0.png)

---

[Tendo problemas? Deixe-nos saber.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

[!include [az webapp up command note](../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Navegar para a aplicação

Utilize o browser para navegar para a aplicação implementada.

```bash
http://<app_name>.azurewebsites.net
```

O código de amostra .NET Core está a ser em execução no Serviço de Aplicações em Linux com uma imagem incorporada.

![Aplicação Web de exemplo em execução no Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

**Parabéns!** Implementou a sua primeira aplicação .NET Core no Serviço de Aplicações no Linux.

[Tendo problemas? Deixe-nos saber.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="update-and-redeploy-the-code&quot;></a>Atualizar e voltar a implementar o código

No diretório local, abra o ficheiro _Startup.cs_. Faça uma pequena alteração no texto da chamada de método `context.Response.WriteAsync`:

```csharp
await context.Response.WriteAsync(&quot;Hello Azure!");
```

Guarde as alterações e, em seguida, volte a recolocar a aplicação utilizando o `az webapp up` comando:

```azurecli
az webapp up --os-type linux
```

Este comando utiliza valores que estão em cache localmente no ficheiro *.azure/config,* incluindo o nome da aplicação, grupo de recursos e plano de Serviço de Aplicações.

Uma vez concluída a implementação, volte para a janela do navegador que abriu na **Navegação para o** passo da aplicação e aprovam a atualização.

![Aplicação de exemplo atualizada em execução no Azure](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

[Tendo problemas? Deixe-nos saber.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="manage-your-new-azure-app"></a>Gerencie a sua nova app Azure

Vá ao <a href="https://portal.azure.com" target="_blank">portal Azure</a> para gerir a app que criou.

A partir do menu esquerdo, clique em **Serviços de Aplicação** e, em seguida, clique no nome da sua aplicação Azure.

:::image type="content" source="./media/quickstart-dotnetcore/portal-app-service-list-up.png" alt-text="Screenshot da página serviços de aplicações mostrando um exemplo Azure app selecionado.":::

Veja a página geral da sua aplicação. Aqui, pode realizar tarefas de gestão básicas, como navegar, parar, iniciar, reiniciar e eliminar. 

![Página Serviço de Aplicações no portal do Azure](media/quickstart-dotnetcore/portal-app-overview-up.png)

O menu à esquerda fornece diferentes páginas para configurar a sua aplicação. 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

[Tendo problemas? Deixe-nos saber.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: ASP.NET Core app com base de dados SQL](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Configurar ASP.NET aplicação Core](configure-language-dotnetcore.md)

::: zone-end

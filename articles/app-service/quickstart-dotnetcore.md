---
title: 'Quickstart: Implementar uma aplicação web ASP.NET'
description: Saiba como executar aplicações web no Azure App Service implementando a sua primeira aplicação ASP.NET.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 03/30/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-ide
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-dotnetcore-uiex
ms.openlocfilehash: 7f538f5accb533b01c5ea685e424c70bfeb44f00
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058343"
---
<!-- NOTES:

I'm a .NET developer who wants to deploy my web app to App Service. I may develop apps with
Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET SDK/CLI. This article
should be able to guide .NET devs, whether they're app is .NET Core, .NET, or .NET Framework.

As a .NET developer, when choosing an IDE and .NET TFM - you map to various OS requirements.
For example, if you choose Visual Studio - you're developing the app on Windows, but you can still
target cross-platform with .NET Core 3.1 or .NET 5.0.

| .NET / IDE         | Visual Studio | Visual Studio for Mac | Visual Studio Code | Command line   |
|--------------------|---------------|-----------------------|--------------------|----------------|
| .NET Core 3.1      | Windows       | macOS                 | Cross-platform     | Cross-platform |
| .NET 5.0           | Windows       | macOS                 | Cross-platform     | Cross-platform |
| .NET Framework 4.8 | Windows       | N/A                   | Windows            | Windows        |

-->

# <a name="quickstart-deploy-an-aspnet-web-app"></a>Quickstart: Implementar uma aplicação web ASP.NET

Neste arranque rápido, você vai aprender a criar e implementar a sua primeira aplicação web ASP.NET para [o Azure App Service](overview.md). O App Service suporta várias versões de aplicações .NET e fornece um serviço de hospedagem web altamente escalável e auto-remendado. ASP.NET aplicações web são cross-platform e podem ser hospedadas em Linux ou Windows. Quando terminar, terá um grupo de recursos Azure composto por um plano de hospedagem do App Service e um Serviço de Aplicações com uma aplicação web implementada.

> [!TIP]
> .NET Core 3.1 é a atual libertação de .NET de suporte a longo prazo (LTS). Para obter mais informações, consulte [a política de suporte .NET](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

## <a name="prerequisites"></a>Pré-requisitos

:::zone target="docs" pivot="development-environment-vs"

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/dotnet)
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2019</a> com a ASP.NET e trabalho **de desenvolvimento web.**

    Se já instalou o Visual Studio 2019:

    - Instale as últimas atualizações no Estúdio Visual selecionando **o Help** Check  >  **for Updates**.
    - Adicione a carga de trabalho selecionando **Ferramentas**  >  **Obter Ferramentas e Funcionalidades**.

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/dotnet)
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio Code</a>.
- A extensão <a href="https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack" target="_blank">das Ferramentas Azure.</a>

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank"> Instale o mais recente .NET Core 3.1 SDK. </a>

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Instale o mais recente .NET 5.0 SDK. </a>

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> Instale o pacote de desenvolvedores .NET Framework 4.8. </a>

> [!NOTE]
> O Código do Estúdio Visual é, no entanto, transversal; .NET Framework não é. Se estiver a desenvolver aplicações .NET Framework com Código de Estúdio Visual, considere usar uma máquina Windows para satisfazer as dependências de construção.

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/dotnet)
- O <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI.</a>
- O .NET SDK (inclui tempo de execução e CLI).

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank"> Instale o mais recente .NET Core 3.1 SDK. </a>

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Instale o mais recente .NET 5.0 SDK. </a>

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Instale o mais recente .NET 5.0 SDK. </a> e <a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> o .NET Framework 4.8 Developer Pack. </a>

> [!NOTE]
> No entanto, [o .NET CLI](/dotnet/core/tools) é transversal; .NET Framework não é. Se estiver a desenvolver aplicações .NET Framework com o .NET CLI, considere utilizar uma máquina Windows para satisfazer as dependências de construção.

---

:::zone-end

## <a name="create-an-aspnet-web-app"></a>Criar uma aplicação Web ASP.NET

:::zone target="docs" pivot="development-environment-vs"

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

1. Abra o Estúdio Visual e, em seguida, **selecione Criar um novo projeto.**
1. In **Create a new project**, find and choose ASP.NET Web Core **App**, em seguida, selecione **Next**.
1. Em **Configurar o seu novo projeto**, nomeie a aplicação _MyFirstAzureWebApp_ e, em seguida, selecione **Next**.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="Configurar ASP.NET web Core 3.1" border="true":::

1. Selecione **.NET Core 3.1 (Suporte a longo prazo)**.
1. Certifique-se de que o **Tipo de Autenticação** está definido para **Nenhum**. Selecione **Criar**.

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-netcoreapp31.png" alt-text="Estúdio Visual - Selecione .NET Core 3.1 e Nenhum para Tipo de Autenticação." border="true":::

1. A partir do menu Visual Studio, selecione **Debug**  >  **Start Without Debugging** para executar a aplicação web localmente.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Estúdio Visual - .NET Core 3.1 navegue localmente" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

1. Abra o Estúdio Visual e, em seguida, **selecione Criar um novo projeto.**
1. In **Create a new project**, find and choose ASP.NET Web Core **App**, em seguida, selecione **Next**.
1. Em **Configurar o seu novo projeto**, nomeie a aplicação _MyFirstAzureWebApp_ e, em seguida, selecione **Next**.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="Visual Studio - Configurar ASP.NET aplicação web 5.0." border="true":::

1. Selecione **.NET Core 5.0 (Corrente)**.
1. Certifique-se de que o **Tipo de Autenticação** está definido para **Nenhum**. Selecione **Criar**.

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-net50.png" alt-text="Estúdio Visual - Informações adicionais ao selecionar .NET Core 5.0." border="true":::

1. A partir do menu Visual Studio, selecione **Debug**  >  **Start Without Debugging** para executar a aplicação web localmente.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio - ASP.NET Core 5.0 a funcionar localmente." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

1. Abra o Estúdio Visual e, em seguida, **selecione Criar um novo projeto.**
1. In **Create a new project**, find, and choose ASP.NET Web Application **(.NET Framework)** e, em seguida, selecione **Next**.
1. Em **Configurar o seu novo projeto**, nomeie a aplicação _MyFirstAzureWebApp_ e, em seguida, selecione **Create**.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-netframework48.png" alt-text="Visual Studio - Configurar ASP.NET aplicação web Framework 4.8." border="true":::

1. Selecione o modelo **MVC.**
1. Certifique-se de que a **autenticação** está definida para **não autenticação.** Selecione **Criar**.

   :::image type="content" source="media/quickstart-dotnet/vs-mvc-no-auth-netframework48.png" alt-text="Estúdio Visual - Selecione o modelo MVC." border="true":::

1. A partir do menu Visual Studio, selecione **Debug**  >  **Start Without Debugging** para executar a aplicação web localmente.

   :::image type="content" source="media/quickstart-dotnet/vs-local-webapp-netframework48.png" alt-text="Visual Studio - ASP.NET Framework 4.8 a funcionar localmente." lightbox="media/quickstart-dotnet/vs-local-webapp-netframework48.png" border="true":::

---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

Crie uma nova pasta chamada _MyFirstAzureWebApp_ e abra-a no Código do Estúdio Visual. Abra a janela <a href="https://code.visualstudio.com/docs/editor/integrated-terminal" target="_blank">Terminal</a> e crie uma nova aplicação web .NET utilizando o [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) comando.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

```dotnetcli
dotnet new webapp -f netcoreapp3.1
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

```dotnetcli
dotnet new webapp -f net5.0
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

```dotnetcli
dotnet new webapp --target-framework-override net48
```

> [!IMPORTANT]
> A `--target-framework-override` bandeira é uma substituição de texto em forma livre do nome-quadro-alvo (TFM) para o projeto, e não *garante* que o modelo de suporte exista ou compila. Só é possível construir e executar aplicações .NET Framework no Windows.

---

A partir do **Terminal** em Código de Estúdio Visual, execute a aplicação localmente usando o [`dotnet run`](/dotnet/core/tools/dotnet-run) comando.

```dotnetcli
dotnet run
```

Abra um browser e navegue para a aplicação em `https://localhost:5001`.


### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Você verá o modelo ASP.NET web Core 3.1 apresentado na página.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code - executar .NET Core 3.1 no navegador localmente." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Você verá o modelo ASP.NET web Core 5.0 apresentado na página.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code - corra .NET 5.0 no navegador localmente." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Você verá o modelo ASP.NET aplicação web Framework 4.8 exibida na página.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code - corra .NET 4.8 no navegador localmente." lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Abra uma janela terminal da sua máquina para um diretório de trabalho. Crie uma nova aplicação web .NET utilizando o [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) comando e, em seguida, altere os diretórios para a aplicação recém-criada.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp -f netcoreapp3.1 && cd MyFirstAzureWebApp
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp -f net5.0 && cd MyFirstAzureWebApp
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp --target-framework-override net48 && cd MyFirstAzureWebApp
```

> [!IMPORTANT]
> A `--target-framework-override` bandeira é uma substituição de texto em forma livre do nome-quadro-alvo (TFM) para o projeto, e não *garante* que o modelo de suporte exista ou compila. Só é possível construir aplicações .NET Framework no Windows.

---

A partir da mesma sessão terminal, execute a aplicação localmente usando o [`dotnet run`](/dotnet/core/tools/dotnet-run) comando.

```dotnetcli
dotnet run
```

Abra um browser e navegue para a aplicação em `https://localhost:5001`.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Você verá o modelo ASP.NET web Core 3.1 apresentado na página.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code - ASP.NET Core 3.1 no navegador local." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Você verá o modelo ASP.NET web Core 5.0 apresentado na página.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code - ASP.NET Core 5.0 no navegador local." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Você verá o modelo ASP.NET aplicação web Framework 4.8 exibida na página.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code - ASP.NET Framework 4.8 no navegador local." lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

## <a name="publish-your-web-app"></a>Publique a sua aplicação web

Para publicar a sua aplicação web, tem primeiro de criar e configurar um novo Serviço de Aplicações para o quais pode publicar a sua aplicação.

Como parte da criação do Serviço de Aplicações, irá criar:

- Um novo grupo de [recursos](../azure-resource-manager/management/overview.md#terminology) para conter todos os recursos da Azure para o serviço.
- Um novo [Plano de Hospedagem](overview-hosting-plans.md) que especifica a localização, tamanho e funcionalidades da fazenda de servidores web que hospeda a sua aplicação.

Siga estes passos para criar o seu Serviço de Aplicações e publique a sua aplicação web:

:::zone target="docs" pivot="development-environment-vs"

1. No **Solution Explorer,** clique com o botão direito no projeto **MyFirstAzureWebApp** e selecione **Publicar**.
1. In **Publish**, selecione **Azure** e, em **seguida, Next**.

    :::image type="content" source="media/quickstart-dotnet/vs-publish-target-Azure.png" alt-text="Visual Studio - Publique a aplicação web e destino Azure." border="true":::

1. As suas opções dependem se já está inscrito no Azure e se tem uma conta visual studio ligada a uma conta Azure. Selecione **Adicione uma conta** ou **inscreva-se** para iniciar súmis na sua subscrição Azure. Se já fez a sua assinatura, selecione a conta que pretende.

    :::image type="content" source="media/quickstart-dotnetcore/sign-in-Azure-vs2019.png" border="true" alt-text="Visual Studio - Selecione iniciar sação no diálogo Azure.":::

1. Escolha o **alvo específico**, ou **Azure App Service (Linux)** ou **Azure App Service (Windows)**.

    > [!IMPORTANT]
    > Ao direcionar ASP.NET Quadro 4.8, utilizará **o Azure App Service (Windows)**.

1. À direita das instâncias do Serviço de **Aplicações,** selecione **+** .

    :::image type="content" source="media/quickstart-dotnetcore/publish-new-app-service.png" border="true" alt-text="Visual Studio - Novo diálogo de aplicações do Serviço de Aplicações.":::

1. Para **Subscrição**, aceite a subscrição listada ou selecione uma nova da lista de drop-down.
1. Para **o grupo de recursos**, selecione **New**. No **nome do grupo de novos recursos,** insira o *myResourceGroup* e selecione **OK**.
1. Para **o Plano de Hospedagem**, selecione **New**.
1. No **Plano de Hospedagem: Criar novo** diálogo, introduzir os valores especificados no quadro seguinte:

    | Definição          | Valor sugerido          | Descrição                                                           |
    |------------------|--------------------------|-----------------------------------------------------------------------|
    | **Plano de Alojamento** | *MyFirstAzureWebAppPlan* | Nome do plano de serviço de aplicações.                                         |
    | **Localização**     | *Europa Ocidental*            | O centro de dados onde o a aplicação Web está alojada.                           |
    | **Tamanho**         | *Gratuito*                   | O [escalão de preço][app-service-pricing-tier] determina as funcionalidades do alojamento. |

    :::image type="content" source="media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png" border="true" alt-text="Criar novo Plano de Hospedagem":::

1. In **Name**, insira um nome de aplicação único que inclui apenas os caracteres válidos são `a-z` , e `A-Z` `0-9` `-` . Pode aceitar o nome único gerado automaticamente. O URL da aplicação Web é `http://<app-name>.azurewebsites.net`, em que `<app-name>` é o nome da aplicação.
1. Selecione **Criar** para criar os recursos Azure.

    :::image type="content" source="media/quickstart-dotnetcore/web-app-name-vs2019.png" border="true" alt-text="Visual Studio - Criar diálogo de recursos de aplicações.":::

   Assim que o assistente estiver completo, os recursos do Azure são criados para si e está pronto para publicar.

1. **Selecione Acabamento** para fechar o assistente.
1. Na página **Publicar,** **selecione Publicar.** Visual Studio constrói, embala e publica a app para o Azure, e depois lança a aplicação no navegador padrão.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Você verá a aplicação web core 3.1 ASP.NET exibida na página.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio - ASP.NET web Core 3.1 em Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Você verá a aplicação web core 5.0 ASP.NET exibida na página.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio - ASP.NET web Core 5.0 em Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    Você verá a aplicação web ASP.NET Framework 4.8 exibida na página.

    :::image type="content" source="media/quickstart-dotnet/vs-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio - ASP.NET Framework 4.8 web app em Azure.":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

Para implementar a sua aplicação web utilizando a extensão Visual Studio Azure Tools:

1. No Código do Estúdio Visual, abra a [**Paleta de Comando,**](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette) <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>P</kbd>.
1. Procure e selecione "Azure App Service: Deploy to Web App".
1. Responda às instruções da seguinte forma:

    - Selecione *MyFirstAzureWebApp* como a pasta a implementar.
    - **Selecione Add Config** quando solicitado.
    - Se solicitado, inscreva-se na sua conta Azure existente.

    :::image type="content" source="media/quickstart-dotnet/vscode-sign-in-to-Azure.png" alt-text="Visual Studio Code - Inscreva-se no Azure." border="true":::

    - Selecione a sua **Subscrição**.
    - Selecione **Criar nova Aplicação Web... Avançado.**
    - Para **introduzir um nome globalmente único,** use um nome único em todo o Azure *(caracteres válidos `a-z` `0-9` são, e `-`*). Um bom padrão é usar uma combinação do nome da sua empresa e um identificador de aplicativos.
    - Selecione **Criar um novo grupo de recursos** e fornecer um nome como `myResourceGroup` .
    - Quando solicitado para **selecionar uma pilha de tempo de execução**:
      - Para *.NET Core 3.1*, selecione **.NET Core 3.1 (LTS)**
      - Para *.NET 5.0*, selecione **.NET 5**
      - Para *.NET Framework 4.8*, selecione **ASP.NET V4.8**
    - Selecione um sistema operativo (Windows ou Linux).
        - Para *.NET Framework 4.8*, o Windows será selecionado implicitamente.
    - Selecione **Criar um novo plano de Serviço de Aplicações,** fornecer um nome e selecionar o [nível de preços][app-service-pricing-tier] **F1 Free** .
    - Selecione **Skip por enquanto** para o recurso Application Insights.
    - Selecione uma localização próxima de si.

1. Ao publicar concluído, **selecione Browse Website** na notificação e selecione **Open** quando solicitado.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Você verá a aplicação web core 3.1 ASP.NET exibida na página.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio Code - ASP.NET web Core 3.1 em Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Você verá a aplicação web core 5.0 ASP.NET exibida na página.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio Code - ASP.NET web Core 5.0 em Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    Você verá a aplicação web ASP.NET Framework 4.8 exibida na página.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code - ASP.NET aplicação web Framework 4.8 em Azure.":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Implemente o código no seu diretório *local MyFirstAzureWebApp* utilizando o [`az webapp up`](/cli/azure/webapp#az_webapp_up) comando:

```azurecli
az webapp up --sku F1 --name <app-name> --os-type <os>
```

- Se o `az` comando não for reconhecido, certifique-se de que tem o CLI Azure instalado como descrito em [Pré-Requisitos](#prerequisites).
- Substitua `<app-name>` por um nome único em todo o Azure *(caracteres válidos `a-z` `0-9` são, e `-`*. Um bom padrão é usar uma combinação do nome da sua empresa e um identificador de aplicativos.
- O `--sku F1` argumento cria a aplicação web no [nível de preços][app-service-pricing-tier] **gratuitos.** Omita este argumento para usar um nível premium mais rápido, que incorre num custo de hora a hora.
- `<os>`Substitua-o por um `linux` ou `windows` . Deve utilizar `windows` ao direcionar ASP.NET Quadro *4.8*.
- Pode opcionalmente incluir o argumento `--location <location-name>` onde `<location-name>` está uma região de Azure disponível. Pode recuperar uma lista de regiões admissíveis para a sua conta Azure, executando o [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) comando.

O comando pode demorar alguns minutos a ser concluído. Durante a execução, fornece mensagens sobre a criação do grupo de recursos, o plano de Serviço de Aplicações e a aplicação de hospedagem, configurando o registo e, em seguida, executando a implementação zip. Em seguida, produz uma mensagem com o URL da aplicação:

```azurecli
You can launch the app at http://<app-name>.azurewebsites.net
```

Abra um navegador web e navegue para o URL:

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Você verá a aplicação web core 3.1 ASP.NET exibida na página.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="CLI - ASP.NET web Core 3.1 em Azure.":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Você verá a aplicação web core 5.0 ASP.NET exibida na página.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="CLI - ASP.NET web Core 5.0 em Azure.":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Você verá a aplicação web ASP.NET Framework 4.8 exibida na página.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/Azure-webapp-net48.png" border="true" alt-text="CLI - ASP.NET Aplicação web Framework 4.8 em Azure.":::

---

:::zone-end

## <a name="update-the-app-and-redeploy"></a>Atualizar a aplicação e reimplementar

Siga estes passos para atualizar e redistribuir a sua aplicação web:

:::zone target="docs" pivot="development-environment-vs"

1. No **Solution Explorer,** no âmbito do seu projeto, abra *Index.cshtml*.
1. Substitua o primeiro `<div>` elemento pelo seguinte código:

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   Guarde as alterações.

1. Para recolocar o Azure, clique com o botão direito no projeto **MyFirstAzureWebApp** no **Solution Explorer** e selecione **Publicar**.
1. Na página do resumo da **Publicação,** **selecione Publicar**.

    Quando a publicação estiver concluída, o Visual Studio inicia um browser para o URL da aplicação Web.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Verá a aplicação ASP.NET web Core 3.1 atualizada apresentada na página.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio - Atualizado ASP.NET web Core 3.1 em Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Verá a aplicação ASP.NET web core 5.0 atualizada apresentada na página.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio - Atualizado ASP.NET web Core 5.0 em Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    Verá a aplicação web ASP.NET Framework 4.8 atualizada exibida na página.

    :::image type="content" source="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio - Atualizado ASP.NET aplicação web Framework 4.8 em Azure.":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

1. Abra o *Index.cshtml*.
1. Substitua o primeiro `<div>` elemento pelo seguinte código:

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   Guarde as alterações.

1. Abra a Barra **Lateral** do Código do Estúdio Visual, selecione o ícone **Azure** para expandir as suas opções.
1. Sob o nó **app SERVICE,** expanda a sua subscrição e clique com o botão direito no **MyFirstAzureWebApp**.
1. Selecione a **Aplicação de Implementação para Web...**.
1. **Selecione Implementar** quando solicitado.
1. Ao publicar concluído, **selecione Browse Website** na notificação e selecione **Open** quando solicitado.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Verá a aplicação ASP.NET web Core 3.1 atualizada apresentada na página.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code - Atualizado ASP.NET web Core 3.1 em Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Verá a aplicação ASP.NET web core 5.0 atualizada apresentada na página.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code - Atualizado ASP.NET web Core 5.0 em Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    Verá a aplicação web ASP.NET Framework 4.8 atualizada exibida na página.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code - Atualizado ASP.NET aplicação web Framework 4.8 em Azure.":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

No diretório local, abra o ficheiro *Index.cshtml.* Substitua o primeiro `<div>` elemento:

```razor
<div class="jumbotron">
    <h1>.NET 💜 Azure</h1>
    <p class="lead">Example .NET app to Azure App Service.</p>
</div>
```

Guarde as alterações e, em seguida, volte a recolocar a aplicação utilizando o `az webapp up` comando:

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

ASP.NET Core 3.1 é uma plataforma cruzada, com base na sua substituição anterior `<os>` por qualquer um ou `linux` `windows` .

```azurecli
az webapp up --os-type <os>
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

ASP.NET Core 5.0 é uma plataforma cruzada, com base na sua substituição anterior `<os>` por qualquer um ou `linux` `windows` .

```azurecli
az webapp up --os-type <os>
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

ASP.NET Quadro 4.8 tem dependências-quadro e deve ser alojado no Windows.

```azurecli
az webapp up --os-type windows
```

> [!TIP]
> Se estiver interessado em hospedar as suas aplicações .NET no Linux, considere migrar de [ASP.NET Framework para ASP.NET Core](/aspnet/core/migration/proper-to-2x).

---

Este comando utiliza valores que estão em cache localmente no ficheiro *.azure/config,* incluindo o nome da aplicação, grupo de recursos e plano de Serviço de Aplicações.

Uma vez concluída a implementação, volte para a janela do navegador que abriu na **Navegação para o** passo da aplicação e aprovam a atualização.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Verá a aplicação ASP.NET web Core 3.1 atualizada apresentada na página.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLI - Atualizado ASP.NET web Core 3.1 em Azure.":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Verá a aplicação ASP.NET web core 5.0 atualizada apresentada na página.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLI - Atualizado ASP.NET web Core 5.0 em Azure.":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Verá a aplicação web ASP.NET Framework 4.8 atualizada exibida na página.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="CLI - Aplicação web 4.8 do Quadro de ASP.NET atualizado em Azure.":::

---

:::zone-end

## <a name="manage-the-azure-app"></a>Gerir a app Azure

Para gerir a sua aplicação web, vá ao [portal Azure](https://portal.azure.com)e procure e selecione **Serviços de Aplicações.**

:::image type="content" source="media/quickstart-dotnetcore/app-services.png" alt-text="Portal Azure - Selecione a opção Serviços de Aplicações.":::

Na página **serviços de aplicações,** selecione o nome da sua aplicação web.

:::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Página Azure Portal - Serviços de Aplicações com uma aplicação web de exemplo selecionada.":::

A página **'Vista Geral'** para a sua aplicação web contém opções para gestão básica como navegar, parar, iniciar, reiniciar e excluir. O menu esquerdo fornece mais páginas para configurar a sua aplicação.

:::image type="content" source="media/quickstart-dotnetcore/web-app-overview-page.png" alt-text="Azure Portal - Página geral do Serviço de Aplicações.":::

<!--
## Clean up resources - H2 added from the next three includes
-->
:::zone target="docs" pivot="development-environment-vs"
[!INCLUDE [Clean-up Portal web app resources](../../includes/clean-up-section-portal-web-app.md)]
:::zone-end

:::zone target="docs" pivot="development-environment-vscode"
[!INCLUDE [Clean-up Portal web app resources](../../includes/clean-up-section-portal-web-app.md)]
:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->
[!INCLUDE [Clean-up CLI resources](../../includes/cli-samples-clean-up.md)]
:::zone-end

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou e implementou uma aplicação web ASP.NET para o Azure App Service.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Avance para o próximo artigo para aprender a criar uma aplicação .NET Core e conectá-la a uma Base de Dados SQL:

> [!div class="nextstepaction"]
> [Tutorial: ASP.NET Core app com base de dados SQL](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Configurar ASP.NET aplicação Core 3.1](configure-language-dotnetcore.md)

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Avance para o próximo artigo para aprender a criar uma aplicação .NET Core e conectá-la a uma Base de Dados SQL:

> [!div class="nextstepaction"]
> [Tutorial: ASP.NET Core app com base de dados SQL](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Configurar ASP.NET aplicação Core 5.0](configure-language-dotnetcore.md)

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Avance para o próximo artigo para aprender a criar uma aplicação .NET Framework e conectá-la a uma Base de Dados SQL:

> [!div class="nextstepaction"]
> [Tutorial: ASP.NET app com base de dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md)

> [!div class="nextstepaction"]
> [Configurar a aplicação Framework ASP.NET](configure-language-dotnet-framework.md)

---

[app-service-pricing-tier]: https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

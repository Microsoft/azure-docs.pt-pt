---
title: Quickstart para configuração de app Azure com ASP.NET Core  Microsoft Docs
description: Quickstart para usar configuração de app Azure com aplicações ASP.NET Core
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: ee50d180c579e117c16f1a956871068f0a46e976
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77498564"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Quickstart: Crie uma app core ASP.NET com configuração de app Azure

Neste arranque rápido, utilizará a Configuração da Aplicação Azure para centralizar o armazenamento e gestão das definições de aplicação para uma aplicação ASP.NET Core. ASP.NET Core constrói um único objeto de configuração baseado em valor-chave utilizando definições de uma ou mais fontes de dados especificadas por uma aplicação. Estas fontes de dados são conhecidas como *fornecedores de configuração*. Uma vez que o cliente .NET Core da Configuração da App é implementado como um fornecedor de configuração, o serviço aparece como outra fonte de dados.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [SDK .NET Core](https://dotnet.microsoft.com/download)

>[!TIP]
> O Azure Cloud Shell é uma concha interativa gratuita que pode usar para executar as instruções da linha de comando neste artigo.  Tem ferramentas azure comuns pré-instaladas, incluindo o .NET Core SDK. Se estiver ligado à sua subscrição Azure, lance o seu [Azure Cloud Shell](https://shell.azure.com) a partir de shell.azure.com.  Você pode saber mais sobre Azure Cloud Shell lendo a [nossa documentação](../cloud-shell/overview.md)

## <a name="create-an-app-configuration-store"></a>Criar uma loja de configuração de aplicações

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione O Explorador de **Configuração** > **Criar** para adicionar os seguintes pares de valor-chave:

    | Chave | Valor |
    |---|---|
    | TestApp:Settings:BackgroundColor | Branco |
    | TestApp:Definições:FontSize | 24 |
    | TestApp:Settings:FontColor | Preto |
    | TestApp:Definições:Mensagem | Dados da Configuração de Aplicações Azure |

    Deixe o **rótulo** e o **tipo de conteúdo** vazios por enquanto.

## <a name="create-an-aspnet-core-web-app"></a>Criar uma aplicação Web ASP.NET Core

Utilize a [interface de linha de comando .NET Core (CLI)](https://docs.microsoft.com/dotnet/core/tools/) para criar um novo ASP.NET projeto de aplicação web Core MVC. A [Azure Cloud Shell](https://shell.azure.com) fornece estas ferramentas para si.  Estão também disponíveis nas plataformas Windows, macOS e Linux.

1. Crie uma pasta nova para o projeto. Para este arranque rápido, nomeie-o *TestAppConfig*.

1. Na nova pasta, execute o seguinte comando para criar um novo ASP.NET projeto de aplicação web Core MVC:

```dotnetcli
dotnet new mvc --no-https
```

## <a name="add-secret-manager"></a>Adicionar Gestor Secreto

Para utilizar o Secret Manager, adicione um elemento `UserSecretsId` ao seu ficheiro *.csproj.*

Abra o ficheiro *.csproj.* Adicione um elemento `UserSecretsId` como mostrado aqui. Pode utilizar o mesmo GUID, ou pode substituir este valor pelo seu. Guarde o ficheiro.

> [!IMPORTANT]
> `CreateHostBuilder` substitui `CreateWebHostBuilder` em .NET Core 3.0.  Selecione a sintaxe correta com base no seu ambiente.

#### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.App" />
        <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
    </ItemGroup>

</Project>
```

#### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    
    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

</Project>
```
---

Esta ferramenta armazena os dados confidenciais dos projetos de programação fora da árvore dos projetos. Esta abordagem ajuda a evitar a partilha acidental de segredos de aplicações no código fonte. Para mais informações sobre o Gestor Secreto, consulte [o armazenamento seguro de segredos de aplicações em desenvolvimento em ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/app-secrets)

## <a name="connect-to-an-app-configuration-store"></a>Ligar a uma loja de configuração de aplicações

1. Adicione uma referência ao pacote nuGet `Microsoft.Azure.AppConfiguration.AspNetCore` executando o seguinte comando:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Execute o seguinte comando para restaurar as embalagens para o seu projeto:

    ```dotnetcli
    dotnet restore
    ```

1. Adicione um segredo chamado *ConnectionStrings:AppConfig* ao Secret Manager.

    Este segredo contém a cadeia de ligação para aceder à sua loja de configuração de aplicações. Substitua o valor no seguinte comando com a cadeia de ligação para a sua loja de configuração de aplicações.

    Este comando tem de ser executado no mesmo diretório que o ficheiro *.csproj*.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    > [!IMPORTANT]
    > Algumas conchas truncarão a corda de ligação a menos que seja fechada em aspas. Certifique-se de que a saída do comando `dotnet user-secrets` mostra toda a cadeia de ligação. Se não o fizer, refaça o comando, encerrando a cadeia de ligação em aspas.

    O Secret Manager é usado apenas para testar a aplicação web localmente. Quando a aplicação é implantada para o [Azure App Service](https://azure.microsoft.com/services/app-service/web), por exemplo, utiliza a definição da aplicação **Connection Strings** no Serviço de Aplicações em vez do Secret Manager para armazenar a cadeia de ligação.

    Aceda a este segredo utilizando a Configuração API. Um cólon (:) funciona no nome de configuração com a Configuração API em todas as plataformas suportadas. Ver [Configuração por ambiente.](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0)

1. Abra *Program.cs*e adicione uma referência ao fornecedor de configuração de aplicações .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Atualize o método `CreateWebHostBuilder` para utilizar a Configuração da App, ligando para o método `config.AddAzureAppConfiguration()`.

    > [!IMPORTANT]
    > `CreateHostBuilder` substitui `CreateWebHostBuilder` em .NET Core 3.0.  Selecione a sintaxe correta com base no seu ambiente.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
        })
        .UseStartup<Startup>());
    ```

    ---

1. Navegue para *<app root>/Vistas/Casa* e abra *o Index.cshtml*. Substitua o seu conteúdo pelo seguinte código:

    ```HTML
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]</h1>
    ```

1. Navegue para *<app root>/Vistas/Partilhados* e *abertos _Layout.cshtml*. Substitua o seu conteúdo pelo seguinte código:

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>@ViewData["Title"] - hello_world</title>

        <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
        <link rel="stylesheet" href="~/css/site.css" />
    </head>
    <body>
        <div class="container body-content">
            @RenderBody()
        </div>

        <script src="~/lib/jquery/dist/jquery.js"></script>
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>

        @RenderSection("Scripts", required: false)
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Construir e executar a app localmente

1. Para construir a aplicação utilizando o CLI .NET Core, navegue para o diretório raiz da sua aplicação e execute o seguinte comando na concha de comando:

    ```dotnetcli
    dotnet build
    ```

1. Depois de a construção concluída com sucesso, execute o seguinte comando para executar a aplicação web localmente:

    ```dotnetcli
    dotnet run
    ```

1. Se estiver a trabalhar na sua máquina local, use um browser para navegar para `http://localhost:5000`. Este é o URL padrão para a aplicação web hospedada localmente.  

Se estiver a trabalhar na Casca de Nuvem Azure, selecione o botão *de pré-visualização web* seguido de *Configurar*.  

![Localizar o botão de pré-visualização da Web](./media/quickstarts/cloud-shell-web-preview.png)

Quando for solicitado para configurar a porta para pré-visualização, insira '5000' e selecione *Open e nave.*  A página web irá ler "Dados da Configuração da Aplicação Azure".

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou uma nova loja de configuração de aplicações e utilizou-a com uma aplicação web ASP.NET Core através do fornecedor de Configuração de [Aplicações.](https://go.microsoft.com/fwlink/?linkid=2074664) Para aprender a configurar a sua aplicação ASP.NET Core para atualizar dinamicamente as configurações de configuração, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Ativar a configuração dinâmica](./enable-dynamic-configuration-aspnet-core.md)

---
title: Início rápido para configuração de Azure App com ASP.NET Core | Microsoft Docs
description: Um guia de início rápido para usar a configuração de Azure App com aplicativos ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET Core
ms.workload: tbd
ms.date: 10/11/2019
ms.author: yegu
ms.openlocfilehash: 4e08192788329e7a835ddb0b6b3f1aa01b2c73e1
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299948"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Início rápido: criar um aplicativo ASP.NET Core com a configuração Azure App

Neste guia de início rápido, você incorpora a configuração de Azure App em um aplicativo ASP.NET Core para centralizar o armazenamento e o gerenciamento de configurações de aplicativo separadas do seu código. ASP.NET Core cria um único objeto de configuração com base em valor chave usando as configurações de uma ou mais fontes de dados que são especificadas por um aplicativo. Essas fontes de dados são conhecidas como *provedores de configuração*. Como o cliente .NET Core da configuração de aplicativo é implementado como tal provedor, o serviço aparece como outra fonte de dados.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [SDK .NET Core](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Criar um repositório de configurações de aplicativo

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione **Configuration Explorer** >  **+ criar** para adicionar os seguintes pares de chave-valor:

    | Chave | Valor |
    |---|---|
    | TestApp: configurações: BackgroundColor | Branco |
    | TestApp: configurações: FontSize | 24 |
    | TestApp: configurações: FontColor | Preto |
    | TestApp: configurações: mensagem | Dados da configuração Azure App |

    Deixe **rótulo** e **tipo de conteúdo** vazio por enquanto.

## <a name="create-an-aspnet-core-web-app"></a>Criar uma aplicação Web ASP.NET Core

Você usa a [CLI (interface de linha de comando) do .NET Core](https://docs.microsoft.com/dotnet/core/tools/) para criar um novo projeto de aplicativo web do ASP.NET Core MVC. A vantagem de usar o CLI do .NET Core sobre o Visual Studio é que ele está disponível nas plataformas Windows, macOS e Linux.

1. Crie uma pasta nova para o projeto. Para este guia de início rápido, nomeie-o *TestAppConfig*.

2. Na nova pasta, execute o seguinte comando para criar um novo projeto de aplicativo Web ASP.NET Core MVC:

    ```CLI
        dotnet new mvc --no-https
    ```

## <a name="add-secret-manager"></a>Adicionar Gerenciador de segredo

Para usar o Gerenciador de segredo, adicione um elemento `UserSecretsId` ao seu arquivo *. csproj* .

- Abra o arquivo *. csproj* . Adicione um elemento `UserSecretsId`, como mostrado aqui. Você pode usar o mesmo GUID ou pode substituir esse valor pelo seu próprio. Guarde o ficheiro.

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

Esta ferramenta armazena os dados confidenciais dos projetos de programação fora da árvore dos projetos. Esta abordagem ajuda a evitar a partilha acidental de segredos de aplicações no código fonte. Para obter mais informações sobre o Gerenciador de segredo, consulte [armazenamento seguro de segredos do aplicativo em desenvolvimento no ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/app-secrets)

## <a name="connect-to-an-app-configuration-store"></a>Conectar-se a um repositório de configuração de aplicativo

1. Adicione uma referência ao pacote NuGet `Microsoft.Azure.AppConfiguration.AspNetCore` executando o seguinte comando:

    ```CLI
        dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-010060003-1250
    ```
2. Execute o seguinte comando para restaurar os pacotes para seu projeto:

    ```CLI
        dotnet restore
    ```
3. Adicione um segredo denominado *ConnectionStrings: AppConfig* ao Gerenciador de segredo.

    Esse segredo contém a cadeia de conexão para acessar seu repositório de configuração de aplicativo. Substitua o valor no comando a seguir pela cadeia de conexão para o repositório de configuração do aplicativo.

    Este comando tem de ser executado no mesmo diretório que o ficheiro *.csproj*.

    ```CLI
        dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    > [!IMPORTANT]
    > Alguns shells truncarão a cadeia de conexão, a menos que seja colocado entre aspas. Verifique se a saída do comando `dotnet user-secrets` mostra a cadeia de conexão inteira. Se não estiver, execute novamente o comando, colocando a cadeia de conexão entre aspas.

    O Gerenciador de segredo é usado apenas para testar o aplicativo Web localmente. Quando o aplicativo é implantado no [serviço Azure app](https://azure.microsoft.com/services/app-service/web), por exemplo, você usa uma configuração de aplicativo **cadeias de conexão** no serviço de aplicativo em vez de com o Gerenciador de segredo para armazenar a cadeia de conexão.

    Esse segredo é acessado com a API de configuração. Dois-pontos (:) funciona no nome da configuração com a API de configuração em todas as plataformas com suporte. Consulte [configuração por ambiente](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

4. Abra *Program.cs*e adicione uma referência ao provedor de configuração do aplicativo .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

5. Atualize o método `CreateWebHostBuilder` para usar a configuração de aplicativo chamando o método `config.AddAzureAppConfiguration()`.
    
    > [!IMPORTANT]
    > `CreateHostBuilder` substitui `CreateWebHostBuilder` no .NET Core 3,0.  Selecione a sintaxe correta com base em seu ambiente.

    ### <a name="update-createwebhostbuilder-for-net-core-2x"></a>Atualizar `CreateWebHostBuilder` para .NET Core 2. x

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

    ### <a name="update-createhostbuilder-for-net-core-3x"></a>Atualizar `CreateHostBuilder` para .NET Core 3. x

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

6. Abra *index. cshtml* nas exibições > diretório base e substitua seu conteúdo pelo código a seguir:

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

7. Abra *_ layout. cshtml* nas exibições > diretório compartilhado e substitua seu conteúdo pelo código a seguir:

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

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Para compilar o aplicativo usando o CLI do .NET Core, execute o seguinte comando no Shell de comando:

    ```CLI
       dotnet build
    ```

2. Depois que a compilação for concluída com êxito, execute o seguinte comando para executar o aplicativo Web localmente:

    ```CLI
        dotnet run
    ```

3. Abra uma janela do navegador e vá para `http://localhost:5000`, que é a URL padrão para o aplicativo Web hospedado localmente.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou um novo repositório de configuração de aplicativo e o utilizou com um aplicativo Web ASP.NET Core por meio do [provedor de configuração de aplicativo](https://go.microsoft.com/fwlink/?linkid=2074664). Para saber mais sobre como usar a configuração de aplicativo, prossiga para o próximo tutorial que demonstra como configurar seu aplicativo Web para atualizar dinamicamente as definições de configuração.

> [!div class="nextstepaction"]
> [Usar a configuração dinâmica em um aplicativo ASP.NET Core](./enable-dynamic-configuration-aspnet-core.md)

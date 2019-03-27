---
title: Início rápido para a configuração de aplicações do Azure com o ASP.NET Core | Documentos da Microsoft
description: Um guia de introdução para utilizar a configuração de aplicações do Azure com aplicações de ASP.NET Core
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
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: a721cc2252619923496ee5a3a8ae590a5cda3b04
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487554"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Início rápido: Criar uma aplicação ASP.NET Core com a configuração de aplicações do Azure

Configuração de aplicações do Azure é um serviço de configuração gerida no Azure. Pode usá-lo facilmente armazenar e gerir todas as suas definições de aplicação num único local que é separada a partir do código. Este guia de introdução mostra-lhe como incorporar o serviço numa aplicação web ASP.NET Core. 

ASP.NET Core baseia-se um objeto de configuração baseada em chave-valor único com definições de um ou mais origens de dados que são especificadas por uma aplicação. Estas origens de dados são conhecidas como *fornecedores de configuração*. Porque a .NET Core configuração de aplicações, o cliente está implementado como tal, um fornecedor, o serviço é apresentado como outra origem de dados.

Pode utilizar qualquer editor de código para realizar os passos neste guia de introdução. [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção disponível no Windows, macOS e plataformas Linux.

## <a name="prerequisites"></a>Pré-requisitos

Para fazer este início rápido, instale o [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Criar um arquivo de configuração de aplicação

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>Criar uma aplicação Web ASP.NET Core

Utilizar o [.NET Core interface de linha de comandos (CLI)](https://docs.microsoft.com/dotnet/core/tools/) para criar um novo projeto de aplicação web de MVC do ASP.NET Core. A vantagem de utilizar a CLI do .NET Core ao longo do Visual Studio é que ele está disponível para todas as plataformas de Linux, macOS e Windows.

1. Crie uma pasta nova para o projeto. Neste início rápido, um nome *TestAppConfig*.

2. Na nova pasta, execute o seguinte comando para criar um novo projeto de aplicação web de MVC do ASP.NET Core:

        dotnet new mvc

## <a name="add-secret-manager"></a>Adicionar Gestor de segredo

Adicionar a [ferramenta Gerenciador de segredo](https://docs.microsoft.com/aspnet/core/security/app-secrets) ao seu projeto. Esta ferramenta armazena os dados confidenciais dos projetos de programação fora da árvore dos projetos. Esta abordagem ajuda a evitar a partilha acidental de segredos de aplicações no código fonte.

- Abra o ficheiro *.csproj*. Adicionar um `UserSecretsId` elemento, conforme mostrado aqui e substitua o respetivo valor com os seus próprios, que é normalmente um GUID. Guarde o ficheiro.

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

## <a name="connect-to-an-app-configuration-store"></a>Ligar a um arquivo de configuração de aplicação

1. Adicionar uma referência para o `Microsoft.Extensions.Configuration.AzureAppConfiguration` pacote NuGet ao executar o seguinte comando:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration --version 1.0.0-preview-007830001

2. Execute o seguinte comando para restaurar pacotes para o seu projeto:

        dotnet restore

3. Adicionar um segredo com o nome *ConnectionStrings:AppConfig* ao Gestor de segredo.

    Este segredo contém a cadeia de ligação para aceder ao seu arquivo de configuração de aplicação. Substitua o valor no comando seguinte a cadeia de ligação do seu arquivo de configuração de aplicação.

    Este comando tem de ser executado no mesmo diretório que o ficheiro *.csproj*.

        dotnet user-secrets set ConnectionStrings:AppConfig "Endpoint=<your_endpoint>;Id=<your_id>;Secret=<your_secret>"

    Segredo Manager é utilizada apenas para testar a aplicação web localmente. Quando a aplicação for implementada, por exemplo, a [App Service do Azure](https://azure.microsoft.com/services/app-service/web), utilize uma aplicação de definir, por exemplo, **cadeias de ligação** no serviço de aplicações. Utilize esta definição em vez de armazenar a cadeia de ligação com o Gestor de segredo.

    Este segredo é acessado com a API de configuração. Dois pontos (:) funciona no nome da configuração com a API de configuração nas plataformas suportadas. Ver [configuração pelo ambiente](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

4. Abra o ficheiro Program.cs e atualizar o `CreateWebHostBuilder` método a utilizar a configuração de aplicações ao chamar o `config.AddAzureAppConfiguration()` método.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    ...

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .SetOfflineCache(new OfflineFileCache());
                });
            })
            .UseStartup<Startup>();
    ```

5. Abra o Index. cshtml nas vistas > diretório de casa e substituir seu conteúdo com o código a seguir:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@Configuration["TestApp:Settings:Message"]</h1>
    </body>
    </html>
    ```

6. Abrir o layout. cshtml nas vistas > partilhados diretório e substituir seu conteúdo com o código a seguir:

    ```html
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

## <a name="build-and-run-the-app-locally"></a>Criar e executar a aplicação localmente

1. Para criar a aplicação com a CLI do .NET Core, execute o seguinte comando na shell de comandos:

        dotnet build

2. Após a compilação for concluída com êxito, execute o seguinte comando para executar a aplicação web localmente:

        dotnet run

3. Abra uma janela do browser e aceda a `http://localhost:5000`, que é o URL predefinido da aplicação web alojada localmente.

    ![Início rápido iniciação da aplicação local](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou um novo arquivo de configuração de aplicação e Use com uma aplicação web do ASP.NET Core através da [fornecedor de configuração de aplicação](https://go.microsoft.com/fwlink/?linkid=2074664). Para saber mais sobre como utilizar a configuração de aplicações, avance para o próximo tutorial que demonstra a autenticação.

> [!div class="nextstepaction"]
> [Identidades geridas para a integração de recursos do Azure](./integrate-azure-managed-service-identity.md)

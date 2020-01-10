---
title: Início rápido para configuração de Azure App com ASP.NET Core | Microsoft Docs
description: Início rápido para uso da configuração de Azure App com aplicativos ASP.NET Core
services: azure-app-configuration
author: jpconnock
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.date: 01/04/2020
ms.author: jeconnoc
ms.openlocfilehash: f625135f036ec8fc816bc3c3eb6c76c635c51fe9
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75690202"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Início rápido: criar um aplicativo ASP.NET Core com a configuração Azure App

Neste guia de início rápido, você usará Azure App configuração para centralizar o armazenamento e o gerenciamento de configurações de aplicativo para um aplicativo ASP.NET Core. ASP.NET Core cria um único objeto de configuração com base em valor chave usando as configurações de uma ou mais fontes de dados especificadas por um aplicativo. Essas fontes de dados são conhecidas como *provedores de configuração*. Como o cliente .NET Core da configuração de aplicativo é implementado como um provedor de configuração, o serviço aparece como outra fonte de dados.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [SDK .NET Core](https://dotnet.microsoft.com/download)

>[!TIP]
> O Azure Cloud Shell é um shell interativo gratuito que você pode usar para executar as instruções de linha de comando neste artigo.  Ele tem ferramentas comuns do Azure pré-instalados, incluindo o SDK do .NET Core. Se você estiver conectado à sua assinatura do Azure, inicie o [Azure cloud Shell](https://shell.azure.com) do Shell.Azure.com.  Você pode saber mais sobre Azure Cloud Shell [lendo nossa documentação](../cloud-shell/overview.md)

## <a name="create-an-app-configuration-store"></a>Criar um repositório de configurações de aplicativo

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione **Configuration Explorer** > **criar** para adicionar os seguintes pares de chave-valor:

    | Chave | Valor |
    |---|---|
    | TestApp:Settings:BackgroundColor | Branco |
    | TestApp: configurações: FontSize | 24 |
    | TestApp:Settings:FontColor | Preto |
    | TestApp: configurações: mensagem | Dados da configuração Azure App |

    Deixe **rótulo** e **tipo de conteúdo** vazio por enquanto.

## <a name="create-an-aspnet-core-web-app"></a>Criar uma aplicação Web ASP.NET Core

Use a [interface de linha de comando (CLI) do .NET Core](https://docs.microsoft.com/dotnet/core/tools/) para criar um novo projeto de aplicativo Web do MVC ASP.NET Core. O [Azure cloud Shell](https://shell.azure.com) fornece essas ferramentas para você.  Eles também estão disponíveis nas plataformas Windows, macOS e Linux.

1. Crie uma pasta nova para o projeto. Para este guia de início rápido, nomeie-o *TestAppConfig*.

1. Na nova pasta, execute o seguinte comando para criar um novo projeto de aplicativo Web ASP.NET Core MVC:

```dotnetcli
dotnet new mvc --no-https
```

## <a name="add-secret-manager"></a>Adicionar Gerenciador de segredo

Para usar o Gerenciador de segredo, adicione um elemento `UserSecretsId` ao seu arquivo *. csproj* .

Abra o arquivo *. csproj* . Adicione um elemento `UserSecretsId`, conforme mostrado aqui. Você pode usar o mesmo GUID ou pode substituir esse valor pelo seu próprio. Guarde o ficheiro.

> [!IMPORTANT]
> `CreateHostBuilder` substitui `CreateWebHostBuilder` no .NET Core 3,0.  Selecione a sintaxe correta com base em seu ambiente.

#### <a name="net-core-2xtabcore2x"></a>[.NET Core 2. x](#tab/core2x)

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

#### <a name="net-core-3xtabcore3x"></a>[.NET Core 3. x](#tab/core3x)

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    
    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

</Project>
```
---

Esta ferramenta armazena os dados confidenciais dos projetos de programação fora da árvore dos projetos. Esta abordagem ajuda a evitar a partilha acidental de segredos de aplicações no código fonte. Para obter mais informações sobre o Gerenciador de segredo, consulte [armazenamento seguro de segredos do aplicativo em desenvolvimento no ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/app-secrets)

## <a name="connect-to-an-app-configuration-store"></a>Conectar-se a um repositório de configuração de aplicativo

1. Adicione uma referência ao pacote `Microsoft.Azure.AppConfiguration.AspNetCore` NuGet executando o seguinte comando:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 3.0.0-preview-010560002-1165
    ```
1. Execute o seguinte comando para restaurar os pacotes para seu projeto:

    ```dotnetcli
    dotnet restore
    ```
1. Adicione um segredo denominado *ConnectionStrings: AppConfig* ao Gerenciador de segredo.

    Esse segredo contém a cadeia de conexão para acessar seu repositório de configuração de aplicativo. Substitua o valor no comando a seguir pela cadeia de conexão para o repositório de configuração do aplicativo.

    Este comando tem de ser executado no mesmo diretório que o ficheiro *.csproj*.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    > [!IMPORTANT]
    > Alguns shells truncarão a cadeia de conexão, a menos que seja colocado entre aspas. Verifique se a saída do comando `dotnet user-secrets` mostra toda a cadeia de conexão. Se não estiver, execute novamente o comando, colocando a cadeia de conexão entre aspas.

    O Gerenciador de segredo é usado apenas para testar o aplicativo Web localmente. Quando o aplicativo é implantado no [serviço Azure app](https://azure.microsoft.com/services/app-service/web), por exemplo, você usa a configuração de aplicativo **cadeias de conexão** no serviço de aplicativo em vez do Gerenciador de segredo para armazenar a cadeia de conexão.

    Acessar esse segredo usando a API de configuração. Dois-pontos (:) funciona no nome da configuração com a API de configuração em todas as plataformas com suporte. Consulte [configuração por ambiente](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

1. Abra *Program.cs*e adicione uma referência ao provedor de configuração do aplicativo .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Atualize o método de `CreateWebHostBuilder` para usar a configuração de aplicativo chamando o método `config.AddAzureAppConfiguration()`.
    
    > [!IMPORTANT]
    > `CreateHostBuilder` substitui `CreateWebHostBuilder` no .NET Core 3,0.  Selecione a sintaxe correta com base em seu ambiente.

    #### <a name="net-core-2xtabcore2x"></a>[.NET Core 2. x](#tab/core2x)

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

    #### <a name="net-core-3xtabcore3x"></a>[.NET Core 3. x](#tab/core3x)
    
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

1. Navegue até *<app root>/views/Home* e abra *index. cshtml*. Substitua seu conteúdo pelo código a seguir:

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

1. Navegue até *<app root>/views/Shared* e abra *_Layout. cshtml*. Substitua seu conteúdo pelo código a seguir:

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

1. Para compilar o aplicativo usando o CLI do .NET Core, navegue até o diretório raiz do seu aplicativo e execute o seguinte comando no Shell de comando:

    ```dotnetcli
    dotnet build
    ```

1. Depois que a compilação for concluída com êxito, execute o seguinte comando para executar o aplicativo Web localmente:

    ```dotnetcli
    dotnet run
    ```

1. Se você estiver trabalhando em seu computador local, use um navegador para navegar até `http://localhost:5000`. Esta é a URL padrão para o aplicativo Web hospedado localmente.  

Se estiver trabalhando na Azure Cloud Shell, selecione o botão *Visualização da Web* seguido por *Configurar*.  

![Localizar o botão de visualização da Web](./media/quickstarts/cloud-shell-web-preview.png)

Quando for solicitado a configurar a porta para visualização, insira ' 5000 ' e selecione *abrir e procurar*.  A página da Web lerá "dados da configuração do Azure App".

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou um novo repositório de configuração de aplicativo e o utilizou com um aplicativo Web ASP.NET Core por meio do [provedor de configuração de aplicativo](https://go.microsoft.com/fwlink/?linkid=2074664). Para saber como configurar seu aplicativo ASP.NET Core para atualizar dinamicamente as definições de configuração, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Habilitar configuração dinâmica](./enable-dynamic-configuration-aspnet-core.md)

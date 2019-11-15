---
title: Início rápido para adição de sinalizadores de recurso a ASP.NET Core | Microsoft Docs
description: Um guia de início rápido para adicionar sinalizadores de recursos para ASP.NET Core aplicativos e gerenciá-los na configuração do Azure App
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET Core
ms.workload: tbd
ms.date: 04/19/2019
ms.author: yegu
ms.openlocfilehash: a203aa3a2df7e61c43f895849afa38b56ebea441
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091270"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Início rápido: Adicionar sinalizadores de recurso a um aplicativo ASP.NET Core

Neste guia de início rápido, você incorpora Azure App configuração em um aplicativo Web ASP.NET Core para criar uma implementação de ponta a ponta do gerenciamento de recursos. Você pode usar o serviço de configuração de aplicativo para armazenar centralmente todos os sinalizadores de recursos e controlar seus Estados. 

As bibliotecas de gerenciamento de recursos do .NET Core estendem a estrutura com suporte abrangente ao sinalizador de recursos. Essas bibliotecas são criadas sobre o sistema de configuração do .NET Core. Eles se integram perfeitamente com a configuração do aplicativo por meio de seu provedor de configuração do .NET Core.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [SDK do .NET Core](https://dotnet.microsoft.com/download).

## <a name="create-an-app-configuration-store"></a>Criar um repositório de configurações de aplicativo

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione **Gerenciador de recursos** >  **+ Adicionar** para adicionar os seguintes sinalizadores de recurso:

    | Chave | Estado |
    |---|---|
    | Beta | Desativado |

## <a name="create-an-aspnet-core-web-app"></a>Criar uma aplicação Web ASP.NET Core

Você usa a [CLI (interface de linha de comando) do .NET Core](https://docs.microsoft.com/dotnet/core/tools/) para criar um novo projeto de aplicativo web do ASP.NET Core MVC. A vantagem de usar o CLI do .NET Core em vez do Visual Studio é que o CLI do .NET Core está disponível nas plataformas Windows, macOS e Linux.

1. Crie uma pasta nova para o projeto. Para este guia de início rápido, nomeie-o *TestFeatureFlags*.

1. Na nova pasta, execute o seguinte comando para criar um novo projeto de aplicativo Web ASP.NET Core MVC:

   ```    
   dotnet new mvc --no-https
   ```

## <a name="add-secret-manager"></a>Adicionar Gerenciador de segredo

Adicione a [ferramenta Gerenciador de segredo](https://docs.microsoft.com/aspnet/core/security/app-secrets) ao seu projeto. A ferramenta Gerenciador de segredo armazena dados confidenciais para trabalho de desenvolvimento fora de sua árvore de projeto. Esta abordagem ajuda a evitar a partilha acidental de segredos de aplicações no código fonte.

1. Abra o arquivo *. csproj* .
1. Adicione um elemento `UserSecretsId`, conforme mostrado no exemplo a seguir, e substitua seu valor por seu próprio, que normalmente é um GUID:

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

1. Guarde o ficheiro.

## <a name="connect-to-an-app-configuration-store"></a>Conectar-se a um repositório de configuração de aplicativo

1. Adicione referência ao `Microsoft.Azure.AppConfiguration.AspNetCore` e aos pacotes NuGet `Microsoft.FeatureManagement.AspNetCore` executando os seguintes comandos:

    ```
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-009470001-12
    dotnet add package Microsoft.FeatureManagement.AspNetCore --version 1.0.0-preview-009000001-1251
    ```

1. Execute o seguinte comando para restaurar os pacotes para seu projeto:

    ```
    dotnet restore
    ```

1. Adicione um segredo denominado **ConnectionStrings: AppConfig** ao Gerenciador de segredo.

    Esse segredo contém a cadeia de conexão para acessar seu repositório de configuração de aplicativo. Substitua o valor `<your_connection_string>` no comando a seguir pela cadeia de conexão para o repositório de configuração do aplicativo.

    Este comando tem de ser executado no mesmo diretório que o ficheiro *.csproj*.

    ```
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    Você usa o Gerenciador de segredo somente para testar o aplicativo Web localmente. Ao implantar o aplicativo no [serviço Azure app](https://azure.microsoft.com/services/app-service), por exemplo, você usa uma configuração de aplicativo denominada **cadeias de conexão** no serviço de aplicativo em vez de usar o Gerenciador de segredo para armazenar a cadeia de conexão.

    Você pode acessar esse segredo com a API de configuração do aplicativo. Dois-pontos (:) funciona no nome da configuração com a API de configuração de aplicativo em todas as plataformas com suporte. Consulte [configuração por ambiente](https://docs.microsoft.com/aspnet/core/fundamentals/configuration).

1. Abra *Program.cs*e adicione uma referência ao provedor de configuração do aplicativo .NET Core:

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Atualize o método de `CreateWebHostBuilder` para usar a configuração de aplicativo chamando o método `config.AddAzureAppConfiguration()`.
    
    > [!IMPORTANT]
    > `CreateHostBuilder` substitui `CreateWebHostBuilder` no .NET Core 3,0.  Selecione a sintaxe correta com base em seu ambiente.

    ### <a name="update-createwebhostbuilder-for-net-core-2x"></a>Atualizar `CreateWebHostBuilder` para .NET Core 2. x

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .UseFeatureFlags();
                });
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
            config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .UseFeatureFlags();
            });

        })
        .UseStartup<Startup>());
    ```


1. Abra *Startup.cs*e adicione referências ao Gerenciador de recursos do .NET Core:

    ```csharp
    using Microsoft.FeatureManagement;
    ```

1. Atualize o método de `ConfigureServices` para adicionar suporte ao sinalizador de recurso chamando o método `services.AddFeatureManagement()`. Opcionalmente, você pode incluir qualquer filtro a ser usado com sinalizadores de recurso chamando `services.AddFeatureFilter<FilterType>()`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
    ```

1. Atualize o método de `Configure` para adicionar um middleware para permitir que os valores de sinalizador de recurso sejam atualizados em um intervalo recorrente enquanto o aplicativo Web ASP.NET Core continua a receber solicitações.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();
        app.UseMvc();
    }
    ```

1. Adicionar um arquivo *MyFeatureFlags.cs* :

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. Adicione *BetaController.cs* ao diretório de *controladores* :

    ```csharp
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.FeatureManagement;
    using Microsoft.FeatureManagement.Mvc;

    namespace TestFeatureFlags.Controllers
    {
        public class BetaController: Controller
        {
            private readonly IFeatureManager _featureManager;

            public BetaController(IFeatureManagerSnapshot featureManager)
            {
                _featureManager = featureManager;
            }

            [FeatureGate(MyFeatureFlags.Beta)]
            public IActionResult Index()
            {
                return View();
            }
        }
    }
    ```

1. Abra *_ViewImports. cshtml* no diretório *views* e adicione o auxiliar de marca do Gerenciador de recursos:

    ```html
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

1. Abra *_Layout. cshtml* nas *exibições*\\diretório *compartilhado* e substitua o código de barra de `<nav>` em `<body>` > `<header>` pelo seguinte código:

    ```html
    <nav class="navbar navbar-expand-sm navbar-toggleable-sm navbar-light bg-white border-bottom box-shadow mb-3">
        <div class="container">
            <a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">TestFeatureFlags</a>
            <button class="navbar-toggler" type="button" data-toggle="collapse" data-target=".navbar-collapse" aria-controls="navbarSupportedContent"
            aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
            </button>
            <div class="navbar-collapse collapse d-sm-inline-flex flex-sm-row-reverse">
                <ul class="navbar-nav flex-grow-1">
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Index">Home</a>
                    </li>
                    <feature name="Beta">
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Beta" asp-action="Index">Beta</a>
                    </li>
                    </feature>
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Privacy">Privacy</a>
                    </li>
                </ul>
            </div>
        </div>
    </nav>
    ```

1. Crie um diretório *beta* em *exibições* e adicione *index. cshtml* a ele:

    ```html
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>
        This is the beta website.
    </h1>
    ```

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Para compilar o aplicativo usando o CLI do .NET Core, execute o seguinte comando no Shell de comando:

    ```
    dotnet build
    ```

1. Depois que a compilação for concluída com êxito, execute o seguinte comando para executar o aplicativo Web localmente:

    ```
    dotnet run
    ```

1. Abra uma janela do navegador e vá para `https://localhost:5001`, que é a URL padrão para o aplicativo Web hospedado localmente.

    ![Local de inicialização do aplicativo de início rápido](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **todos os recursos**e selecione a instância do repositório de configuração de aplicativo que você criou no guia de início rápido.

1. Selecione **Gerenciador de recursos**e altere o estado da chave **beta** para **ativado**:

    | Chave | Estado |
    |---|---|
    | Beta | Ativado |

1. Reinicie o aplicativo alternando de volta para o prompt de comando e pressionando `Ctrl-C` para cancelar o processo de `dotnet` em execução e, em seguida, executar novamente `dotnet run`.

1. Atualize a página do navegador para ver as novas definições de configuração.

    ![Local de inicialização do aplicativo de início rápido](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, você criou um novo repositório de configuração de aplicativo e o utilizou para gerenciar recursos em um aplicativo Web ASP.NET Core por meio das [bibliotecas de gerenciamento de recursos](https://go.microsoft.com/fwlink/?linkid=2074664).

- Saiba mais sobre o [Gerenciamento de recursos](./concept-feature-management.md).
- [Gerenciar sinalizadores de recurso](./manage-feature-flags.md).
- [Use os sinalizadores de recurso em um aplicativo ASP.NET Core](./use-feature-flags-dotnet-core.md).
- [Usar a configuração dinâmica em um aplicativo ASP.NET Core](./enable-dynamic-configuration-aspnet-core.md)

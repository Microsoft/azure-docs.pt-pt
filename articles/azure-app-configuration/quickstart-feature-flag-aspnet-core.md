---
title: Introdução rápida para adicionar os sinalizadores de recurso para ASP.NET Core | Documentos da Microsoft
description: Um guia de introdução para adicionar os sinalizadores de recurso para aplicações ASP.NET Core e gerenciá-las na configuração de aplicações do Azure
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
ms.openlocfilehash: bd812ad1194f88b14d88f067583ca6eee4bb0c74
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274219"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Início rápido: Adicionar sinalizadores de recurso para uma aplicação ASP.NET Core

Pode ativar a gestão de recursos em ASP.NET Core ao ligar-se a sua aplicação para configuração de aplicações do Azure. Pode utilizar este serviço gerido para armazenar todos os sinalizadores de recurso e controlar centralmente seus Estados. Este início rápido mostra como incorporar a configuração de aplicações num ASP.NET Core, aplicação web para criar uma implementação de ponto-a-ponto de gestão de recursos.

As bibliotecas de gestão de recursos do .NET Core ampliar a estrutura com suporte do sinalizador de funcionalidade abrangente. Essas bibliotecas são criadas sobre o sistema de configuração do .NET Core. Eles integram facilmente com a configuração de aplicações através do seu fornecedor de configuração do .NET Core.

Pode utilizar qualquer editor de código para realizar os passos neste guia de introdução. [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção disponível no Windows, macOS e plataformas Linux.

## <a name="prerequisites"></a>Pré-requisitos

Para fazer este início rápido, instale o [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Criar um arquivo de configuração de aplicações

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione **Gestor de recursos** >  **+ criar** para adicionar os seguintes sinalizadores de recurso:

    | Chave | Estado |
    |---|---|
    | Beta | Desativado |

## <a name="create-an-aspnet-core-web-app"></a>Criar uma aplicação Web ASP.NET Core

Utilizar o [.NET Core interface de linha de comandos (CLI)](https://docs.microsoft.com/dotnet/core/tools/) para criar um novo projeto de aplicação web de MVC do ASP.NET Core. A vantagem de utilizar a CLI do .NET Core em vez do Visual Studio é que a CLI do .NET Core está disponível para todas as plataformas de Linux, macOS e Windows.

1. Crie uma pasta nova para o projeto. Neste início rápido, um nome *TestFeatureFlags*.

1. Na nova pasta, execute o seguinte comando para criar um novo projeto de aplicação web de MVC do ASP.NET Core:

   ```    
   dotnet new mvc
   ```

## <a name="add-secret-manager"></a>Adicionar Gestor de segredo

Adicionar a [ferramenta Gerenciador de segredo](https://docs.microsoft.com/aspnet/core/security/app-secrets) ao seu projeto. A ferramenta Gerenciador de segredo armazena dados confidenciais para projetos de programação fora da sua árvore de projeto. Esta abordagem ajuda a evitar a partilha acidental de segredos de aplicações no código fonte.

1. Abra o *. csproj* ficheiro.
1. Adicionar um `UserSecretsId` elemento, conforme mostrado no exemplo a seguir e substitua o respetivo valor com os seus próprios, que é normalmente um GUID:

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

## <a name="connect-to-an-app-configuration-store"></a>Ligar a um arquivo de configuração de aplicações

1. Adicionar referências para o `Microsoft.Extensions.Configuration.AzureAppConfiguration` e `Microsoft.FeatureManagement` pacotes de NuGet ao executar os comandos seguintes:

    ```
    dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration --version 1.0.0-preview-008920001-990

    dotnet add package Microsoft.FeatureManagement.AspNetCore --version 1.0.0-preview-009000001-1251
    ```

1. Execute o seguinte comando para restaurar pacotes para o seu projeto:

    ```
    dotnet restore
    ```

1. Adicionar um segredo com o nome **ConnectionStrings:AppConfig** ao Gestor de segredo.

    Este segredo contém a cadeia de ligação para aceder ao seu arquivo de configuração de aplicações. Substitua o `<your_connection_string>` valor no comando seguinte com a cadeia de ligação para seu armazenamento de configuração de aplicações.

    Este comando tem de ser executado no mesmo diretório que o ficheiro *.csproj*.

    ```
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    Utilize o Gestor de segredo apenas para testar a aplicação web localmente. Ao implementar a aplicação [App Service do Azure](https://azure.microsoft.com/services/app-service), por exemplo, utilizar uma definição com o nome de aplicação **cadeias de ligação** no serviço de aplicações em vez de utilizar o Gestor de segredo para armazenar a cadeia de ligação.

    Pode aceder a este segredo com a API de configuração de aplicação. Dois pontos (:) funciona no nome da configuração com a API de configuração de aplicação em todas as plataformas suportadas. Ver [configuração pelo ambiente](https://docs.microsoft.com/aspnet/core/fundamentals/configuration).

1. Open *Program.cs*e adicione uma referência para o fornecedor de configuração de aplicações do .NET Core:

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Atualização do `CreateWebHostBuilder` método a utilizar a configuração de aplicações ao chamar o `config.AddAzureAppConfiguration()` método.

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

1. Open *Startup.cs*e adicione referências para o Gestor de recursos do .NET Core:

    ```csharp
    using Microsoft.FeatureManagement.AspNetCore;
    ```

1. Atualização do `ConfigureServices` método para adicionar o sinalizador de funcionalidade oferecer suporte ao chamar o `services.AddFeatureManagement()` método. Opcionalmente, pode incluir qualquer filtro a ser utilizado com os sinalizadores de recurso ao chamar `services.AddFeatureFilter<FilterType>()`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
    ```

1. Adicionar uma *MyFeatureFlags.cs* ficheiro:

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. Adicione *BetaController.cs* para o *controladores* diretório:

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

1. Open *_ViewImports.cshtml* no *vistas* directory, e adicione o auxiliar de marca de Gestor de recursos:

    ```html
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

1. Open *layout. cshtml* no *vistas*\\*partilhado* diretório e substitua o `<nav>` código de barras em `<body>`  >  `<header>` com o código a seguir:

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

1. Criar uma *Beta* diretório sob *vistas* e adicione *Index. cshtml* a ele:

    ```html
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>
        This is the beta website.
    </h1>
    ```

## <a name="build-and-run-the-app-locally"></a>Criar e executar a aplicação localmente

1. Para criar a aplicação com a CLI do .NET Core, execute o seguinte comando na shell de comandos:

    ```
    dotnet build
    ```

1. Após a compilação for concluída com êxito, execute o seguinte comando para executar a aplicação web localmente:

    ```
    dotnet run
    ```

1. Abra uma janela do browser e aceda a `https://localhost:5001`, que é o URL predefinido da aplicação web alojada localmente.

    ![Início rápido iniciação da aplicação local](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **todos os recursos**e selecione a instância do arquivo de configuração de aplicações que criou no guia de introdução.

1. Selecione **Gestor de recursos**e altere o estado do **Beta** chave **no**:

    | Chave | Estado |
    |---|---|
    | Beta | Ativado |

1. Atualize a página do browser para ver as novas definições de configuração.

    ![Início rápido iniciação da aplicação local](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou um novo arquivo de configuração de aplicações e utilizá-la para gerir recursos numa aplicação web ASP.NET Core através da [bibliotecas de gestão de recursos](https://go.microsoft.com/fwlink/?linkid=2074664).

- Saiba mais sobre [gestão de recursos](./concept-feature-management.md).
- [Gerir sinalizadores de recurso](./manage-feature-flags.md).
- [Utilizar os sinalizadores de recurso numa aplicação ASP.NET Core](./use-feature-flags-dotnet-core.md).

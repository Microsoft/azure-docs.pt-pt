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
ms.openlocfilehash: 95f702b1d85dc8fe22b1800df3f7b0ebc987bee5
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65412386"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Início rápido: Adicionar sinalizadores de recurso para uma aplicação ASP.NET Core

Gestão de recursos em ASP.NET Core pode ser ativado ao ligar-se a sua aplicação para configuração de aplicações do Azure. Pode utilizar este serviço gerido para armazenar todos os sinalizadores de recurso e controlar centralmente seus Estados. Este guia de introdução mostra-lhe como incorporar o serviço numa aplicação web ASP.NET Core para criar uma implementação de ponto-a-ponto de gestão de recursos.

As bibliotecas de gestão de recursos do .NET Core ampliar a estrutura com suporte do sinalizador de funcionalidade abrangente. Eles são criados sobre o sistema de configuração do .NET Core. Eles integram facilmente com a configuração de aplicações através do seu fornecedor de configuração do .NET Core.

Pode utilizar qualquer editor de código para realizar os passos neste guia de introdução. [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção disponível no Windows, macOS e plataformas Linux.

## <a name="prerequisites"></a>Pré-requisitos

Para fazer este início rápido, instale o [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Criar um arquivo de configuração de aplicação

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione **Gestor de recursos** > **+ criar** para adicionar os seguintes sinalizadores de recurso:

    | Chave | Estado |
    |---|---|
    | Beta | Desativada |

## <a name="create-an-aspnet-core-web-app"></a>Criar uma aplicação Web ASP.NET Core

Utilizar o [.NET Core interface de linha de comandos (CLI)](https://docs.microsoft.com/dotnet/core/tools/) para criar um novo projeto de aplicação web de MVC do ASP.NET Core. A vantagem de utilizar a CLI do .NET Core ao longo do Visual Studio é que ele está disponível para todas as plataformas de Linux, macOS e Windows.

1. Crie uma pasta nova para o projeto. Neste início rápido, um nome *TestFeatureFlags*.

2. Na nova pasta, execute o seguinte comando para criar um novo projeto de aplicação web de MVC do ASP.NET Core:

        dotnet new mvc

## <a name="add-secret-manager"></a>Adicionar Gestor de segredo

Adicionar a [ferramenta Gerenciador de segredo](https://docs.microsoft.com/aspnet/core/security/app-secrets) ao seu projeto. Esta ferramenta armazena os dados confidenciais dos projetos de programação fora da árvore dos projetos. Esta abordagem ajuda a evitar a partilha acidental de segredos de aplicações no código fonte.

- Abra o *. csproj* ficheiro. Adicionar um `UserSecretsId` elemento, conforme mostrado aqui e substitua o respetivo valor com os seus próprios, que é normalmente um GUID. Guarde o ficheiro.

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

1. Adicionar referências para o `Microsoft.Extensions.Configuration.AzureAppConfiguration` e `Microsoft.FeatureManagement` pacotes de NuGet ao executar os comandos seguintes:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration --version 1.0.0-preview-008520001

        dotnet add package Microsoft.FeatureManagement.AspNetCore --version 1.0.0-preview-008560001-910

2. Execute o seguinte comando para restaurar pacotes para o seu projeto:

        dotnet restore

3. Adicionar um segredo com o nome *ConnectionStrings:AppConfig* ao Gestor de segredo.

    Este segredo contém a cadeia de ligação para aceder ao seu arquivo de configuração de aplicação. Substitua o valor no comando seguinte a cadeia de ligação do seu arquivo de configuração de aplicação.

    Este comando tem de ser executado no mesmo diretório que o ficheiro *.csproj*.

        dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>

    Segredo Manager é utilizada apenas para testar a aplicação web localmente. Quando a aplicação é implementada [App Service do Azure](https://azure.microsoft.com/services/app-service/web), por exemplo, utilizar uma definição da aplicação **cadeias de ligação** no serviço de aplicações em vez de com o Gestor de segredo para armazenar a cadeia de ligação.

    Este segredo é acessado com a API de configuração. Dois pontos (:) funciona no nome da configuração com a API de configuração nas plataformas suportadas. Ver [configuração pelo ambiente](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

4. Open *Program.cs*e adicione uma referência para o fornecedor de configuração de aplicações do .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

5. Atualização do `CreateWebHostBuilder` método a utilizar a configuração de aplicações ao chamar o `config.AddAzureAppConfiguration()` método.

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

6. Open *Startup.cs*e adicione referências para o Gestor de recursos do .NET Core.

    ```csharp
    using Microsoft.FeatureManagement.AspNetCore;
    ```

7. Atualização do `ConfigureServices` método para adicionar o sinalizador de funcionalidade oferecer suporte ao chamar o `services.AddFeatureManagement()` método e, opcionalmente, inclua qualquer filtro a ser utilizado com os sinalizadores de recurso ao chamar `services.AddFeatureFilter<FilterType>()`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
    ```

8. Adicionar uma *MyFeatureFlags.cs* ficheiro.

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

9. Adicione *BetaController.cs* para o diretório de controladores:

    ```csharp
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.FeatureManagement.AspNetCore;

    namespace TestFeatureFlags.Controllers
    {
        public class BetaController: Controller
        {
            private readonly IFeatureManager _featureManager;

            public BetaController(IFeatureManagerSnapshot featureManager)
            {
                _featureManager = featureManager;
            }

            [Feature(MyFeatureFlags.Beta)]
            public IActionResult Index()
            {
                return View();
            }
        }
    }
    ```

10. Open *_ViewImports.cshtml* no diretório Views, e adicione o auxiliar de marca de Gestor de recursos:

    ```html
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

11. Open *layout. cshtml* nas vistas > diretório partilhado e substituir de forma a `<nav>` barra sob `<body>`  >  `<header>` com o código a seguir:

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

12. Crie um diretório de Beta sob Views e adicione *Index. cshtml* a ele:

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

        dotnet build

2. Após a compilação for concluída com êxito, execute o seguinte comando para executar a aplicação web localmente:

        dotnet run

3. Abra uma janela do browser e aceda a `https://localhost:5001`, que é o URL predefinido da aplicação web alojada localmente.

    ![Início rápido iniciação da aplicação local](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

4. Inicie sessão no [portal do Azure](https://aka.ms/azconfig/portal). Selecione **todos os recursos**e selecione a instância de arquivo de configuração de aplicação que criou no guia de introdução.

5. Selecione **Gestor de recursos**e altere o valor de *Beta* para *no*:

    | Chave | Estado |
    |---|---|
    | Beta | Ativa |

6. Atualize a página do browser para ver as novas definições de configuração.

    ![Início rápido iniciação da aplicação local](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou um novo arquivo de configuração de aplicação e utilizado para gerir recursos numa aplicação web ASP.NET Core através da [bibliotecas de gestão de recursos](https://go.microsoft.com/fwlink/?linkid=2074664).

* Saiba mais sobre [gestão de recursos](./concept-feature-management.md)
* [Gerir sinalizadores de funcionalidades](./manage-feature-flags.md)
* [Utilizar os sinalizadores de recurso numa aplicação ASP.NET Core](./use-feature-flags-dotnet-core.md)

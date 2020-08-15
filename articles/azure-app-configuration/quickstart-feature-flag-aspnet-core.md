---
title: Quickstart para adicionar bandeiras de recurso ao ASP.NET Core
description: Adicione bandeiras de funcionalidades para ASP.NET aplicações Core e geri-las usando a Configuração de Aplicações Azure
author: lisaguthrie
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 01/14/2020
ms.author: lcozzens
ms.openlocfilehash: 12b66dc173a8d3f93f97fb369ce03533299a65d7
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235269"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Quickstart: Adicione bandeiras de recurso a uma aplicação core ASP.NET

Neste quickstart, cria-se uma implementação de ponta a ponta da gestão de funcionalidades numa aplicação core ASP.NET utilizando a Configuração da App Azure. Utilizará o serviço de Configuração de Aplicações para armazenar centralmente todas as suas bandeiras de funcionalidades e controlar os seus estados. 

As bibliotecas de Gestão de Recursos Centrais .NET alargam o quadro com suporte abrangente de bandeira de recurso. Estas bibliotecas são construídas em cima do sistema de configuração .NET Core. Integram-se perfeitamente com a Configuração de Aplicações através do seu fornecedor de configuração .NET Core.

## <a name="prerequisites"></a>Pré-requisitos

- Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download).

## <a name="create-an-app-configuration-store"></a>Criar uma loja de configuração de aplicativos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione **Feature Manager**  >  **+Adicione** para adicionar uma bandeira de recurso chamada `Beta` .

    > [!div class="mx-imgBorder"]
    > ![Ativar a bandeira de recurso chamada Beta](media/add-beta-feature-flag.png)

    Deixe `label` indefinida por enquanto. **Selecione Aplicar** para guardar a nova bandeira de funcionalidade.

## <a name="create-an-aspnet-core-web-app"></a>Criar uma aplicação Web ASP.NET Core

Utilize a [interface de linha de comando .NET Core (CLI)](https://docs.microsoft.com/dotnet/core/tools/) para criar um novo projeto de aplicação web Core MVC ASP.NET. A vantagem de utilizar o .NET Core CLI em vez do Visual Studio é que o CLI .NET Core está disponível nas plataformas Windows, macOS e Linux.

1. Crie uma pasta nova para o projeto. Para este arranque rápido, nomeie-o *TestFeatureFlags*.

1. Na nova pasta, executar o seguinte comando para criar um novo projeto de aplicação web Core MVC ASP.NET:

   ```    
   dotnet new mvc --no-https
   ```

## <a name="add-secret-manager"></a>Adicionar Gerente Secreto

Para utilizar o Gestor Secreto, adicione um `UserSecretsId` elemento ao seu ficheiro *.csproj.*

1. Abra o ficheiro *.csproj.*

1.  Adicione um `UserSecretsId` elemento como mostrado aqui. Pode usar o mesmo GUID, ou pode substituir este valor pelo seu.

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

1. Guarde o ficheiro *.csproj.*

Esta ferramenta armazena os dados confidenciais dos projetos de programação fora da árvore dos projetos. Esta abordagem ajuda a evitar a partilha acidental de segredos de aplicações no código fonte.

> [!TIP]
> Para saber mais sobre o Secret Manager, consulte [o armazenamento seguro de segredos de aplicações em desenvolvimento em ASP.NET Core.](https://docs.microsoft.com/aspnet/core/security/app-secrets)

## <a name="connect-to-an-app-configuration-store"></a>Conecte-se a uma loja de configuração de aplicativos

1. Adicione referência aos `Microsoft.Azure.AppConfiguration.AspNetCore` pacotes e aos `Microsoft.FeatureManagement.AspNetCore` NuGet executando os seguintes comandos:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    dotnet add package Microsoft.FeatureManagement.AspNetCore
    ```

1. Executar o seguinte comando para restaurar pacotes para o seu projeto:

    ```dotnetcli
    dotnet restore
    ```

1. Adicione um segredo chamado **ConnectionStrings:AppConfig** ao Gestor Secreto.

    Este segredo contém o fio de ligação para aceder à sua loja de Configuração de Aplicações. Substitua o `<your_connection_string>` valor no seguinte comando com o fio de ligação para a sua loja de Configuração de Aplicações. Pode encontrar a cadeia de ligação principal apenas de leitura sob **as teclas de acesso** no portal Azure.

    Este comando tem de ser executado no mesmo diretório que o ficheiro *.csproj*.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    Você usa Secret Manager apenas para testar a aplicação web localmente. Quando implementa a aplicação para o [Azure App Service](https://azure.microsoft.com/services/app-service), por exemplo, utiliza uma definição de aplicação chamada **Connection Strings** in App Service em vez de utilizar o Secret Manager para armazenar a cadeia de ligação.

    Pode aceder a este segredo com a API de Configuração de Aplicações. Um cólon (:) funciona no nome de configuração com a API de Configuração de Aplicações em todas as plataformas suportadas. Ver [Configuração por ambiente](https://docs.microsoft.com/aspnet/core/fundamentals/configuration).

1. Em *Program.cs,* atualize o `CreateWebHostBuilder` método para utilizar a Configuração da Aplicação, chamando o `config.AddAzureAppConfiguration()` método.

    > [!IMPORTANT]
    > `CreateHostBuilder` substitui `CreateWebHostBuilder` em .NET Core 3.0.  Selecione a sintaxe correta com base no seu ambiente.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

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

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

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
    ---

1. Abra *Startup.cs*, e adicione referências ao gestor de funcionalidades .NET Core:

    ```csharp
    using Microsoft.FeatureManagement;
    ```

1. Atualize o `ConfigureServices` método para adicionar suporte de bandeira de recurso chamando o `services.AddFeatureManagement()` método. Opcionalmente, pode incluir qualquer filtro a ser usado com bandeiras de características `services.AddFeatureFilter<FilterType>()` chamando:

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)
    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);        
        services.AddFeatureManagement();
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)
    ```csharp    
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllersWithViews();
        services.AddFeatureManagement();
    }

    ---

1. Update the `Configure` method to add a middleware to allow the feature flag values to be refreshed at a recurring interval while the ASP.NET Core web app continues to receive requests.

    #### [.NET Core 2.x](#tab/core2x)
    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
            }

            app.UseStaticFiles();
            app.UseCookiePolicy();
            app.UseAzureAppConfiguration();
            app.UseMvc(routes =>
            {
                routes.MapRoute(
                    name: "default",
                    template: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)
    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
            }
            app.UseStaticFiles();
            app.UseRouting();
            app.UseAuthorization();
            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
            app.UseAzureAppConfiguration();
    }
    ```
    ---

1. Adicione um ficheiro *MyFeatureFlags.cs:*

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. Adicione *BetaController.cs* ao *diretório de controladores:*

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

1. Abra *_ViewImports.cshtml* no diretório *views* e adicione o ajudante de etiquetas de recurso:

    ```html
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

1. Abra *_Layout.cshtml* no diretório *Views* \\ *Shared* e substitua o código de `<nav>` barras por baixo do seguinte `<body>`  >  `<header>` código:

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

1. Crie um *diretório Beta* em *Visualizações* e adicione *Index.cshtml:*

    ```html
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>
        This is the beta website.
    </h1>
    ```

## <a name="build-and-run-the-app-locally"></a>Construa e execute a app localmente

1. Para construir a aplicação utilizando o .NET Core CLI, executar o seguinte comando na concha de comando:

    ```
    dotnet build
    ```

1. Após a construção concluída com sucesso, executar o seguinte comando para executar a aplicação web localmente:

    ```
    dotnet run
    ```

1. Abra uma janela do navegador e vá para `https://localhost:5000` , que é o URL padrão para a aplicação web hospedada localmente.
    Se estiver a trabalhar no Azure Cloud Shell, selecione o botão *de pré-visualização web* seguido de *Configuração*.  Quando solicitado, selecione a porta 5000.

    ![Localizar o botão de pré-visualização web](./media/quickstarts/cloud-shell-web-preview.png)

    O seu navegador deve exibir uma página semelhante à imagem abaixo.
    ![Quickstart app lançar local](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Todos os recursos**e selecione a instância da loja de Configuração de Aplicações que criou no arranque rápido.

1. Selecione **'Feature Manager'** e altere o estado da tecla **Beta** para **On**.

1. Volte ao comando e cancele o processo de funcionamento `dotnet` premindo `Ctrl-C` .  Reinicie a sua aplicação utilizando `dotnet run` .

1. Refresque a página do navegador para ver as novas definições de configuração.

    ![Quickstart app lançar local](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou uma nova loja de Configuração de Aplicações e utilizou-a para gerir funcionalidades numa aplicação web core ASP.NET através das [bibliotecas de Gestão de Recursos.](https://go.microsoft.com/fwlink/?linkid=2074664)

- Saiba mais sobre [gestão de recursos.](./concept-feature-management.md)
- [Gerir bandeiras de características](./manage-feature-flags.md).
- [Utilize bandeiras de recurso numa aplicação core ASP.NET.](./use-feature-flags-dotnet-core.md)
- [Utilize a configuração dinâmica numa aplicação core ASP.NET](./enable-dynamic-configuration-aspnet-core.md)

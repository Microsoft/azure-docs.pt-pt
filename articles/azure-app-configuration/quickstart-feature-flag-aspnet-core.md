---
title: Quickstart para adicionar bandeiras de recurso ao ASP.NET Core
description: Adicione bandeiras de recurso para ASP.NET aplicações Core e gerencie-as usando a configuração da app Azure
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 01/14/2020
ms.author: lcozzens
ms.openlocfilehash: d8582dfc796fe3e87b8bdc5be763dddfb5d0176b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80245417"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Quickstart: Adicione bandeiras de recurso a uma aplicação ASP.NET Core

Neste arranque rápido, cria-se uma implementação final da gestão de funcionalidades numa aplicação ASP.NET Core utilizando a Configuração de Aplicações Azure. Utilizará o serviço de Configuração de Aplicações para armazenar centralmente todas as suas bandeiras de funcionalidades e controlar os seus estados. 

As bibliotecas .NET Core Feature Management alargam o quadro com um suporte abrangente de bandeira de características. Estas bibliotecas são construídas em cima do sistema de configuração .NET Core. Integram-se perfeitamente com a Configuração da App através do seu fornecedor de configuração .NET Core.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download).

## <a name="create-an-app-configuration-store"></a>Criar uma loja de configuração de aplicações

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione > Gestor de **Funcionalidades** `Beta`**+Adicione** para adicionar uma bandeira de recurso chamada .

    > [!div class="mx-imgBorder"]
    > ![Ativar a bandeira de recurso chamada Beta](media/add-beta-feature-flag.png)

    Deixe `label` indefinido por enquanto. Selecione **Aplicar** para salvar a nova bandeira de recurso.

## <a name="create-an-aspnet-core-web-app"></a>Criar uma aplicação Web ASP.NET Core

Utilize a [interface de linha de comando .NET Core (CLI)](https://docs.microsoft.com/dotnet/core/tools/) para criar um novo ASP.NET projeto de aplicação web Core MVC. A vantagem de utilizar o CLI .NET Core em vez do Visual Studio é que o CLI .NET Core está disponível nas plataformas Windows, macOS e Linux.

1. Crie uma pasta nova para o projeto. Para este arranque rápido, nomeie-o *TestFeatureFlags*.

1. Na nova pasta, execute o seguinte comando para criar um novo ASP.NET projeto de aplicação web Core MVC:

   ```    
   dotnet new mvc --no-https
   ```

## <a name="add-secret-manager"></a>Adicionar Gestor Secreto

Para utilizar o Secret `UserSecretsId` Manager, adicione um elemento ao seu ficheiro *.csproj.*

1. Abra o ficheiro *.csproj.*

1.  Adicione `UserSecretsId` um elemento como mostrado aqui. Pode utilizar o mesmo GUID, ou pode substituir este valor pelo seu.

    > [!IMPORTANT]
    > `CreateHostBuilder`substitui `CreateWebHostBuilder` em .NET Core 3.0.  Selecione a sintaxe correta com base no seu ambiente.

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
> Para saber mais sobre o Secret Manager, consulte [o armazenamento seguro de segredos de aplicações em desenvolvimento em ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/app-secrets).

## <a name="connect-to-an-app-configuration-store"></a>Ligar a uma loja de configuração de aplicações

1. Adicione referência `Microsoft.Azure.AppConfiguration.AspNetCore` aos `Microsoft.FeatureManagement.AspNetCore` pacotes NuGet e NuGet executando os seguintes comandos:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    dotnet add package Microsoft.FeatureManagement.AspNetCore
    ```

1. Execute o seguinte comando para restaurar as embalagens para o seu projeto:

    ```dotnetcli
    dotnet restore
    ```

1. Adicione um segredo chamado **ConnectionStrings:AppConfig** ao Secret Manager.

    Este segredo contém a cadeia de ligação para aceder à sua loja de configuração de aplicações. Substitua `<your_connection_string>` o valor no seguinte comando com a cadeia de ligação para a sua loja de configuração de aplicações. Pode encontrar a cadeia de ligação sob **teclas** de acesso no portal Azure.

    Este comando tem de ser executado no mesmo diretório que o ficheiro *.csproj*.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    Usa o Secret Manager apenas para testar a aplicação web localmente. Quando implementa a aplicação para o [Azure App Service](https://azure.microsoft.com/services/app-service), por exemplo, utiliza uma definição de aplicação chamada **Connection Strings** no Serviço de Aplicações em vez de utilizar o Secret Manager para armazenar a cadeia de ligação.

    Pode aceder a este segredo com a API de Configuração de Aplicações. Um cólon (:) trabalha no nome de configuração com a API de Configuração de Aplicações em todas as plataformas suportadas. Ver [Configuração por ambiente.](https://docs.microsoft.com/aspnet/core/fundamentals/configuration)

1. Atualize `CreateWebHostBuilder` o método para utilizar `config.AddAzureAppConfiguration()` a Configuração da Aplicação, ligando para o método.
    
    > [!IMPORTANT]
    > `CreateHostBuilder`substitui `CreateWebHostBuilder` em .NET Core 3.0.  Selecione a sintaxe correta com base no seu ambiente.

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

1. Abra *Startup.cs*e adicione referências ao gestor de funcionalidades .NET Core:

    ```csharp
    using Microsoft.FeatureManagement;
    ```

1. Atualize `ConfigureServices` o método para adicionar `services.AddFeatureManagement()` suporte à bandeira de recurso, chamando o método. Opcionalmente, pode incluir qualquer filtro a ser `services.AddFeatureFilter<FilterType>()`utilizado com bandeiras de características, chamando:

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
    ```
    ---

1. Atualize `Configure` o método para adicionar um middleware para permitir que os valores da bandeira da funcionalidade sejam atualizados num intervalo recorrente, enquanto a aplicação web ASP.NET Core continua a receber pedidos.
    
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)
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

1. Adicione *BetaController.cs* ao diretório dos *Controladores:*

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

1. Abra *_ViewImports.cshtml* no diretório *Views* e adicione o ajudante de etiqueta de gestor de funcionalidades:

    ```html
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

1. Abra *_Layout.cshtml* no diretório*partilhado* *views*\\e `<body>`  >  `<header>` substitua o código de `<nav>` barras por baixo do seguinte código:

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

1. Crie um diretório *Beta* em *Views* e *adicione-lhe Index.cshtml:*

    ```html
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>
        This is the beta website.
    </h1>
    ```

## <a name="build-and-run-the-app-locally"></a>Construir e executar a app localmente

1. Para construir a aplicação utilizando o CLI .NET Core, execute o seguinte comando na concha de comando:

    ```
    dotnet build
    ```

1. Depois de a construção concluída com sucesso, execute o seguinte comando para executar a aplicação web localmente:

    ```
    dotnet run
    ```

1. Abra uma janela do `https://localhost:5000`navegador e vá para , que é o URL padrão para a aplicação web hospedada localmente.
    Se estiver a trabalhar na Casca de Nuvem Azure, selecione o botão *de pré-visualização web* seguido de *Configurar*.  Quando solicitado, selecione a porta 5000.

    ![Localizar o botão de pré-visualização da Web](./media/quickstarts/cloud-shell-web-preview.png)

    O seu navegador deve apresentar uma página semelhante à imagem abaixo.
    ![Lançamento de app Quickstart local](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **Todos os recursos**e selecione a instância da loja de configuração de aplicações que criou no arranque rápido.

1. Selecione Gestor de **Funcionalidades**e altere o estado da chave **Beta** para **On**.

1. Volte ao pedido de comando `dotnet` e `Ctrl-C`cancele o processo de funcionamento premindo .  Reiniciar a `dotnet run`sua aplicação utilizando .

1. Refresque a página do navegador para ver as novas configurações de configuração.

    ![Lançamento de app Quickstart local](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou uma nova loja de configuração de aplicações e utilizou-a para gerir funcionalidades numa ASP.NET aplicação web Core através das bibliotecas de Gestão de [Recursos.](https://go.microsoft.com/fwlink/?linkid=2074664)

- Saiba mais sobre gestão de [recursos.](./concept-feature-management.md)
- [Gerir as bandeiras de recurso.](./manage-feature-flags.md)
- [Utilize bandeiras de recurso numa aplicação ASP.NET Core](./use-feature-flags-dotnet-core.md).
- [Utilize a configuração dinâmica numa aplicação ASP.NET Core](./enable-dynamic-configuration-aspnet-core.md)

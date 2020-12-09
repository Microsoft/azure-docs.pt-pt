---
title: Quickstart para adicionar bandeiras de recurso ao ASP.NET Core
description: Adicione bandeiras de funcionalidades para ASP.NET aplicações Core e geri-las usando a Configuração de Aplicações Azure
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: d465f3c44ede8b4df56ef0da08c5bbbcd477d93f
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932158"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Quickstart: Adicione bandeiras de recurso a uma aplicação core ASP.NET

Neste quickstart, cria-se uma implementação de ponta a ponta da gestão de funcionalidades numa aplicação core ASP.NET utilizando a Configuração da App Azure. Utilizará o serviço de Configuração de Aplicações para armazenar centralmente todas as suas bandeiras de funcionalidades e controlar os seus estados. 

As bibliotecas de Gestão de Recursos Centrais .NET alargam o quadro com suporte abrangente de bandeira de recurso. Estas bibliotecas são construídas em cima do sistema de configuração .NET Core. Integram-se perfeitamente com a Configuração de Aplicações através do seu fornecedor de configuração .NET Core.

## <a name="prerequisites"></a>Pré-requisitos

* Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/dotnet)
* [SDK .NET Core](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Criar uma loja de configuração de aplicativos

[!INCLUDE[Azure App Configuration resource creation steps](../../includes/azure-app-configuration-create.md)]

8. Selecione **Operations**  >  **Gestor de Funcionalidades de** Operações  >  **Adicione** para adicionar uma bandeira de recurso chamada *Beta*.

    > [!div class="mx-imgBorder"]
    > ![Ativar a bandeira de recurso chamada Beta](media/add-beta-feature-flag.png)

    Deixe **a etiqueta** vazia por enquanto. **Selecione Aplicar** para guardar a nova bandeira de funcionalidade.

## <a name="create-an-aspnet-core-web-app"></a>Criar uma aplicação Web ASP.NET Core

Utilize a [interface de linha de comando .NET Core (CLI)](/dotnet/core/tools) para criar um novo projeto core MVC ASP.NET. A vantagem de utilizar o .NET Core CLI em vez do Visual Studio é que o CLI .NET Core está disponível nas plataformas Windows, macOS e Linux.

Executar o seguinte comando para criar um projeto core MVC ASP.NET numa nova pasta *TestFeatureFlags:*

```dotnetcli
dotnet new mvc --no-https --output TestFeatureFlags
```

[!INCLUDE[Add Secret Manager support to an ASP.NET Core project](../../includes/azure-app-configuration-add-secret-manager.md)]

## <a name="connect-to-an-app-configuration-store"></a>Conecte-se a uma loja de configuração de aplicativos

1. Instale os pacotes [Microsoft.Azure.AppConfiguration.AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) e [Microsoft.FeatureManagement.AspNetCore](https://www.nuget.org/packages/Microsoft.FeatureManagement.AspNetCore) NuGet executando os seguintes comandos:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

    ```dotnetcli
    dotnet add package Microsoft.FeatureManagement.AspNetCore
    ```

1. Executar o seguinte comando no mesmo diretório que o ficheiro *.csproj.* O comando utiliza o Secret Manager para armazenar um segredo chamado `ConnectionStrings:AppConfig` , que armazena a cadeia de conexão para a sua loja de Configuração de Aplicações. Substitua o `<your_connection_string>` espaço reservado pela cadeia de ligação da sua loja de configuração de aplicações. Pode encontrar a cadeia de ligação sob **as teclas de acesso** no portal Azure.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig "<your_connection_string>"
    ```

    O Secret Manager é usado apenas para testar a aplicação web localmente. Quando a aplicação for implantada no [Azure App Service,](https://azure.microsoft.com/services/app-service/web)utilize a definição da aplicação **Connection Strings** no Serviço de Aplicações em vez de Secret Manager para armazenar a cadeia de ligação.

    Aceda a este segredo utilizando a API de Configuração de Núcleo .NET. Um cólon `:` () funciona no nome de configuração com a API de configuração em todas as plataformas suportadas. Para obter mais informações, consulte [as teclas e valores de Configuração](/aspnet/core/fundamentals/configuration#configuration-keys-and-values).

1. Em *Program.cs,* atualize o `CreateWebHostBuilder` método para utilizar a Configuração da Aplicação, chamando o `AddAzureAppConfiguration` método.

    > [!IMPORTANT]
    > `CreateHostBuilder` substitui `CreateWebHostBuilder` em .NET Core 3.x. Selecione a sintaxe correta com base no seu ambiente.

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    var connection = settings.GetConnectionString("AppConfig");
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(connection).UseFeatureFlags());
                }).UseStartup<Startup>());
    ```

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration(config =>
               {
                   var settings = config.Build();
                   var connection = settings.GetConnectionString("AppConfig");
                   config.AddAzureAppConfiguration(options =>
                       options.Connect(connection).UseFeatureFlags());
               }).UseStartup<Startup>();
    ```

    ---

    Com a alteração anterior, o [fornecedor de configuração para configuração de aplicações](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration) foi registado na API de Configuração de Núcleo .NET.

1. Em *Startup.cs*, adicione uma referência ao gestor de recurso .NET Core:

    ```csharp
    using Microsoft.FeatureManagement;
    ```

1. Atualize o `Startup.ConfigureServices` método para adicionar suporte de bandeira de recurso chamando o `AddFeatureManagement` método. Opcionalmente, pode incluir qualquer filtro a ser usado com bandeiras de características `AddFeatureFilter<FilterType>()` chamando:

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp    
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllersWithViews();
        services.AddFeatureManagement();
    }
    ```

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc()
            .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
        services.AddFeatureManagement();
    }
    ```

    ---

1. Adicione um ficheiro *MyFeatureFlags.cs* ao diretório do projeto raiz com o seguinte código:

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. Adicione um ficheiro *BetaController.cs* ao *diretório dos Controladores* com o seguinte código:

    ```csharp
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.FeatureManagement;
    using Microsoft.FeatureManagement.Mvc;

    namespace TestFeatureFlags.Controllers
    {
        public class BetaController: Controller
        {
            private readonly IFeatureManager _featureManager;

            public BetaController(IFeatureManagerSnapshot featureManager) =>
                _featureManager = featureManager;

            [FeatureGate(MyFeatureFlags.Beta)]
            public IActionResult Index() => View();
        }
    }
    ```

1. Em *Views/_ViewImports.cshtml,* registe o gestor de funcionalidades Tag Helper utilizando uma `@addTagHelper` diretiva:

    ```cshtml
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

    O código anterior permite que o `<feature>` Tag Helper seja utilizado nos *ficheiros .cshtml* do projeto.

1. Abra *_Layout.cshtml* no *diretório views* \\ *shared.* Localize o `<nav>` código de barras em `<body>`  >  `<header>` . Insira uma nova `<feature>` etiqueta entre os itens de navegação *Home* e *Privacy,* como mostram as linhas abaixo destacadas.

    :::code language="html" source="../../includes/azure-app-configuration-navbar.md" range="15-38" highlight="13-17":::

1. Criar um *diretório De Pontos/Beta* e um ficheiro *Index.cshtml* contendo a seguinte marcação:

    ```cshtml
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>This is the beta website.</h1>
    ```

## <a name="build-and-run-the-app-locally"></a>Construa e execute a app localmente

1. Para construir a aplicação utilizando o .NET Core CLI, executar o seguinte comando na concha de comando:

    ```dotnetcli
    dotnet build
    ```

1. Após a construção concluída com sucesso, executar o seguinte comando para executar a aplicação web localmente:

    ```dotnetcli
    dotnet run
    ```

1. Abra uma janela do navegador e vá para `http://localhost:5000` , que é o URL padrão para a aplicação web hospedada localmente. Se estiver a trabalhar no Azure Cloud Shell, selecione o botão **de pré-visualização web** seguido de **Configuração**. Quando solicitado, selecione a porta 5000.

    ![Localizar o botão de pré-visualização web](./media/quickstarts/cloud-shell-web-preview.png)

    O seu navegador deve exibir uma página semelhante à imagem abaixo.

    :::image type="content" source="media/quickstarts/aspnet-core-feature-flag-local-before.png" alt-text="App local de arranque rápido antes de mudar" border="true":::

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Todos os recursos** e selecione a instância da loja de Configuração de Aplicações que criou no arranque rápido.

1. Selecione **O Gestor de Recursos**. 

1. Ativar a bandeira *Beta* selecionando a caixa de verificação em **Enabled**.

1. Volte para a concha de comando. Cancelar o processo de funcionamento `dotnet` pressionando <kbd>ctrl+C</kbd>. Reinicie a sua aplicação utilizando `dotnet run` .

1. Refresque a página do navegador para ver as novas definições de configuração.

    :::image type="content" source="media/quickstarts/aspnet-core-feature-flag-local-after.png" alt-text="App local quickstart após mudança" border="true":::

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE[Azure App Configuration cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou uma nova loja de Configuração de Aplicações e utilizou-a para gerir funcionalidades numa aplicação web core ASP.NET através das [bibliotecas de Gestão de Recursos.](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration)

* Saiba mais sobre [gestão de recursos.](./concept-feature-management.md)
* [Gerir bandeiras de características](./manage-feature-flags.md).
* [Utilize bandeiras de recurso numa aplicação core ASP.NET.](./use-feature-flags-dotnet-core.md)
* [Utilize a configuração dinâmica numa aplicação core ASP.NET](./enable-dynamic-configuration-aspnet-core.md)
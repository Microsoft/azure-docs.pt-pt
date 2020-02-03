---
title: 'Tutorial: usar a configuração dinâmica de configuração de aplicativo no ASP.NET Core'
titleSuffix: Azure App Configuration
description: Neste tutorial, você aprenderá a atualizar dinamicamente os dados de configuração para aplicativos ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 8032a9b206be4a5eb70a1f40fd33667a1dbdfaa7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714725"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Tutorial: usar a configuração dinâmica em um aplicativo ASP.NET Core

ASP.NET Core tem um sistema de configuração conectável que pode ler dados de configuração de uma variedade de fontes. Ele pode lidar com alterações imediatamente sem causar a reinicialização de um aplicativo. ASP.NET Core dá suporte à associação de definições de configuração para classes .NET fortemente tipadas. Injeta-os no seu código utilizando os vários padrões de `IOptions<T>`. Um destes padrões, especificamente `IOptionsSnapshot<T>`, recarrega automaticamente a configuração da aplicação quando os dados subjacentes mudam. Pode injetar `IOptionsSnapshot<T>` em controladores na sua aplicação para aceder à configuração mais recente armazenada na Configuração de Aplicações Azure.

Você também pode configurar a biblioteca de cliente de configuração de aplicativo ASP.NET Core para atualizar um conjunto de definições de configuração dinamicamente usando um middleware. Desde que o aplicativo Web continue a receber solicitações, as definições de configuração continuarão a ser atualizadas com o repositório de configurações.

Para manter as configurações atualizadas e evitar muitas chamadas para o repositório de configuração, um cache é usado para cada configuração. Até que o valor em cache de uma configuração tenha expirado, a operação de atualização não atualizará o valor, mesmo quando o valor tiver sido alterado no repositório de configuração. O tempo de expiração padrão para cada solicitação é de 30 segundos, mas pode ser substituído se necessário.

Este tutorial mostra como você pode implementar atualizações de configuração dinâmicas em seu código. Ele se baseia no aplicativo Web introduzido nos guias de início rápido. Antes de continuar, termine [Criar uma aplicação ASP.NET Core com configuração](./quickstart-aspnet-core-app.md) de aplicações primeiro.

Você pode usar qualquer editor de código para executar as etapas neste tutorial. [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção que está disponível nas plataformas Windows, macOS e Linux.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure seu aplicativo para atualizar sua configuração em resposta a alterações em um repositório de configuração de aplicativo.
> * Insira a configuração mais recente nos controladores do aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

Para fazer este tutorial, instale o [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Antes de continuar, termine [Criar uma aplicação ASP.NET Core com configuração](./quickstart-aspnet-core-app.md) de aplicações primeiro.

## <a name="reload-data-from-app-configuration"></a>Recarregar dados da configuração do aplicativo

1. Adicione uma referência ao pacote nuGet `Microsoft.Azure.AppConfiguration.AspNetCore` executando o seguinte comando:

    ```CLI
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 3.0.0-preview-011100002-1192
    ```

1. Abra *Program.cs*e atualize o método `CreateWebHostBuilder` para adicionar o método `config.AddAzureAppConfiguration()`.

    #### <a name="net-core-2xtabcore2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .ConfigureRefresh(refresh =>
                           {
                               refresh.Register("TestApp:Settings:BackgroundColor")
                                      .Register("TestApp:Settings:FontColor")
                                      .Register("TestApp:Settings:Message");
                           });
                });
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3xtabcore3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                    {   
                        options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureRefresh(refresh =>
                                {
                                    refresh.Register("TestApp:Settings:BackgroundColor")
                                            .Register("TestApp:Settings:FontColor")
                                            .Register("TestApp:Settings:Message");
                                });
                    });
                })
            .UseStartup<Startup>());
    ```
    ---

    O método `ConfigureRefresh` é utilizado para especificar as definições utilizadas para atualizar os dados de configuração com a loja de configuração da aplicação quando é acionada uma operação de atualização. Para realmente disparar uma operação de atualização, um middleware de atualização precisa ser configurado para que o aplicativo atualize os dados de configuração quando ocorre alguma alteração.

2. Adicione um ficheiro *Settings.cs* que define e implementa uma nova classe `Settings`.

    ```csharp
    namespace TestAppConfig
    {
        public class Settings
        {
            public string BackgroundColor { get; set; }
            public long FontSize { get; set; }
            public string FontColor { get; set; }
            public string Message { get; set; }
        }
    }
    ```

3. Abra *Startup.cs*e utilize `IServiceCollection.Configure<T>` no método `ConfigureServices` para ligar os dados de configuração à classe `Settings`.

    #### <a name="net-core-2xtabcore2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

    #### <a name="net-core-3xtabcore3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
    }
    ```
    ---

4. Atualize o método `Configure`, adicionando o `UseAzureAppConfiguration` middleware para permitir que as configurações de configuração registadas para a atualização sejam atualizadas enquanto a aplicação web ASP.NET Core continua a receber pedidos.


    #### <a name="net-core-2xtabcore2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        app.UseMvc();
    }
    ```

    #### <a name="net-core-3xtabcore3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    ---
    
    O middleware utiliza a configuração de atualização especificada no método `AddAzureAppConfiguration` em `Program.cs` para desencadear uma atualização para cada pedido recebido pela aplicação web ASP.NET Core. Para cada solicitação, uma operação de atualização é disparada e a biblioteca de cliente verifica se o valor em cache para as definições de configuração registradas expirou. Para os valores armazenados em cache que expiraram, os valores para as configurações são atualizados com o repositório de configuração de aplicativo e os valores restantes permanecem inalterados.
    
    > [!NOTE]
    > O tempo de validade da cache padrão para uma definição de configuração é de 30 segundos, mas pode ser ultrapassado chamando o método `SetCacheExpiration` sobre as opções inicializador espávida como um argumento para o método `ConfigureRefresh`.

## <a name="use-the-latest-configuration-data"></a>Usar os dados de configuração mais recentes

1. Abra *HomeController.cs* no diretório de Controladores e adicione uma referência ao pacote `Microsoft.Extensions.Options`.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Atualize a classe `HomeController` para receber `Settings` através da injeção de dependência, e faça uso dos seus valores.

    #### <a name="net-core-2xtabcore2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        public HomeController(IOptionsSnapshot<Settings> settings)
        {
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }
    }
    ```

    #### <a name="net-core-3xtabcore3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }

        // ...
    }
    ```
    ---



3. Open *Index.cshtml* no diretório Views > Home, e substitua o seu conteúdo pelo seguinte script:

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"];
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@ViewData["Message"]</h1>
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Para compilar o aplicativo usando o CLI do .NET Core, execute o seguinte comando no Shell de comando:

        dotnet build

2. Depois que a compilação for concluída com êxito, execute o seguinte comando para executar o aplicativo Web localmente:

        dotnet run

3. Abra uma janela do navegador e vá para `http://localhost:5000`, que é o URL padrão para a aplicação web hospedada localmente.

    ![Local de inicialização do aplicativo de início rápido](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Todos os recursos**e selecione a instância da loja de configuração de aplicações que criou no arranque rápido.

5. Selecione O Explorador de **Configuração**e atualize os valores das seguintes teclas:

    | Chave | Valor |
    |---|---|
    | TestApp:Settings:BackgroundColor | verde |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp: configurações: mensagem | Dados da configuração do Azure App – agora com atualizações dinâmicas! |

6. Atualize a página do navegador para ver as novas definições de configuração. Mais de uma atualização da página do navegador pode ser necessária para que as alterações sejam refletidas.

    ![Local de atualização do aplicativo de início rápido](./media/quickstarts/aspnet-core-app-launch-local-after.png)
    
    > [!NOTE]
    > Como as definições de configuração são armazenadas em cache com um tempo de expiração padrão de 30 segundos, todas as alterações feitas nas configurações no repositório de configuração de aplicativo só serão refletidas no aplicativo Web quando o cache tiver expirado.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, você habilitou seu aplicativo Web ASP.NET Core para atualizar dinamicamente as definições de configuração da configuração do aplicativo. Para saber como usar uma identidade gerenciada do Azure para simplificar o acesso à configuração do aplicativo, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerida](./howto-integrate-azure-managed-service-identity.md)

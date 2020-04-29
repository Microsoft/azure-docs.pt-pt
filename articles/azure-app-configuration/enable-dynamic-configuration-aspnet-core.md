---
title: 'Tutorial: Utilize a configuração dinâmica de configuração da configuração dinâmica da configuração da configuração da configuração da configuração da configuração ASP.NET da configuração'
titleSuffix: Azure App Configuration
description: Neste tutorial, aprende-se a atualizar de forma dinâmica os dados de configuração para ASP.NET aplicações Core
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
ms.openlocfilehash: e9df6d2e7a8219d16e7b60f7c3b8d826a87e6110
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80348860"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Tutorial: Use a configuração dinâmica numa aplicação ASP.NET Core

ASP.NET Core tem um sistema de configuração pluggable que pode ler dados de configuração de várias fontes. Pode lidar com alterações dinamicamente sem causar o reinício de uma aplicação. ASP.NET Core suporta a ligação das definições de configuração para classes .NET fortemente digitadas. Injeta-os no seu código `IOptions<T>` usando os vários padrões. Um destes padrões, especificamente, `IOptionsSnapshot<T>`recarrega automaticamente a configuração da aplicação quando os dados subjacentes mudam. Pode injetar `IOptionsSnapshot<T>` nos controladores na sua aplicação para aceder à configuração mais recente armazenada na Configuração de Aplicações Azure.

Também pode configurar a configuração da aplicação ASP.NET biblioteca de clientes Core para refrescar um conjunto de configurações de configuração dinamicamente usando um middleware. As definições de configuração são atualizadas com a loja de configuração de cada vez, desde que a aplicação web receba pedidos.

A configuração da aplicação cacheautomaticamente cada definição para evitar demasiadas chamadas para a loja de configuração. A operação de atualização aguarda até que o valor cached de uma definição expire para atualizar essa definição, mesmo quando o seu valor muda na loja de configuração. O tempo de validade da cache predefinido é de 30 segundos. Pode anular este tempo de validade, se necessário.

Este tutorial mostra como pode implementar atualizações dinâmicas de configuração no seu código. Baseia-se na aplicação web introduzida nos quickstarts. Antes de continuar, termine [Criar uma aplicação ASP.NET Core com configuração](./quickstart-aspnet-core-app.md) de aplicações primeiro.

Pode usar qualquer editor de código para fazer os passos neste tutorial. [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção que está disponível nas plataformas Windows, macOS e Linux.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configura riste a sua aplicação para atualizar a sua configuração em resposta a alterações numa loja de Configuração de Aplicações.
> * Injete a configuração mais recente nos controladores da sua aplicação.

## <a name="prerequisites"></a>Pré-requisitos

Para fazer este tutorial, instale o [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Antes de continuar, termine [Criar uma aplicação ASP.NET Core com configuração](./quickstart-aspnet-core-app.md) de aplicações primeiro.

## <a name="add-a-sentinel-key"></a>Adicione uma chave sentinela

Uma *tecla sentinela* é uma chave especial usada para sinalizar quando a configuração mudou. A sua aplicação monitoriza a tecla sentinela para alterações. Quando é detetada uma alteração, refresca todos os valores de configuração. Esta abordagem reduz o número total de pedidos feitos pela sua app para configuração de aplicações, em comparação com a monitorização de todas as teclas para alterações.

1. No portal Azure, selecione O Explorador de **Configuração > Criar > valor-chave**.

1. Para **a tecla**, *introduza testApp:Definições:Sentinel*. Para **Valor,** insira 1. Deixe o tipo **de etiqueta** e **conteúdo** em branco.

1. Selecione **Aplicar**.

## <a name="reload-data-from-app-configuration"></a>Recarregar dados da configuração da app

1. Adicione uma referência `Microsoft.Azure.AppConfiguration.AspNetCore` ao pacote NuGet executando o seguinte comando:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Abra *Program.cs*e `CreateWebHostBuilder` atualize o `config.AddAzureAppConfiguration()` método para adicionar o método.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

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
                                    refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                           .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                });
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
                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(settings["ConnectionStrings:AppConfig"])
                               .ConfigureRefresh(refresh =>
                                    {
                                        refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                               .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                    });
                    });
                })
            .UseStartup<Startup>());
    ```
    ---

    O `ConfigureRefresh` método é utilizado para especificar as definições utilizadas para atualizar os dados de configuração com a loja de configuração da aplicação quando é acionada uma operação de atualização. O `refreshAll` parâmetro do `Register` método indica que todos os valores de configuração devem ser atualizados se a tecla sentinela mudar.

    Além `SetCacheExpiration` disso, o método substitui o tempo de validade da cache padrão de 30 segundos, especificando um tempo de 5 minutos. Isto reduz o número de pedidos feitos à Configuração de Aplicações.

    > [!NOTE]
    > Para efeitos de teste, é melhor baixar o tempo de validade da cache.

    Para realmente desencadear uma operação de atualização, terá de configurar um meio-termo atualizado para a aplicação atualizar os dados de configuração quando ocorrer qualquer alteração. Verá como fazer isso num passo posterior.

2. Adicione um ficheiro *Settings.cs* que define `Settings` e implementa uma nova classe.

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

3. Abra *Startup.cs*e `IServiceCollection.Configure<T>` utilize `ConfigureServices` no método para `Settings` ligar os dados de configuração à classe.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
    }
    ```
    ---

4. Atualize `Configure` o método, adicionando o `UseAzureAppConfiguration` middleware para permitir que as configurações de configuração registadas para a atualização sejam atualizadas enquanto a ASP.NET aplicação web Core continua a receber pedidos.


    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

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

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

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
    
    O middleware utiliza a configuração `AddAzureAppConfiguration` de `Program.cs` atualização especificada no método para desencadear uma atualização para cada pedido recebido pela aplicação web ASP.NET Core. Para cada pedido, é desencadeada uma operação de atualização e a biblioteca do cliente verifica se o valor em cache para a configuração registada expirou. Se expirar, é renovado.

## <a name="use-the-latest-configuration-data"></a>Utilize os dados mais recentes da configuração

1. Abra *HomeController.cs* no diretório de Controladores e `Microsoft.Extensions.Options` adicione uma referência ao pacote.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Atualize `HomeController` a `Settings` classe para receber através da injeção de dependência e faça uso dos seus valores.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

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

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

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



3. Open *Index.cshtml* no diretório de visualizações > home, e substitua o seu conteúdo pelo seguinte script:

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"]px;
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

## <a name="build-and-run-the-app-locally"></a>Construir e executar a app localmente

1. Para construir a aplicação utilizando o CLI .NET Core, execute o seguinte comando na concha de comando:

        dotnet build

1. Depois de a construção concluída com sucesso, execute o seguinte comando para executar a aplicação web localmente:

        dotnet run
1. Abra uma janela do navegador e vá `dotnet run` ao URL mostrado na saída.

    ![Lançar app quickstart localmente](./media/quickstarts/aspnet-core-app-launch-local-before.png)

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Todos os recursos**e selecione a instância da loja de configuração de aplicações que criou no arranque rápido.

1. Selecione O Explorador de **Configuração**e atualize os valores das seguintes teclas:

    | Chave | Valor |
    |---|---|
    | TestApp:Definições:BackgroundColor | green |
    | TestApp:Definições:FontColor | luzGray |
    | TestApp:Definições:Mensagem | Dados da Configuração de Aplicações Azure - agora com atualizações ao vivo! |
    | TestApp:Configurações:Sentinel | 2 |

1. Refresque a página do navegador para ver as novas configurações de configuração. Pode ser necessário refrescar mais de uma vez para que as alterações sejam refletidas.

    ![Lançamento de app quickstart atualizada localmente](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, permitiu que o seu ASP.NET aplicação web Core adote as configurações de configuração de forma dinâmica a partir da Configuração da App. Para aprender a usar uma identidade gerida pelo Azure para agilizar o acesso à Configuração de Aplicações, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerida](./howto-integrate-azure-managed-service-identity.md)

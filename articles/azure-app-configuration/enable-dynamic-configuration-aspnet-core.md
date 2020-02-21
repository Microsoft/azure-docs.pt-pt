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
ms.openlocfilehash: 3c461e543e3b01501ec47589a9eab3d74820491a
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77500236"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Tutorial: Use a configuração dinâmica numa aplicação ASP.NET Core

ASP.NET Core tem um sistema de configuração pluggable que pode ler dados de configuração de várias fontes. Pode lidar com alterações no voo sem causar o reinício de uma aplicação. ASP.NET Core suporta a ligação das definições de configuração para classes .NET fortemente digitadas. Injeta-os no seu código utilizando os vários padrões de `IOptions<T>`. Um destes padrões, especificamente `IOptionsSnapshot<T>`, recarrega automaticamente a configuração da aplicação quando os dados subjacentes mudam. Pode injetar `IOptionsSnapshot<T>` em controladores na sua aplicação para aceder à configuração mais recente armazenada na Configuração de Aplicações Azure.

Também pode configurar a configuração da aplicação ASP.NET biblioteca de clientes Core para refrescar um conjunto de configurações de configuração dinamicamente usando um middleware. Enquanto a aplicação da web continuar a receber pedidos, as definições de configuração continuam a ser atualizadas com a loja de configuração.

Para manter as definições atualizadas e evitar demasiadas chamadas para a loja de configuração, é utilizada uma cache para cada definição. Até que o valor cached de uma definição tenha expirado, a operação de atualização não atualiza o valor, mesmo quando o valor mudou na loja de configuração. O tempo de validade padrão para cada pedido é de 30 segundos, mas pode ser ultrapassado se necessário.

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

## <a name="reload-data-from-app-configuration"></a>Recarregar dados da configuração da app

1. Adicione uma referência ao pacote nuGet `Microsoft.Azure.AppConfiguration.AspNetCore` executando o seguinte comando:

    ```CLI
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Abra *Program.cs*e atualize o método `CreateWebHostBuilder` para adicionar o método `config.AddAzureAppConfiguration()`.

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
                               refresh.Register("TestApp:Settings:BackgroundColor")
                                      .Register("TestApp:Settings:FontColor")
                                      .Register("TestApp:Settings:Message");
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
                                    refresh.Register("TestApp:Settings:BackgroundColor")
                                            .Register("TestApp:Settings:FontColor")
                                            .Register("TestApp:Settings:Message");
                                });
                    });
                })
            .UseStartup<Startup>());
    ```
    ---

    O método `ConfigureRefresh` é utilizado para especificar as definições utilizadas para atualizar os dados de configuração com a loja de configuração da aplicação quando é acionada uma operação de atualização. Para realmente desencadear uma operação de atualização, é necessário configurar um meio-termo atualizado para a aplicação atualizar os dados de configuração quando ocorrer qualquer alteração.

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

4. Atualize o método `Configure`, adicionando o `UseAzureAppConfiguration` middleware para permitir que as configurações de configuração registadas para a atualização sejam atualizadas enquanto a aplicação web ASP.NET Core continua a receber pedidos.


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
    
    O middleware utiliza a configuração de atualização especificada no método `AddAzureAppConfiguration` em `Program.cs` para desencadear uma atualização para cada pedido recebido pela aplicação web ASP.NET Core. Para cada pedido, é desencadeada uma operação de atualização e a biblioteca do cliente verifica se o valor em cache para as configurações de configuração registada supram. Para os valores em cache que tenham expirado, os valores das definições são atualizados com a loja de Configuração de Aplicações, e os valores restantes permanecem inalterados.
    
    > [!NOTE]
    > O tempo de validade da cache padrão para uma definição de configuração é de 30 segundos, mas pode ser ultrapassado chamando o método `SetCacheExpiration` sobre as opções inicializador espávida como um argumento para o método `ConfigureRefresh`.

## <a name="use-the-latest-configuration-data"></a>Utilize os dados mais recentes da configuração

1. Abra *HomeController.cs* no diretório de Controladores e adicione uma referência ao pacote `Microsoft.Extensions.Options`.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Atualize a classe `HomeController` para receber `Settings` através da injeção de dependência, e faça uso dos seus valores.

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

## <a name="build-and-run-the-app-locally"></a>Construir e executar a app localmente

1. Para construir a aplicação utilizando o CLI .NET Core, execute o seguinte comando na concha de comando:

        dotnet build

2. Depois de a construção concluída com sucesso, execute o seguinte comando para executar a aplicação web localmente:

        dotnet run

3. Abra uma janela do navegador e vá para `http://localhost:5000`, que é o URL padrão para a aplicação web hospedada localmente.

    ![Lançamento de app Quickstart local](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **Todos os recursos**e selecione a instância da loja de configuração de aplicações que criou no arranque rápido.

5. Selecione O Explorador de **Configuração**e atualize os valores das seguintes teclas:

    | Chave | Valor |
    |---|---|
    | TestApp:Settings:BackgroundColor | green |
    | TestApp:Settings:FontColor | luzGray |
    | TestApp:Definições:Mensagem | Dados da Configuração de Aplicações Azure - agora com atualizações ao vivo! |

6. Refresque a página do navegador para ver as novas configurações de configuração. Mais de uma atualização da página do navegador pode ser necessária para que as alterações sejam refletidas.

    ![App Quickstart refrescar local](./media/quickstarts/aspnet-core-app-launch-local-after.png)
    
    > [!NOTE]
    > Uma vez que as definições de configuração são colocadas em cache com um tempo de validade padrão de 30 segundos, quaisquer alterações feitas nas definições na loja de configuração da aplicação só seriam refletidas na aplicação web quando a cache expirasse.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, permitiu que o seu ASP.NET aplicação web Core adote as configurações de configuração de forma dinâmica a partir da Configuração da App. Para aprender a usar uma identidade gerida pelo Azure para agilizar o acesso à Configuração de Aplicações, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerida](./howto-integrate-azure-managed-service-identity.md)

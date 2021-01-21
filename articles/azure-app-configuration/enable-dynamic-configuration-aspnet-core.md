---
title: 'Tutorial: Use configuração dinâmica de configuração de configuração de aplicativos em ASP.NET Core'
titleSuffix: Azure App Configuration
description: Neste tutorial, aprende-se a atualizar dinamicamente os dados de configuração para ASP.NET aplicações Core
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/1/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 7072720e2600221e7b8ad8d2337577b65b079afb
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660687"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Tutorial: Use configuração dinâmica numa aplicação core ASP.NET

ASP.NET Core tem um sistema de configuração pluggável que pode ler dados de configuração de várias fontes. Pode lidar com alterações dinamicamente sem fazer com que uma aplicação reinicie. ASP.NET Core suporta a ligação das definições de configuração para classes .NET fortemente dactilografada. Injeta-os no seu código usando os `IOptions<T>` vários padrões. Um destes padrões, `IOptionsSnapshot<T>` especificamente, recarrega automaticamente a configuração da aplicação quando os dados subjacentes mudam. Pode `IOptionsSnapshot<T>` injetar-se nos controladores na sua aplicação para aceder à configuração mais recente armazenada na Configuração da Aplicação Azure.

Também pode configurar a configuração de aplicação ASP.NET biblioteca de clientes Core para atualizar um conjunto de definições de configuração dinamicamente usando um middleware. As definições de configuração são atualizadas com a loja de configuração sempre que a aplicação web recebe pedidos.

A Configuração da Aplicação cache automaticamente cada definição para evitar demasiadas chamadas para a loja de configuração. A operação de atualização aguarda até que o valor em cache de uma definição expire para atualizar essa definição, mesmo quando o seu valor muda na loja de configuração. O tempo de validade da cache padrão é de 30 segundos. Pode anular este tempo de validade, se necessário.

Este tutorial mostra como pode implementar atualizações dinâmicas de configuração no seu código. Baseia-se na aplicação web introduzida nos quickstarts. Antes de continuar, termine [Criar uma aplicação Core ASP.NET com a Configuração de Aplicações](./quickstart-aspnet-core-app.md) em primeiro lugar.

Você pode usar qualquer editor de código para fazer os passos neste tutorial. [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção disponível nas plataformas Windows, macOS e Linux.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Configurar a sua aplicação para atualizar a sua configuração em resposta a alterações numa loja de Configuração de Aplicações.
> * Injete a configuração mais recente nos controladores da sua aplicação.

## <a name="prerequisites"></a>Pré-requisitos

Para fazer este tutorial, instale o [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Antes de continuar, termine [Criar uma aplicação Core ASP.NET com a Configuração de Aplicações](./quickstart-aspnet-core-app.md) em primeiro lugar.

## <a name="add-a-sentinel-key"></a>Adicione uma chave de sentinela

Uma *chave sentinela* é uma chave especial usada para sinalizar quando a configuração foi alterada. A sua aplicação monitoriza a chave sentinela para alterações. Quando uma alteração é detetada, atualiza-se todos os valores de configuração. Esta abordagem reduz o número total de pedidos feitos pela sua app para a Configuração de Aplicações, em comparação com a monitorização de todas as teclas para alterações.

1. No portal Azure, selecione **Configuration Explorer > Criar > valor-chave**.
1. Para **tecla**, introduza *TestApp:Definições:Sentinela*. Para **Valor,** insira 1. Deixe **o rótulo** e o **conteúdo** em branco.
1. Selecione **Aplicar**.

> [!NOTE]
> Se não estiver a usar uma chave sentinela, tem de registar manualmente todas as chaves que pretende ver.

## <a name="reload-data-from-app-configuration"></a>Recarregar dados da Configuração de Aplicações

1. Adicione uma referência ao `Microsoft.Azure.AppConfiguration.AspNetCore` pacote NuGet executando o seguinte comando:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Abra *Program.cs* e atualize o `CreateWebHostBuilder` método para adicionar o `config.AddAzureAppConfiguration()` método.

   #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

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
    ---

    O `ConfigureRefresh` método é utilizado para especificar as definições utilizadas para atualizar os dados de configuração com a loja de Configuração de Aplicações quando uma operação de atualização é desencadeada. O `refreshAll` parâmetro do método indica que todos os `Register` valores de configuração devem ser atualizados se a chave sentinela mudar.

    Além disso, o `SetCacheExpiration` método substitui o tempo de validade da cache padrão de 30 segundos, especificando um tempo de 5 minutos. Isto reduz o número de pedidos feitos para a Configuração de Aplicações.

    > [!NOTE]
    > Para efeitos de teste, é melhor baixar o tempo de validade da cache.

    Para desencadear uma operação de atualização, terá de configurar um middleware de atualização para a aplicação para atualizar os dados de configuração quando ocorrer qualquer alteração. Verá como fazer isto mais tarde.

2. Adicione um ficheiro *Settings.cs* no diretório de Controladores que define e implementa uma nova `Settings` classe. Substitua o espaço de nome pelo nome do seu projeto. 

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

3. Abra *Startup.cs*, e use no método para ligar os dados de `IServiceCollection.Configure<T>` `ConfigureServices` configuração à `Settings` classe.

    #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
        services.AddAzureAppConfiguration();
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
        services.AddAzureAppConfiguration();
    }
    ```
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```
    ---
    > [!Tip]
    > Para saber mais sobre o padrão de opções ao ler valores de configuração, consulte [Padrões de Opções no ASP.NET Core](/aspnet/core/fundamentals/configuration/options?view=aspnetcore-3.1).

4. Atualize o `Configure` método, adicionando o `UseAzureAppConfiguration` middleware para permitir que as definições de configuração registadas para atualização sejam atualizadas enquanto a aplicação web core ASP.NET continua a receber pedidos.


    #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

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
    ---
    
    O middleware utiliza a configuração de atualização especificada `AddAzureAppConfiguration` no método para desencadear uma `Program.cs` atualização para cada pedido recebido pela aplicação web Core ASP.NET. Para cada pedido, é ativada uma operação de atualização e a biblioteca do cliente verifica se o valor em cache para a definição de configuração registada expirou. Se tiver expirado, é refrescado.

    > [!NOTE]
    > Para garantir que a configuração é atualizada, adicione o middleware tão cedo quanto apropriado ao seu pipeline de pedido para que não seja curto-circuito por outro middleware na sua aplicação.

## <a name="use-the-latest-configuration-data"></a>Utilize os dados de configuração mais recentes

1. Abra *HomeController.cs* no diretório dos controladores e adicione uma referência ao `Microsoft.Extensions.Options` pacote.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Atualize a `HomeController` classe para receber através da injeção de dependência e faça uso dos seus `Settings` valores.

 #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

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
---



3. Abrir *Index.cshtml* no diretório Views > Home e substituir o seu conteúdo pelo seguinte script:

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

## <a name="build-and-run-the-app-locally"></a>Construa e execute a app localmente

1. Para construir a aplicação utilizando o .NET Core CLI, executar o seguinte comando na concha de comando:

    ```console
        dotnet build
    ```

1. Após a construção concluída com sucesso, executar o seguinte comando para executar a aplicação web localmente:

    ```console
        dotnet run
    ```

1. Abra uma janela do navegador e vá para o URL mostrado na `dotnet run` saída.

    ![Lançamento de app quickstart localmente](./media/quickstarts/aspnet-core-app-launch-local-before.png)

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Todos os recursos** e selecione a instância da loja de Configuração de Aplicações que criou no arranque rápido.

1. Selecione **Configuration Explorer** e atualize os valores das seguintes teclas:

    | Chave | Valor |
    |---|---|
    | TestApp:Definições:BackgroundColor | green |
    | TestApp:Definições:FontColor | lightGray |
    | TestApp:Definições:Mensagem | Dados da Configuração da App Azure - agora com atualizações ao vivo! |
    | TestApp:Definições:Sentinela | 2 |

1. Refresque a página do navegador para ver as novas definições de configuração. Poderá ter de atualizar mais de uma vez para que as alterações sejam refletidas ou altere a sua taxa de atualização automática para menos de 5 minutos. 

    ![Lançamento de app quickstart atualizada localmente](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, permitiu que a sua ASP.NET web atualizasse dinamicamente as definições de configuração a partir da Configuração da Aplicação. Para aprender a usar uma identidade gerida pelo Azure para agilizar o acesso à Configuração de Aplicações, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerida](./howto-integrate-azure-managed-service-identity.md)

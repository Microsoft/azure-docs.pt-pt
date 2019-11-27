---
title: Tutorial para usar a configuração dinâmica de configuração do Azure App em um aplicativo ASP.NET Core | Microsoft Docs
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
ms.openlocfilehash: f49161531753c217e31d0681bcd19043cb47de75
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185254"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Tutorial: usar a configuração dinâmica em um aplicativo ASP.NET Core

ASP.NET Core tem um sistema de configuração conectável que pode ler dados de configuração de uma variedade de fontes. Ele pode lidar com alterações imediatamente sem causar a reinicialização de um aplicativo. ASP.NET Core dá suporte à associação de definições de configuração para classes .NET fortemente tipadas. Ele os injeta em seu código usando os vários padrões de `IOptions<T>`. Um desses padrões, especificamente `IOptionsSnapshot<T>`, recarrega automaticamente a configuração do aplicativo quando os dados subjacentes são alterados. Você pode injetar `IOptionsSnapshot<T>` em controladores em seu aplicativo para acessar a configuração mais recente armazenada na configuração do Azure App.

Você também pode configurar a biblioteca de cliente de configuração de aplicativo ASP.NET Core para atualizar um conjunto de definições de configuração dinamicamente usando um middleware. Desde que o aplicativo Web continue a receber solicitações, as definições de configuração continuarão a ser atualizadas com o repositório de configurações.

Para manter as configurações atualizadas e evitar muitas chamadas para o repositório de configuração, um cache é usado para cada configuração. Até que o valor em cache de uma configuração tenha expirado, a operação de atualização não atualizará o valor, mesmo quando o valor tiver sido alterado no repositório de configuração. O tempo de expiração padrão para cada solicitação é de 30 segundos, mas pode ser substituído se necessário.

Este tutorial mostra como você pode implementar atualizações de configuração dinâmicas em seu código. Ele se baseia no aplicativo Web introduzido nos guias de início rápido. Antes de continuar, conclua a [criação de um aplicativo ASP.NET Core com a configuração de aplicativo](./quickstart-aspnet-core-app.md) primeiro.

Você pode usar qualquer editor de código para executar as etapas neste tutorial. [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção disponível nas plataformas Windows, MacOS e Linux.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure seu aplicativo para atualizar sua configuração em resposta a alterações em um repositório de configuração de aplicativo.
> * Insira a configuração mais recente nos controladores do aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

Para fazer este tutorial, instale o [SDK do .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Antes de continuar, conclua a [criação de um aplicativo ASP.NET Core com a configuração de aplicativo](./quickstart-aspnet-core-app.md) primeiro.

## <a name="reload-data-from-app-configuration"></a>Recarregar dados da configuração do aplicativo

1. Adicione uma referência ao pacote `Microsoft.Azure.AppConfiguration.AspNetCore` NuGet executando o seguinte comando:

    ```CLI
        dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-010060003-1250
    ```

1. Abra *Program.cs*e atualize o método `CreateWebHostBuilder` para adicionar o método `config.AddAzureAppConfiguration()`.

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

    O método `ConfigureRefresh` é usado para especificar as configurações usadas para atualizar os dados de configuração com o repositório de configuração de aplicativo quando uma operação de atualização é disparada. Para realmente disparar uma operação de atualização, um middleware de atualização precisa ser configurado para que o aplicativo atualize os dados de configuração quando ocorre alguma alteração.

2. Adicione um arquivo *Settings.cs* que define e implementa uma nova classe `Settings`.

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

3. Abra *Startup.cs*e atualize o método `ConfigureServices` para associar os dados de configuração à classe `Settings`.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

4. Atualize o método de `Configure` para adicionar um middleware para permitir que as definições de configuração registradas para atualização sejam atualizadas enquanto o aplicativo Web ASP.NET Core continua a receber solicitações.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();
        app.UseMvc();
    }
    ```
    
    O middleware usa a configuração de atualização especificada no método `AddAzureAppConfiguration` no `Program.cs` para disparar uma atualização para cada solicitação recebida pelo aplicativo Web ASP.NET Core. Para cada solicitação, uma operação de atualização é disparada e a biblioteca de cliente verifica se o valor em cache para as definições de configuração registradas expirou. Para os valores armazenados em cache que expiraram, os valores para as configurações são atualizados com o repositório de configuração de aplicativo e os valores restantes permanecem inalterados.
    
    > [!NOTE]
    > O tempo de expiração de cache padrão para uma definição de configuração é de 30 segundos, mas pode ser substituído chamando o método `SetCacheExpiration` no inicializador de opções passado como um argumento para o método `ConfigureRefresh`.

## <a name="use-the-latest-configuration-data"></a>Usar os dados de configuração mais recentes

1. Abra *HomeController.cs* no diretório de controladores e adicione uma referência ao pacote de `Microsoft.Extensions.Options`.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Atualize a classe `HomeController` para receber `Settings` por meio de injeção de dependência e use seus valores.

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

3. Abra *index. cshtml* nas exibições > diretório base e substitua seu conteúdo pelo seguinte script:

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

3. Abra uma janela do navegador e vá para `http://localhost:5000`, que é a URL padrão para o aplicativo Web hospedado localmente.

    ![Local de inicialização do aplicativo de início rápido](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **todos os recursos**e selecione a instância do repositório de configuração de aplicativo que você criou no guia de início rápido.

5. Selecione **Configuration Explorer**e atualize os valores das seguintes chaves:

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

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você habilitou seu aplicativo Web ASP.NET Core para atualizar dinamicamente as definições de configuração da configuração do aplicativo. Para saber como usar uma identidade gerenciada do Azure para simplificar o acesso à configuração do aplicativo, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerenciada](./howto-integrate-azure-managed-service-identity.md)

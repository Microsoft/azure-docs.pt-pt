---
title: Tutorial para utilizar a configuração dinâmica de configuração de aplicações do Azure numa aplicação ASP.NET Core | Documentos da Microsoft
description: Neste tutorial, irá aprender a atualizar dinamicamente os dados de configuração para aplicações ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: d5e1e5989f9902d9ab8dcc3e6c2b9d2a71f12df9
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224384"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Tutorial: Utilizar a configuração dinâmica de uma aplicação ASP.NET Core

ASP.NET Core tem um sistema de configuração conectáveis que pode ler dados de configuração de várias origens. Ele pode lidar com alterações imediatamente sem fazer com que um aplicativo reinicie. ASP.NET Core suporta a associação das definições de configuração para classes do .NET com rigidez de tipos. Ele injeta-los no seu código usando as várias `IOptions<T>` padrões. Um dos seguintes padrões especificamente `IOptionsSnapshot<T>`, automaticamente recarrega a configuração da aplicação quando os dados subjacentes são alterados. 

Pode injetar `IOptionsSnapshot<T>` nos controladores na sua aplicação para aceder à configuração mais recente, armazenada na configuração de aplicações do Azure. Também pode configurar a biblioteca de cliente de configuração de aplicação ASP.NET Core continuamente, monitorizar e recuperar qualquer alteração num arquivo de configuração de aplicação. É possível definir o intervalo periódico para a consulta.

Este tutorial mostra como pode implementar atualizações de configuração dinâmica no seu código. Ele se baseia na aplicação web introduzida nos rápidos. Antes de continuar, concluir [criar uma aplicação ASP.NET Core com a configuração de aplicações](./quickstart-aspnet-core-app.md) primeiro.

Pode utilizar qualquer editor de código para realizar os passos neste guia de introdução. [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção está disponível no Windows, macOS e plataformas Linux.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure a aplicação para atualizar a respetiva configuração em resposta a alterações num arquivo de configuração de aplicação.
> * Inserir a configuração mais recente em controladores de seu aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

Para fazer este início rápido, instale o [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Recarregar os dados de configuração de aplicações

1. Abra o ficheiro Program.cs e atualizar o `CreateWebHostBuilder` método adicionando o `config.AddAzureAppConfiguration()` método.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(o => o.Connect(settings["ConnectionStrings:AppConfig"])
                    .Watch("TestApp:Settings:BackgroundColor", TimeSpan.FromSeconds(1))
                    .Watch("TestApp:Settings:FontColor", TimeSpan.FromSeconds(1))
                    .Watch("TestApp:Settings:Message", TimeSpan.FromSeconds(1)));
            })
            .UseStartup<Startup>();
    ```

    O segundo parâmetro no `.Watch` método é o intervalo de consulta em que a biblioteca de cliente do ASP.NET consulta um arquivo de configuração de aplicação. A biblioteca de cliente verifica a definição de configuração específica para ver se qualquer alteração ocorreu.

2. Adicione um ficheiro Settings.cs que define e implementa um novo `Settings` classe.

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

3. Abra Startup.cs e atualize o `ConfigureServices` método para vincular dados de configuração para o `Settings` classe.

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

## <a name="use-the-latest-configuration-data"></a>Utilizar os dados de configuração mais recente

1. Abra HomeController.cs no diretório de controladores. Atualização do `HomeController` classe para receber `Settings` por meio de injeção de dependência e marca utilizar dos seus valores.

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

2. Abra o Index. cshtml nas vistas > diretório de casa e substituir seu conteúdo com o seguinte script:

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

## <a name="build-and-run-the-app-locally"></a>Criar e executar a aplicação localmente

1. Para criar a aplicação com a CLI do .NET Core, execute o seguinte comando na shell de comandos:

        dotnet build

2. Após a compilação for concluída com êxito, execute o seguinte comando para executar a aplicação web localmente:

        dotnet run

3. Abra uma janela do browser e aceda a `http://localhost:5000`, que é o URL predefinido da aplicação web alojada localmente.

    ![Início rápido iniciação da aplicação local](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. Inicie sessão no [portal do Azure](https://aka.ms/azconfig/portal). Selecione **todos os recursos**e selecione a instância de arquivo de configuração de aplicação que criou no guia de introdução.

5. Selecione **Explorador de chave/valor**e atualize os valores das seguintes chaves:

    | Chave | Value |
    |---|---|
    | TestAppSettings:BackgroundColor | Azul |
    | TestAppSettings:FontColor | lightGray |
    | TestAppSettings:Message | Dados de configuração de aplicações do Azure - agora com atualizações em direto! |

6. Atualize a página do browser para ver as novas definições de configuração.

    ![Atualização de aplicação de início rápido local](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, adicionou do Azure para simplificar o acesso à configuração de aplicações e melhorar a gestão de credenciais para a sua aplicação, a identidade de serviço gerido. Para saber mais sobre como utilizar a configuração de aplicações, avance para os exemplos da CLI do Azure.

> [!div class="nextstepaction"]
> [Amostras de CLI](./cli-samples.md)

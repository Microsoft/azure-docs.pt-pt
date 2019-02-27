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
ms.openlocfilehash: 44ae922b182874eef378d4868fb278c3c76252db
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884792"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Tutorial: Utilizar a configuração dinâmica de uma aplicação ASP.NET Core

ASP.NET Core tem um sistema de configuração conectáveis capaz de ler dados de configuração de uma variedade de origens, bem como lidar com alterações imediatamente sem fazer com que um aplicativo reinicie. Suporta a ligação das definições de configuração para classes do .NET com rigidez de tipos e injetá-los em seu código usando as várias `IOptions<T>` padrões. Um dos seguintes padrões especificamente `IOptionsSnapshot<T>`, fornece o recarregamento automático da configuração da aplicação quando os dados subjacentes são alterados. Pode injetar `IOptionsSnapshot<T>` nos controladores na sua aplicação para aceder à configuração mais recente, armazenada na configuração de aplicações do Azure. Além disso, pode configurar a biblioteca de cliente de configuração de aplicação ASP.NET Core continuamente, monitorizar e recuperar qualquer alteração num arquivo de configuração de aplicação por meio de consulta em intervalos periódicos que definir.

Este tutorial mostra como pode implementar atualizações de configuração dinâmica no seu código. Ele se baseia na aplicação web introduzida nos rápidos. Concluída [criar uma aplicação ASP.NET Core com a configuração de aplicações](./quickstart-aspnet-core-app.md) primeiro antes de continuar.

Pode utilizar qualquer editor de código para concluir os passos deste início rápido. No entanto, o [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção, disponível nas plataformas Windows, macOS e Linux.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar a aplicação para atualizar a respetiva configuração em resposta a alterações num arquivo de configuração de aplicação
> * Inserir a configuração mais recente em controladores da sua aplicação

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, instale o [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Recarregar os dados de configuração de aplicações

1. Open *Program.cs* e atualizar a `CreateWebHostBuilder` método adicionando o `config.AddAzureAppConfiguration()` método.

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

    O segundo parâmetro no `.Watch` método é o intervalo de consulta em que a biblioteca de cliente do ASP.NET consulta um arquivo de configuração de aplicação para ver se tiver ocorrido qualquer alteração para a definição de configuração específicos.

2. Adicionar uma *Settings.cs* ficheiro que define e implementa um novo `Settings` classe.

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

3. Open *Startup.cs* e atualizar a `ConfigureServices` método para vincular dados de configuração para o `Settings` classe.

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

1. Open *HomeController.cs* no *controladores* diretório, a atualização a `HomeController` classe para receber `Settings` por meio de injeção de dependência e marca utilizar dos seus valores.

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

2. Open *Index. cshtml* no *vistas* > *home page* diretório e substitua o respetivo conteúdo pelo seguinte:

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

1. Para criar a aplicação com a CLI de .NET Core, execute o seguinte comando na shell de comandos:

        dotnet build

2. Depois de concluir com êxito a compilação, execute o seguinte comando para executar a aplicação Web localmente:

        dotnet run

3. Inicie uma janela do browser e navegue para `http://localhost:5000`, que é o URL predefinido da aplicação web alojada localmente.

    ![Início rápido iniciação da aplicação local](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. Inicie sessão para o [portal do Azure](https://aka.ms/azconfig/portal), clique em **todos os recursos** e a instância de arquivo de configuração de aplicação que criou no guia de introdução.

5. Clique em **Explorador de chave/valor** e atualize os valores das seguintes chaves:

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
> [Exemplos da CLI](./cli-samples.md)

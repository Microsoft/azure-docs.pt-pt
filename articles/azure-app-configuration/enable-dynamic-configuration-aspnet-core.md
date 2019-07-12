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
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 78c64786f523aa424e8a9816e42db70e2a2997c2
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798457"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Tutorial: Utilizar a configuração dinâmica de uma aplicação ASP.NET Core

ASP.NET Core tem um sistema de configuração conectáveis que pode ler dados de configuração de várias origens. Ele pode lidar com alterações imediatamente sem fazer com que um aplicativo reinicie. ASP.NET Core suporta a associação das definições de configuração para classes do .NET com rigidez de tipos. Ele injeta-los no seu código usando as várias `IOptions<T>` padrões. Um dos seguintes padrões especificamente `IOptionsSnapshot<T>`, automaticamente recarrega a configuração da aplicação quando os dados subjacentes são alterados. Pode injetar `IOptionsSnapshot<T>` nos controladores na sua aplicação para aceder à configuração mais recente, armazenada na configuração de aplicações do Azure.

Também pode configurar a biblioteca de cliente de configuração de aplicação ASP.NET Core para atualizar um conjunto de definições de configuração dinamicamente usando um middleware. Desde que a aplicação web continua a receber pedidos, as definições de configuração continuam a ser atualizado com o armazenamento de configuração.

Para manter as definições atualizadas e evitar demasiadas chamadas para o armazenamento de configuração, um cache é utilizada para cada definição. Até que o valor em cache de uma configuração tiver expirado, a operação de atualização não atualiza o valor, mesmo quando o valor foi alterado no arquivo de configuração. A hora de expiração predefinida para cada solicitação é 30 segundos, mas pode ser substituído se necessário.

Este tutorial mostra como pode implementar atualizações de configuração dinâmica no seu código. Ele se baseia na aplicação web introduzida nos rápidos. Antes de continuar, concluir [criar uma aplicação ASP.NET Core com a configuração de aplicações](./quickstart-aspnet-core-app.md) primeiro.

Pode utilizar qualquer editor de código para realizar os passos neste tutorial. [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção está disponível no Windows, macOS e plataformas Linux.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure a aplicação para atualizar a respetiva configuração em resposta a alterações num arquivo de configuração de aplicação.
> * Inserir a configuração mais recente em controladores de seu aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

Para fazer este tutorial, instale o [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Recarregar os dados de configuração de aplicações

1. Open *Program.cs*e atualizar a `CreateWebHostBuilder` método para adicionar o `config.AddAzureAppConfiguration()` método.

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
                                      .Register("TestApp:Settings:Message")
                           });
                }
            })
            .UseStartup<Startup>();
    ```

    O `ConfigureRefresh` método é usado para especificar as definições utilizadas para atualizar os dados de configuração com o armazenamento de configuração de aplicação quando uma operação de atualização é acionada. Para acionar, na verdade, uma operação de atualização, um middleware de atualização tem de ser configurado para a aplicação atualizar os dados de configuração quando ocorrer qualquer alteração.

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

3. Open *Startup.cs*e atualizar a `ConfigureServices` método para vincular dados de configuração para o `Settings` classe.

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

4. Atualização do `Configure` método para adicionar um middleware para permitir que as definições de configuração registado para a atualização ser atualizado enquanto a aplicação web do ASP.NET Core continua a receber pedidos.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();
        app.UseMvc();
    }
    ```
    
    O middleware usa a configuração de atualização especificada na `AddAzureAppConfiguration` método na `Program.cs` para acionar uma atualização para cada solicitação recebida pela aplicação web ASP.NET Core. Para cada solicitação, uma operação de atualização é acionada e a biblioteca de cliente verifica se o valor em cache para que as definições de configuração registados ter expirado. Para os valores do cache que tiverem expirado, os valores para as definições são atualizados com o armazenamento de configuração de aplicação e os restantes valores permanecem inalterados.
    
    > [!NOTE]
    > A hora de expiração do cache padrão para uma definição de configuração é de 30 segundos, mas pode ser substituída ao chamar o `SetCacheExpiration` método no inicializador de opções passada como um argumento para o `ConfigureRefresh` método.

## <a name="use-the-latest-configuration-data"></a>Utilizar os dados de configuração mais recente

1. Open *HomeController.cs* no diretório de controladores e adicione uma referência para o `Microsoft.Extensions.Options` pacote.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Atualização do `HomeController` classe para receber `Settings` por meio de injeção de dependência e marca utilizar dos seus valores.

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

3. Open *Index. cshtml* nas vistas > diretório de casa e substituir seu conteúdo com o seguinte script:

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

4. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **todos os recursos**e selecione a instância de arquivo de configuração de aplicação que criou no guia de introdução.

5. Selecione **Explorador de configuração**e atualize os valores das seguintes chaves:

    | Chave | Valor |
    |---|---|
    | TestAppSettings:BackgroundColor | green |
    | TestAppSettings:FontColor | lightGray |
    | TestAppSettings:Message | Dados de configuração de aplicações do Azure - agora com atualizações em direto! |

6. Atualize a página do browser para ver as novas definições de configuração. Mais do que uma atualização da página do navegador pode ser necessária para que as alterações sejam refletidas.

    ![Atualização de aplicação de início rápido local](./media/quickstarts/aspnet-core-app-launch-local-after.png)
    
    > [!NOTE]
    > Uma vez que as definições de configuração são colocadas em cache com um tempo de expiração predefinida de 30 segundos, todas as alterações efetuadas às definições no arquivo de configuração de aplicação só serão refletidas no aplicativo web quando a cache tiver expirado.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, adicionou do Azure para simplificar o acesso à configuração de aplicações e melhorar a gestão de credenciais para a sua aplicação, a identidade de serviço gerido. Para saber mais sobre como utilizar a configuração de aplicações, avance para os exemplos da CLI do Azure.

> [!div class="nextstepaction"]
> [Amostras de CLI](./cli-samples.md)

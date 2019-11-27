---
title: Tutorial para usar a configuração dinâmica de configuração do Azure App em um aplicativo Azure Functions | Microsoft Docs
description: Neste tutorial, você aprenderá a atualizar dinamicamente os dados de configuração para aplicativos Azure Functions
services: azure-app-configuration
documentationcenter: ''
author: zhenlan
manager: qingye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/17/2019
ms.author: zhenlwa
ms.custom: azure-functions
ms.tgt_pltfrm: Azure Functions
ms.openlocfilehash: ba70d5f186c1424b2019716ab7a87aeae85f8913
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185451"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>Tutorial: usar a configuração dinâmica em um aplicativo Azure Functions

O provedor de configuração do .NET Standard de configuração de aplicativo dá suporte ao Caching e à atualização da configuração controlada dinamicamente pela atividade do aplicativo. Este tutorial mostra como você pode implementar atualizações de configuração dinâmicas em seu código. Ele se baseia no aplicativo Azure Functions introduzido nos guias de início rápido. Antes de continuar, conclua a [criação de um aplicativo do Azure Functions com Azure app configuração](./quickstart-azure-functions-csharp.md) primeiro.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure seu aplicativo Azure Functions para atualizar sua configuração em resposta a alterações em um repositório de configuração de aplicativo.
> * Insira a configuração mais recente para suas chamadas de Azure Functions.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) com a carga de trabalho de **desenvolvimento do Azure**
- [Ferramentas de Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- Concluir início rápido [criar um aplicativo do Azure Functions com a configuração do Azure app](./quickstart-azure-functions-csharp.md)

## <a name="reload-data-from-app-configuration"></a>Recarregar dados da configuração do aplicativo

1. Abra *function1.cs*. Além da `Configuration`de propriedades `static`, adicione uma nova propriedade `static` `ConfigurationRefresher` para manter uma instância singleton de `IConfigurationRefresher` que será usada para sinalizar atualizações de configuração durante chamadas de funções posteriormente.

    ```csharp
    private static IConfiguration Configuration { set; get; }
    private static IConfigurationRefresher ConfigurationRefresher { set; get; }
    ```

2. Atualize o construtor e use o método `ConfigureRefresh` para especificar a configuração a ser atualizada no repositório de configuração do aplicativo. Uma instância de `IConfigurationRefresher` é recuperada usando `GetRefresher` método. Opcionalmente, também alteramos a janela de tempo de expiração do cache de configuração para 1 minuto a partir do padrão de 30 segundos.

    ```csharp
    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   .ConfigureRefresh(refreshOptions =>
                        refreshOptions.Register("TestApp:Settings:Message")
                                      .SetCacheExpiration(TimeSpan.FromSeconds(60))
            );
            ConfigurationRefresher = options.GetRefresher();
        });
        Configuration = builder.Build();
    }
    ```

3. Atualize o método de `Run` e o sinal para atualizar a configuração usando o método `Refresh` no início da chamada de funções. Isso não será op se a janela de tempo de expiração do cache não for atingida. Remova o operador de `await` se preferir que a configuração seja atualizada sem bloqueio.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await ConfigurationRefresher.Refresh();

        string keyName = "TestApp:Settings:Message";
        string message = Configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>Testar localmente a função

1. Defina uma variável de ambiente chamada **ConnectionString**e defina-a como a chave de acesso para seu repositório de configuração de aplicativo. Se você usar o prompt de comando do Windows, execute o seguinte comando e reinicie o prompt de comando para permitir que a alteração entre em vigor:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se você usar o Windows PowerShell, execute o seguinte comando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Se você usar o macOS ou Linux, execute o seguinte comando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Para testar a sua função, prima F5. Se solicitado, aceite a solicitação do Visual Studio para baixar e instalar as ferramentas de **Azure Functions Core (CLI)** . Talvez você também precise habilitar uma exceção de firewall para que as ferramentas possam lidar com solicitações HTTP.

3. Copie o URL da sua função na saída do tempo de execução das funções do Azure.

    ![Depuração de função de início rápido no VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Cole o URL do pedido HTTP na barra de endereço do browser. A imagem a seguir mostra a resposta no navegador para a solicitação GET local retornada pela função.

    ![Local de inicialização da função de início rápido](./media/quickstarts/dotnet-core-function-launch-local.png)

5. Iniciar sessão no [portal do Azure](https://portal.azure.com). Selecione **todos os recursos**e selecione a instância do repositório de configuração de aplicativo que você criou no guia de início rápido.

6. Selecione **Configuration Explorer**e atualize os valores da seguinte chave:

    | Chave | Valor |
    |---|---|
    | TestApp: configurações: mensagem | Dados da configuração Azure App-atualizado |

7. Atualize o navegador algumas vezes. Quando a configuração armazenada em cache expira após um minuto, a página mostra a resposta da chamada de funções com o valor atualizado.

    ![Atualização local da função de início rápido](./media/quickstarts/dotnet-core-function-refresh-local.png)

O código de exemplo usado neste tutorial pode ser baixado do [repositório GitHub de configuração de aplicativo](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, você habilitou seu aplicativo Azure Functions para atualizar dinamicamente as definições de configuração da configuração do aplicativo. Para saber como usar uma identidade gerenciada do Azure para simplificar o acesso à configuração do aplicativo, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerenciada](./howto-integrate-azure-managed-service-identity.md)

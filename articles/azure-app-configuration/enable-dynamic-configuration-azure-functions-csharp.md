---
title: Tutorial para utilização de configuração dinâmica de configuração de configuração de configuração de aplicação de aplicação Azure App numa aplicação Azure Functions / Microsoft Docs
description: Neste tutorial, aprende-se a atualizar dinamicamente os dados de configuração das aplicações Azure Functions
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
ms.custom: devx-track-csharp, azure-functions
ms.tgt_pltfrm: Azure Functions
ms.openlocfilehash: e603aa8ba85fdd214c04de515f405bcf9028791e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88207107"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>Tutorial: Use configuração dinâmica numa aplicação Azure Functions

O fornecedor de configuração padrão de configuração da aplicação .NET suporta a configuração de caching e a configuração refrescante dinamicamente impulsionada pela atividade da aplicação. Este tutorial mostra como pode implementar atualizações dinâmicas de configuração no seu código. Baseia-se na aplicação Azure Functions introduzida nos quickstarts. Antes de continuar, termine [Crie primeiro uma aplicação de funções Azure com configuração de aplicações Azure.](./quickstart-azure-functions-csharp.md)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie a sua app Azure Functions para atualizar a sua configuração em resposta a alterações numa loja de Configuração de Aplicações.
> * Injete a configuração mais recente nas suas chamadas Azure Functions.

## <a name="prerequisites"></a>Pré-requisitos

- Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) com a carga de trabalho **de desenvolvimento Azure**
- [Ferramentas Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- Acabamento quickstart [Criar uma app de funções Azure com Configuração de Aplicação Azure](./quickstart-azure-functions-csharp.md)

## <a name="reload-data-from-app-configuration"></a>Recarregar dados da Configuração de Aplicações

1. Abra *Function1.cs.* Além da `static` `Configuration` propriedade, adicione uma nova `static` propriedade para manter uma instância singleton que será usada para sinalizar `ConfigurationRefresher` atualizações de `IConfigurationRefresher` configuração durante as chamadas de Funções mais tarde.

    ```csharp
    private static IConfiguration Configuration { set; get; }
    private static IConfigurationRefresher ConfigurationRefresher { set; get; }
    ```

2. Atualize o construtor e utilize o `ConfigureRefresh` método para especificar a definição a ser atualizada a partir da loja de Configuração de Aplicações. Um caso `IConfigurationRefresher` de é recuperado usando o `GetRefresher` método. Opcionalmente, também alteramos a janela de tempo de validade da cache de configuração para 1 minuto a partir dos 30 segundos predefinidos.

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

3. Atualize o `Run` método e o sinal para atualizar a configuração utilizando o método no início da chamada `TryRefreshAsync` Funções. Isto não será operatório se a janela de tempo de validade do cache não for alcançada. Retire o `await` operador se preferir que a configuração seja atualizada sem bloquear.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await ConfigurationRefresher.TryRefreshAsync(); 

        string keyName = "TestApp:Settings:Message";
        string message = Configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>Testar localmente a função

1. Desaprote uma variável ambiental chamada **ConnectionString**e desaprote-a na chave de acesso à sua loja de configuração de aplicações. Se utilizar o pedido de comando do Windows, executar o seguinte comando e reiniciar a solicitação de comando para permitir que a alteração entre em vigor:

    ```console
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Se utilizar o Windows PowerShell, executar o seguinte comando:

    ```powershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Se utilizar o macOS ou o Linux, executar o seguinte comando:

    ```console
    export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Para testar a sua função, prima F5. Se solicitado, aceite o pedido do Visual Studio para descarregar e instalar **ferramentas Azure Functions Core (CLI).** Também pode ser necessário ativar uma exceção de firewall para que as ferramentas possam lidar com pedidos HTTP.

3. Copie o URL da sua função na saída do tempo de execução das funções do Azure.

    ![Função quickstart depurando em VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Cole o URL do pedido HTTP na barra de endereço do browser. A imagem a seguir mostra a resposta no navegador ao pedido GET local devolvido pela função.

    ![Quickstart Function lançar local](./media/quickstarts/dotnet-core-function-launch-local.png)

5. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Todos os recursos**e selecione a instância da loja de Configuração de Aplicações que criou no arranque rápido.

6. Selecione **Configuration Explorer**e atualize os valores da seguinte tecla:

    | Chave | Valor |
    |---|---|
    | TestApp:Definições:Mensagem | Dados da Configuração da Aplicação Azure - Atualizado |

7. Refresque o navegador algumas vezes. Quando a definição em cache expira após um minuto, a página mostra a resposta da chamada funções com valor atualizado.

    ![Função quickstart refresca local](./media/quickstarts/dotnet-core-function-refresh-local.png)

O código de exemplo utilizado neste tutorial pode ser descarregado a partir de [App Configuration GitHub repo](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, permitiu que a sua aplicação Azure Functions atualizasse dinamicamente as definições de configuração a partir da Configuração da Aplicação. Para aprender a usar uma identidade gerida pelo Azure para agilizar o acesso à Configuração de Aplicações, continue até ao próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerida](./howto-integrate-azure-managed-service-identity.md)

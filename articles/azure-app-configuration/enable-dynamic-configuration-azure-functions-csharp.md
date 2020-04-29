---
title: Tutorial para usar configuração dinâmica de configuração de configuração de aplicação Azure em uma aplicação Funções Azure [ Microsoft Docs
description: Neste tutorial, aprende-se a atualizar de forma dinâmica os dados de configuração das aplicações De Funções Azure
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74185451"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>Tutorial: Utilize a configuração dinâmica numa aplicação de funções Azure

O fornecedor de configuração de configuração App Configuração .NET Standard suporta configuração de cache e configuração refrescante impulsionada dinamicamente pela atividade da aplicação. Este tutorial mostra como pode implementar atualizações dinâmicas de configuração no seu código. Baseia-se na aplicação Funções Azure introduzida nos quickstarts. Antes de continuar, termine Criar uma aplicação de [funções Azure com configuração de aplicações Azure](./quickstart-azure-functions-csharp.md) primeiro.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie a sua aplicação Funções Azure para atualizar a sua configuração em resposta a alterações numa loja de Configuração de Aplicações.
> * Injete a configuração mais recente nas chamadas das suas Funções Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Estúdio Visual 2019](https://visualstudio.microsoft.com/vs) com a carga de trabalho de **desenvolvimento do Azure**
- [Ferramentas de funções azure](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- Termine quickstart Criar uma aplicação de [funções Azure com configuração](./quickstart-azure-functions-csharp.md) de app Azure

## <a name="reload-data-from-app-configuration"></a>Recarregar dados da configuração da app

1. Abra *Function1.cs.* Além da `static` `Configuration`propriedade, adicione `static` `ConfigurationRefresher` uma nova propriedade `IConfigurationRefresher` para manter uma instância singleton que será usada para sinalizar atualizações de configuração durante as chamadas de Funções posteriormente.

    ```csharp
    private static IConfiguration Configuration { set; get; }
    private static IConfigurationRefresher ConfigurationRefresher { set; get; }
    ```

2. Atualize o construtor `ConfigureRefresh` e utilize o método para especificar a definição a renovar a partir da loja de configuração da aplicação. Uma instância `IConfigurationRefresher` de é `GetRefresher` recuperada usando o método. Opcionalmente, também alteramos a janela de tempo de validade da cache de configuração para 1 minuto a partir dos 30 segundos predefinidos.

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

3. Atualize `Run` o método e o `Refresh` sinal para refrescar a configuração utilizando o método no início da chamada funções. Isto não será operatório se a janela de tempo de validade da cache não for alcançada. Retire `await` o operador se preferir que a configuração seja atualizada sem bloquear.

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

1. Detete uma variável ambiental chamada **ConnectionString**e detetete-a na chave de acesso à sua loja de configuração de aplicações. Se utilizar o pedido de comando do Windows, execute o seguinte comando e reinicie a solicitação de comando para permitir que a alteração faça efeito:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se utilizar o Windows PowerShell, execute o seguinte comando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Se utilizar macOS ou Linux, execute o seguinte comando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Para testar a sua função, prima F5. Se solicitado, aceite o pedido do Estúdio Visual para descarregar e instalar **ferramentas Do Núcleo de Funções Azure (CLI).** Também pode ser necessário permitir uma exceção à firewall para que as ferramentas possam lidar com pedidos HTTP.

3. Copie o URL da sua função na saída do tempo de execução das funções do Azure.

    ![Depuração da função Quickstart em VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Cole o URL do pedido HTTP na barra de endereço do browser. A imagem que se segue mostra a resposta no navegador ao pedido de GET local devolvido pela função.

    ![Quickstart Function lançar local](./media/quickstarts/dotnet-core-function-launch-local.png)

5. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Todos os recursos**e selecione a instância da loja de configuração de aplicações que criou no arranque rápido.

6. Selecione O Explorador de **Configuração**e atualize os valores da seguinte tecla:

    | Chave | Valor |
    |---|---|
    | TestApp:Definições:Mensagem | Dados da Configuração de Aplicações Azure - Atualizado |

7. Refresque o navegador algumas vezes. Quando a definição em cache expira após um minuto, a página mostra a resposta da chamada funções com valor atualizado.

    ![Função Quickstart refrescar local](./media/quickstarts/dotnet-core-function-refresh-local.png)

O código de exemplo usado neste tutorial pode ser descarregado a partir de [App Configuration GitHub repo](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ativou a sua aplicação Funções Azure para atualizar dinamicamente as definições de configuração a partir da Configuração da App. Para aprender a usar uma identidade gerida pelo Azure para agilizar o acesso à Configuração de Aplicações, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerida](./howto-integrate-azure-managed-service-identity.md)

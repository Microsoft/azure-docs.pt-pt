---
title: Tutorial para utilização de configuração dinâmica de configuração de aplicação de aplicação Azure App numa aplicação Azure Functions / Microsoft Docs
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
ms.openlocfilehash: add4b54adb02db09536f4e56a7f039c46245c182
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2021
ms.locfileid: "97963569"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>Tutorial: Use configuração dinâmica numa aplicação Azure Functions

O fornecedor de configuração de configuração de configuração da aplicação .NET suporta a configuração de caching e a configuração refrescante dinamicamente impulsionada pela atividade da aplicação. Este tutorial mostra como pode implementar atualizações dinâmicas de configuração no seu código. Baseia-se na aplicação Azure Functions introduzida nos quickstarts. Antes de continuar, termine [Crie primeiro uma aplicação de funções Azure com configuração de aplicações Azure.](./quickstart-azure-functions-csharp.md)

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

1. Abra *Startup.cs* e atualize o `ConfigureAppConfiguration` método. 

   O `ConfigureRefresh` método regista uma definição a verificar para alterações sempre que uma atualização é desencadeada dentro da aplicação, o que fará no passo posterior ao adicionar `_configurationRefresher.TryRefreshAsync()` . O `refreshAll` parâmetro instrui o fornecedor de Configuração de Aplicações a recarregar toda a configuração sempre que for detetada uma alteração na definição registada.

    Todas as definições registadas para atualização têm uma expiração padrão de cache de 30 segundos. Pode ser atualizado chamando o `AzureAppConfigurationRefreshOptions.SetCacheExpiration` método.

    ```csharp
    public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
    {
        builder.ConfigurationBuilder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   // Load all keys that start with `TestApp:`
                   .Select("TestApp:*")
                   // Configure to reload configuration if the registered 'Sentinel' key is modified
                   .ConfigureRefresh(refreshOptions =>
                      refreshOptions.Register("TestApp:Settings:Sentinel", refreshAll: true));
        });
    }
    ```

   > [!TIP]
   > Quando está a atualizar vários valores-chave na Configuração da Aplicação, normalmente não pretende que a sua aplicação recarregue a configuração antes de todas as alterações serem feitas. Pode registar uma chave **sentinela** e só atualizá-la quando todas as outras alterações de configuração estiverem concluídas. Isto ajuda a garantir a consistência da configuração na sua aplicação.

2. Atualize o `Configure` método para disponibilizar serviços de Configuração de Aplicações Azure através da injeção de dependência.

    ```csharp
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.Services.AddAzureAppConfiguration();
    }
    ```

3. Abra *Function1.cs* e adicione os seguintes espaços de nome.

    ```csharp
    using System.Linq;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

   Atualize o construtor para obter a ocorrência de uma injeção de `IConfigurationRefresherProvider` dependência, a partir da qual pode obter a instância de `IConfigurationRefresher` .

    ```csharp
    private readonly IConfiguration _configuration;
    private readonly IConfigurationRefresher _configurationRefresher;

    public Function1(IConfiguration configuration, IConfigurationRefresherProvider refresherProvider)
    {
        _configuration = configuration;
        _configurationRefresher = refresherProvider.Refreshers.First();
    }
    ```

4. Atualize o `Run` método e o sinal para atualizar a configuração utilizando o método no início da chamada `TryRefreshAsync` Funções. Será um não-op se a janela de tempo de validade do cache não for alcançada. Retire o `await` operador se preferir que a configuração seja atualizada sem bloquear a chamada de Funções atuais. Nesse caso, as chamadas posteriores de Funções receberão valor atualizado.

    ```csharp
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await _configurationRefresher.TryRefreshAsync(); 

        string keyName = "TestApp:Settings:Message";
        string message = _configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>Testar localmente a função

1. Desaprote uma variável ambiental chamada **ConnectionString** e desaprote-a na chave de acesso à sua loja de configuração de aplicações. Se utilizar o pedido de comando do Windows, executar o seguinte comando e reiniciar a solicitação de comando para permitir que a alteração entre em vigor:

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

5. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Todos os recursos** e selecione a loja de Configuração de Aplicações que criou no arranque rápido.

6. Selecione **o explorador de configuração** e atualize o valor da seguinte tecla:

    | Chave | Valor |
    |---|---|
    | TestApp:Definições:Mensagem | Dados da Configuração da Aplicação Azure - Atualizado |

   Em seguida, crie a chave sentinela ou modifique o seu valor se já existir, por exemplo,

    | Chave | Valor |
    |---|---|
    | TestApp:Definições:Sentinela | v1 |


7. Refresque o navegador algumas vezes. Quando a definição em cache expirar após 30 segundos, a página mostra a resposta da chamada Funções com valor atualizado.

    ![Função quickstart refresca local](./media/quickstarts/dotnet-core-function-refresh-local.png)

> [!NOTE]
> O código de exemplo utilizado neste tutorial pode ser descarregado a partir do [repo GitHub de Configuração de Aplicações.](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, permitiu que a sua aplicação Azure Functions atualizasse dinamicamente as definições de configuração a partir da Configuração da Aplicação. Para aprender a usar uma identidade gerida pelo Azure para agilizar o acesso à Configuração de Aplicações, continue até ao próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerida](./howto-integrate-azure-managed-service-identity.md)

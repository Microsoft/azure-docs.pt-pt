---
title: Quickstart para Configuração de Aplicativos Azure com Funções Azure Microsoft Docs
description: Neste quickstart, faça uma aplicação Azure Functions com Configuração de Aplicação Azure e C#. Criar e ligar a uma loja de Configuração de Aplicações. Teste a função localmente.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: 4197891949062123042736e578cfbcc5def4e1f9
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930811"
---
# <a name="quickstart-create-an-azure-functions-app-with-azure-app-configuration"></a>Quickstart: Criar uma app Azure Functions com Configuração de Aplicações Azure

Neste arranque rápido, incorpora o serviço de Configuração de Aplicações Azure numa aplicação Azure Functions para centralizar o armazenamento e gestão de todas as definições da sua aplicação separadas do seu código.

## <a name="prerequisites"></a>Pré-requisitos

- Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/dotnet)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) com a carga de trabalho **de desenvolvimento Azure.**
- [Ferramentas Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Criar uma loja de configuração de aplicativos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Selecione **Configuration Explorer**  >  **+ Crie** valor  >  **chave** para adicionar os seguintes pares de valor-chave:

    | Chave | Valor |
    |---|---|
    | TestApp:Definições:Mensagem | Dados da Configuração da Aplicação Azure |

    Deixe **a etiqueta** e o tipo de **conteúdo** vazios por enquanto.

8. Selecione **Aplicar**.

## <a name="create-a-functions-app"></a>Criar uma aplicação De Funções

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Conecte-se a uma loja de configuração de aplicativos

1. Clique com o botão direito no seu projeto e **selecione Gerir Pacotes NuGet**. No separador **Procurar,** procure e adicione o `Microsoft.Extensions.Configuration.AzureAppConfiguration` pacote NuGet ao seu projeto. Se não conseguir encontrá-lo, selecione a caixa de verificação **de pré-escoam.**

2. Abra *Function1.cs*, e adicione os espaços de nome da configuração .NET Core e do fornecedor de configuração de configuração de aplicação de aplicações.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

3. Adicione uma `static` propriedade nomeada para criar uma instância singleton de `Configuration` `IConfiguration` . Em seguida, adicione um `static` construtor para ligar à Configuração da Aplicação chamando `AddAzureAppConfiguration()` . Isto carregará a configuração uma vez no arranque da aplicação. A mesma instância de configuração será usada para todas as chamadas de Funções mais tarde.

    ```csharp
    private static IConfiguration Configuration { set; get; }

    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));
        Configuration = builder.Build();
    }
    ```

4. Atualize o `Run` método para ler os valores a partir da configuração.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string keyName = "TestApp:Settings:Message";
        string message = Configuration[keyName];

        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>Testar localmente a função

1. Desaprote uma variável ambiental chamada **ConnectionString** e desaprote-a na chave de acesso à sua loja de Configuração de Aplicações. Se utilizar o pedido de comando do Windows, executar o seguinte comando e reiniciar a solicitação de comando para permitir que a alteração entre em vigor:

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Se utilizar o Windows PowerShell, executar o seguinte comando:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Se utilizar o macOS ou o Linux, executar o seguinte comando:

    ```bash
        export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Prima F5 para testar a sua função. Se solicitado, aceite o pedido do Visual Studio para descarregar e instalar **ferramentas Azure Functions Core (CLI).** Também pode ser necessário ativar uma exceção de firewall para que as ferramentas possam lidar com pedidos HTTP.

3. Copie o URL da sua função na saída do tempo de execução das funções do Azure.

    ![Função quickstart depurando em VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Cole o URL do pedido HTTP na barra de endereço do browser. A imagem a seguir mostra a resposta no navegador ao pedido GET local devolvido pela função.

    ![Quickstart Function lançar local](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou uma nova loja de Configuração de Aplicações e utilizou-a com uma aplicação Azure Functions através do fornecedor de Configuração de [Aplicações.](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration) Para aprender a configurar a sua app Azure Functions para atualizar dinamicamente as definições de configuração, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Ativar a configuração dinâmica](./enable-dynamic-configuration-azure-functions-csharp.md)
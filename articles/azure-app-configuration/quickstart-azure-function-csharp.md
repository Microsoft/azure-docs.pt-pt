---
title: Início rápido para a configuração de aplicações do Azure com as funções do Azure | Documentos da Microsoft
description: Guia de introdução para utilizar a configuração de aplicações do Azure com as funções do Azure.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: Azure Functions
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 5f28e213a5f824562df62a05b98f0f92f71bc591
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957441"
---
# <a name="quickstart-create-an-azure-function-with-app-configuration"></a>Início rápido: Criar uma função do Azure com configuração de aplicações

Configuração de aplicações do Azure é um serviço de configuração gerida no Azure. Permite-lhe armazenar e gerir todas as suas definições de aplicação num único local que é separada do seu código facilmente. Este guia de introdução mostra-lhe como incorporar o serviço numa função do Azure. 

Pode utilizar qualquer editor de código para concluir os passos deste início rápido. No entanto, o [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção, disponível nas plataformas Windows, macOS e Linux.

![Início rápido concluído no local](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, instale [Visual Studio 2017](https://visualstudio.microsoft.com/vs) (e certifique-se de que o **desenvolvimento do Azure** carga de trabalho também é instalada) e o [mais recentes ferramentas de funções do Azure](../azure-functions/functions-develop-vs.md#check-your-tools-version).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Criar um arquivo de configuração de aplicação

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-function-app"></a>Criar uma aplicação de função

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-app-configuration-store"></a>Ligar ao arquivo de configuração de aplicação

1. Open *Function1.cs* e adicione uma referência ao fornecedor de configuração de configuração de aplicações .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

2. Atualização do `Run` método a utilizar a configuração de aplicações ao chamar `builder.AddAzureAppConfiguration()`.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));
        var config = builder.Build();
        string message = config["TestApp:Settings:Message"];
        message = message ?? req.Query["message"];

        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
        message = message ?? data?.message;

        return message != null
            ? (ActionResult) new OkObjectResult(message)
            : new BadRequestObjectResult("Please pass a message from a configuration store, on the query string or in the request body");
    }
    ```

## <a name="test-the-function-locally"></a>Testar localmente a função

1. Definir uma variável de ambiente com o nome **ConnectionString** e defini-lo para a chave de acesso ao seu arquivo de configuração de aplicação. Se estiver a utilizar o Prompt de comando do Windows, execute o seguinte comando e reinicie o Prompt de comando para permitir que a alteração tenha efeito:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se estiver a utilizar o Windows PowerShell, execute o seguinte comando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Se estiver a utilizar o macOS ou Linux, execute o seguinte comando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Para testar a sua função, prima **F5**. Se lhe for solicitado, aceite o pedido do Visual Studio para transferir e instalar **núcleo de funções do Azure (CLI)** ferramentas. Também poderá ativar a exceção da firewall para que todas as ferramentas possam aceitar os pedidos de HTTP.

3. Copie o URL da sua função na saída do tempo de execução das funções do Azure.

    ![Função de início rápido de depuração no VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Cole o URL do pedido HTTP na barra de endereço do browser. O exemplo apresentado em seguida mostra a resposta no browser relativamente ao pedido GET devolvido retornado pela função:

    ![Local de lançamento de função de início rápido](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou um novo arquivo de configuração de aplicação e Use com uma função do Azure. Para saber mais sobre como utilizar a configuração de aplicações, avance para o próximo tutorial que demonstra a autenticação.

> [!div class="nextstepaction"]
> [Identidades geridas para a integração de recursos do Azure](./integrate-azure-managed-service-identity.md)

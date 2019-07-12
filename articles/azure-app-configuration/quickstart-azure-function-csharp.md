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
ms.openlocfilehash: a4900964fb6feeb4c7cb0f147d3681031cac6a7b
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798434"
---
# <a name="quickstart-create-an-azure-function-with-app-configuration"></a>Início rápido: Criar uma função do Azure com configuração de aplicações

Configuração de aplicações do Azure é um serviço de configuração gerida no Azure. Pode usá-lo facilmente armazenar e gerir todas as suas definições de aplicação num único local que é separada a partir do código. Este guia de introdução mostra-lhe como incorporar o serviço numa função do Azure. 

Pode utilizar qualquer editor de código para realizar os passos neste guia de introdução. [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção disponível no Windows, macOS e plataformas Linux.

![Local completa do guia de introdução](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="prerequisites"></a>Pré-requisitos

Para fazer este início rápido, instale [Visual Studio 2019](https://visualstudio.microsoft.com/vs). Certifique-se de que o **desenvolvimento do Azure** carga de trabalho também é instalada. Também instalar o [mais recentes ferramentas de funções do Azure](../azure-functions/functions-develop-vs.md#check-your-tools-version).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Criar um arquivo de configuração de aplicação

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione **Explorador de configuração** >  **+ criar** para adicionar os seguintes pares de chave-valor:

    | Chave | Valor |
    |---|---|
    | TestApp:Settings:Message | Dados de configuração de aplicações do Azure |

    Deixe **rótulo** e **tipo de conteúdo** branco por agora.

## <a name="create-a-function-app"></a>Criar uma aplicação de função

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Ligar a um arquivo de configuração de aplicação

1. Clique no projeto e selecione **gerir pacotes NuGet**. Sobre o **procurar** separador, procure e adicione os seguintes pacotes NuGet ao seu projeto. Se não é possível encontrá-los, selecione o **incluir pré-lançamento** caixa de verificação.

    ```
    Microsoft.Extensions.Configuration.AzureAppConfiguration 2.0.0-preview-009200001-1437 or later
    ```

2. Open *Function1.cs*e adicione uma referência para o fornecedor de configuração de aplicações do .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

3. Atualização do `Run` método a utilizar a configuração de aplicações ao chamar `builder.AddAzureAppConfiguration()`.

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

1. Definir uma variável de ambiente com o nome **ConnectionString**e defina-o para a chave de acesso ao seu arquivo de configuração de aplicação. Se utilizar a linha de comandos do Windows, execute o seguinte comando e reinicie o prompt de comando para permitir que a alteração tenha efeito:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se utilizar o Windows PowerShell, execute o seguinte comando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Se usar o macOS ou Linux, execute o seguinte comando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Para testar a sua função, prima F5. Se lhe for solicitado, aceite o pedido do Visual Studio para transferir e instalar **núcleo de funções do Azure (CLI)** ferramentas. Poderá também ter de ativar a exceção da firewall para que as ferramentas podem processar pedidos de HTTP.

3. Copie o URL da sua função na saída do tempo de execução das funções do Azure.

    ![Função de início rápido de depuração no VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Cole o URL do pedido HTTP na barra de endereço do browser. A imagem seguinte mostra a resposta no browser para relativamente ao pedido GET devolvido pela função.

    ![Local de lançamento de função de início rápido](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, criou um novo arquivo de configuração de aplicação e Use com uma função do Azure. Para saber mais sobre como utilizar a configuração de aplicações, avance para o próximo tutorial que demonstra a autenticação.

> [!div class="nextstepaction"]
> [Integração de identidade gerida](./howto-integrate-azure-managed-service-identity.md)

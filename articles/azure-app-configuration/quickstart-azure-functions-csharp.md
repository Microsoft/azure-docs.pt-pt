---
title: Início rápido para configuração de Azure App com Azure Functions | Microsoft Docs
description: Um guia de início rápido para usar Azure App configuração com Azure Functions.
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
ms.openlocfilehash: 6329cf0e74bbcf57164afeab5b04e2af4ee43943
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186196"
---
# <a name="quickstart-create-an-azure-functions-app-with-azure-app-configuration"></a>Início rápido: criar um aplicativo Azure Functions com a configuração Azure App

Neste guia de início rápido, você incorpora o serviço de configuração Azure App em um aplicativo Azure Functions para centralizar o armazenamento e o gerenciamento de todas as suas configurações de aplicativo separadas do seu código.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) com a carga de trabalho de **desenvolvimento do Azure** .
- [Ferramentas de Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Criar um repositório de configurações de aplicativo

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione **Configuration Explorer** >  **+ criar** para adicionar os seguintes pares de chave-valor:

    | Chave | Valor |
    |---|---|
    | TestApp: configurações: mensagem | Dados da configuração Azure App |

    Deixe **rótulo** e **tipo de conteúdo** vazio por enquanto.

## <a name="create-a-functions-app"></a>Criar um aplicativo de funções

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Conectar-se a um repositório de configuração de aplicativo

1. Clique com o botão direito do mouse em seu projeto e selecione **gerenciar pacotes NuGet**. Na guia **procurar** , pesquise e adicione os seguintes pacotes NuGet ao seu projeto. Se você não conseguir encontrá-los, marque a caixa de seleção **incluir pré-lançamento** .

    ```
    Microsoft.Extensions.Configuration.AzureAppConfiguration 2.1.0-preview-010380001-1099 or later
    ```

2. Abra *function1.cs*e adicione os namespaces da configuração do .NET Core e o provedor de configuração de configuração do aplicativo.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```
3. Adicione uma propriedade de `static` `Configuration` para criar uma instância singleton do `IConfiguration`. Em seguida, adicione um Construtor `static` para se conectar à configuração do aplicativo chamando `AddAzureAppConfiguration()`. Isso carregará a configuração uma vez na inicialização do aplicativo. A mesma instância de configuração será usada para todas as chamadas de funções posteriormente.

    ```csharp
    private static IConfiguration Configuration { set; get; }

    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));
        Configuration = builder.Build();
    }
    ```
4. Atualize o método `Run` para ler valores da configuração.

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

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou um novo repositório de configuração de aplicativo e o utilizou com um aplicativo Azure Functions por meio do [provedor de configuração de aplicativo](https://go.microsoft.com/fwlink/?linkid=2074664). Para saber como configurar seu aplicativo Azure Functions para atualizar dinamicamente as definições de configuração, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Habilitar configuração dinâmica](./enable-dynamic-configuration-azure-functions-csharp.md)

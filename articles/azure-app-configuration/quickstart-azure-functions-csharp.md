---
title: Quickstart para configuração de aplicações Azure com funções Azure  Microsoft Docs
description: Um arranque rápido para usar a configuração da aplicação Azure com funções Azure.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 1/9/2019
ms.author: lcozzens
ms.openlocfilehash: 71a330523f1d3393a365fec29fb66f5c9773b6cc
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2020
ms.locfileid: "78207069"
---
# <a name="quickstart-create-an-azure-functions-app-with-azure-app-configuration"></a>Quickstart: Criar uma aplicação de funções Azure com configuração de aplicações azure

Neste arranque rápido, incorpora o serviço de Configuração de Aplicações Azure numa aplicação De Funções Azure para centralizar o armazenamento e gestão de todas as definições da sua aplicação separadas do seu código.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Estúdio Visual 2019](https://visualstudio.microsoft.com/vs) com a carga de trabalho de **desenvolvimento azure.**
- [Ferramentas de funções azure](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Criar uma loja de configuração de aplicações

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione O Explorador de **Configuração** >  **+ Criar** para adicionar os seguintes pares de valor-chave:

    | Chave | Valor |
    |---|---|
    | TestApp:Definições:Mensagem | Dados da Configuração de Aplicações Azure |

    Deixe o **rótulo** e o **tipo de conteúdo** vazios por enquanto.

## <a name="create-a-functions-app"></a>Criar uma aplicação Funções

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Ligar a uma loja de configuração de aplicações

1. Clique no seu projeto e selecione **Gerir pacotes NuGet**. No separador **Browse,** procure e adicione o pacote NuGet `Microsoft.Extensions.Configuration.AzureAppConfiguration` ao seu projeto. Se não conseguir encontrá-la, selecione a caixa de verificação **de pré-lançamento Incluir.**

2. Abra *Function1.cs*e adicione os espaços de nome da configuração .NET Core e do fornecedor de configuração de configuração de aplicações.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

3. Adicione uma propriedade `static` chamada `Configuration` para criar uma instância singleton de `IConfiguration`. Em seguida, adicione um `static` construtor para ligar à Configuração da Aplicação, chamando `AddAzureAppConfiguration()`. Isto carregará a configuração uma vez no arranque da aplicação. A mesma configuração será usada para todas as chamadas funções posteriormente.

    ```csharp
    private static IConfiguration Configuration { set; get; }

    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));
        Configuration = builder.Build();
    }
    ```

4. Atualize o método `Run` para ler valores a partir da configuração.

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

1. Detete uma variável ambiental chamada **ConnectionString**e detetete-a na chave de acesso à sua loja de configuração de aplicações. Se utilizar o pedido de comando do Windows, execute o seguinte comando e reinicie a solicitação de comando para permitir que a alteração faça efeito:

    ```CLI
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Se utilizar o Windows PowerShell, execute o seguinte comando:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Se utilizar macOS ou Linux, execute o seguinte comando:

    ```bash
        export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Pressione F5 para testar a sua função. Se solicitado, aceite o pedido do Estúdio Visual para descarregar e instalar **ferramentas Do Núcleo de Funções Azure (CLI).** Também pode ser necessário permitir uma exceção à firewall para que as ferramentas possam lidar com pedidos HTTP.

3. Copie o URL da sua função na saída do tempo de execução das funções do Azure.

    ![Depuração da função Quickstart em VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Cole o URL do pedido HTTP na barra de endereço do browser. A imagem que se segue mostra a resposta no navegador ao pedido de GET local devolvido pela função.

    ![Quickstart Function lançar local](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou uma nova loja de configuração de aplicações e utilizou-a com uma aplicação De Funções Azure através do fornecedor de Configuração de [Aplicações.](https://go.microsoft.com/fwlink/?linkid=2074664) Para aprender a configurar a sua aplicação Funções Azure para atualizar dinamicamente as configurações de configuração, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Ativar a configuração dinâmica](./enable-dynamic-configuration-azure-functions-csharp.md)

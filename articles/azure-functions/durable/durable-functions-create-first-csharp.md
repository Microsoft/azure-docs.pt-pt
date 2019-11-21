---
title: Create your first durable function in Azure using C#
description: Create and publish an Azure Durable Function using Visual Studio.
author: jeffhollan
ms.topic: quickstart
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 6b3ead9eefd6f0d4c504cc7711ea4e03facf8edc
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231517"
---
# <a name="create-your-first-durable-function-in-c"></a>Create your first durable function in C\#

*Durable Functions* is an extension of [Azure Functions](../functions-overview.md) that lets you write stateful functions in a serverless environment. A extensão gere o estado, os pontos de verificação e os reinícios por si.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

In this article, you learn how to use the Visual Studio 2019 to locally create and test a "hello world" durable function.  This function orchestrates and chains-together calls to other functions. Em seguida, publique o código de função no Azure. These tools are available as part of the Azure development workload in Visual Studio 2019.

![Running durable function in Azure](./media/durable-functions-create-first-csharp/functions-vs-complete.png)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* Install [Visual Studio 2019](https://visualstudio.microsoft.com/vs/). Make sure that the **Azure development** workload is also installed. Visual Studio 2017 also supports Durable Functions development, but the UI and steps differ.

* Verify you have the [Azure Storage Emulator](../../storage/common/storage-use-emulator.md) installed and running.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Criar um projeto de aplicação de função

The Azure Functions template creates a project that can be published to a function app in Azure. As aplicações de funções permitem-lhe agrupar funções como unidades lógicas para a gestão, implementação e partilha de recursos.

1. No Visual Studio, selecione **Novo** > **Projeto**, no menu **Ficheiro**.

1. In the **Add a new project** dialog, search for `functions`, choose the **Azure Functions** template, and select **Next**. 

    ![Caixa de diálogo Novo projeto para criar uma função no Visual Studio](./media/durable-functions-create-first-csharp/functions-vs-new-project.png)

1. Type a **Project name** for your project, and select **OK**. The project name must be valid as a C# namespace, so don't use underscores, hyphens, or any other nonalphanumeric characters.

1. In **Create a new Azure Functions Application**, use the settings specified in the table that follows the image.

    ![Create a new Azure Functions Application dialog in Visual Studio](./media/durable-functions-create-first-csharp/functions-vs-new-function.png)

    | Definição      | Valor sugerido  | Descrição                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Versão** | Azure Functions 2.0 <br />(.NET Core) | Creates a function project that uses the version 2.0 runtime of Azure Functions, which supports .NET Core. Azure Functions 1.0 supports the .NET Framework. Para obter mais informações, veja [How to target Azure Functions runtime version](../functions-versions.md) (Como segmentar a versão do runtime das Funções do Azure).   |
    | **Modelo** | Empty | Creates an empty function app. |
    | **Conta de armazenamento**  | Emulador do Armazenamento | A storage account is required for durable function state management. |

4. Select **Create** to create an empty function project. This project has the basic configuration files needed to run your functions.

## <a name="add-functions-to-the-app"></a>Add functions to the app

The following steps use a template to create the durable function code in your project.

1. Right-click the project in Visual Studio and select **Add** > **New Azure Function**.

    ![Add new function](./media/durable-functions-create-first-csharp/functions-vs-add-new-function.png)

1. Verify **Azure Function** is selected from the add menu, type a name for your C# file, and then select **Add**.

1. Select the **Durable Functions Orchestration** template and then select **Ok**

    ![Select durable template](./media/durable-functions-create-first-csharp/functions-vs-select-template.png)  

> [!NOTE]
> This template currently creates a durable function using an older 1.x version of the extension. See the [Durable Functions Versions](durable-functions-versions.md) article for information about how to upgrade to the newer 2.x versions of Durable Functions.

A new durable function is added to the app.  Open the new .cs file to view the contents. This durable function is a simple function chaining example with the following methods:  

| Método | FunctionName | Descrição |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `<file-name>` | Manages the durable orchestration. In this case, the orchestration starts, creates a list, and adds the result of three functions calls to the list.  When the three function calls are complete, it returns the list. |
| **`SayHello`** | `<file-name>_Hello` | The function returns a hello. It is the function that contains the business logic that is being orchestrated. |
| **`HttpStart`** | `<file-name>_HttpStart` | An [HTTP-triggered function](../functions-bindings-http-webhook.md) that starts an instance of the orchestration and returns a check status response. |

Now that you've created your function project and a durable function, you can test it on your local computer.

## <a name="test-the-function-locally"></a>Testar localmente a função

As Ferramentas de Núcleo das Funções do Azure permitem-lhe executar um projeto de funções do Azure no seu computador de programação local. Deverá instalar essas ferramentas da primeira vez que iniciar uma função do Visual Studio.

1. Para testar a sua função, prima F5. Se solicitado, aceite o pedido do Visual Studio para transferir e instalar as ferramentas de Núcleo das Funções do Azure (CLI). Também poderá ativar a exceção da firewall para que todas as ferramentas possam aceitar os pedidos de HTTP.

2. Copie o URL da sua função na saída do tempo de execução das funções do Azure.

    ![Tempo de execução local do Azure](./media/durable-functions-create-first-csharp/functions-vs-debugging.png)

3. Paste the URL for the HTTP request into your browser's address bar and execute the request. O exemplo apresentado em seguida mostra a resposta no browser relativamente ao pedido GET devolvido retornado pela função:

    ![Resposta da função localhost no browser](./media/durable-functions-create-first-csharp/functions-vs-status.png)

    The response is the initial result from the HTTP function letting us know the durable orchestration has started successfully.  It is not yet the end result of the orchestration.  The response includes a few useful URLs.  For now, let's query the status of the orchestration.

4. Copy the URL value for `statusQueryGetUri` and pasting it in the browser's address bar and execute the request.

    The request will query the orchestration instance for the status. You should get an eventual response that looks like the following.  This output shows us the instance has completed, and includes the outputs or results of the durable function.

    ```json
    {
        "instanceId": "d495cb0ac10d4e13b22729c37e335190",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2019-11-02T07:07:40Z",
        "lastUpdatedTime": "2019-11-02T07:07:52Z"
    }
    ```

5. Para parar a depuração, prima **Shift + F5**.

Depois de verificar que a função é executada corretamente no computador local, deve publicar o projeto no Azure.

## <a name="publish-the-project-to-azure"></a>Publicar o projeto no Azure

Deverá ter uma aplicação de funções na sua subscrição do Azure antes de poder publicar o seu projeto. Pode criar uma aplicação de funções diretamente a partir do Visual Studio.

[!INCLUDE [Publish the project to Azure](../../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testar a sua função no Azure

1. Copie a URL de base da aplicação de funções a partir da página de perfil Publicar. Substitua a `localhost:port` parte da URL que utilizou ao testar a localmente a função com a nova URL de base.

    The URL that calls your durable function HTTP trigger should be in the following format:

        http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>_HttpStart

2. Cole este novo URL do pedido HTTP na barra de endereço do browser. You should get the same status response as before when using the published app.

## <a name="next-steps"></a>Passos seguintes

You have used Visual Studio to create and publish a C# durable function app.

> [!div class="nextstepaction"]
> [Learn about common durable function patterns](durable-functions-overview.md#application-patterns)
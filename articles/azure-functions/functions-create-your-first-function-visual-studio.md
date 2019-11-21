---
title: Criar a sua primeira função no Azure com o Visual Studio
description: Criar e publicar uma Função do Azure acionada por HTTP com o Visual Studio.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 07/19/2019
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: 6d09453db0fdf8c09ad6d90c63b27f48508302be
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230716"
---
# <a name="create-your-first-function-using-visual-studio"></a>Criar a sua primeira função com o Visual Studio

As Funções do Azure permitem-lhe executar o seu código num ambiente [sem servidor](https://azure.microsoft.com/solutions/serverless/), sem que tenha de criar primeiro uma VM ou publicar uma aplicação Web.

In this article, you learn how to use Visual Studio 2019 to locally create and test a "hello world" function and then publish it to Azure. This quickstart is designed for Visual Studio 2019. When creating a Functions project using Visual Studio 2017, you must first install the [latest Azure Functions tools](functions-develop-vs.md#check-your-tools-version).

![Resposta da função localhost no browser](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

## <a name="prerequisites"></a>Pré-requisitos

To complete this tutorial, you must first install [Visual Studio 2019](https://azure.microsoft.com/downloads/). Make sure that the **Azure development** workload is also installed.

![Install Visual Studio with the Azure development workload](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Criar um projeto de aplicação de função

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio creates a project and class that contains boilerplate code for the HTTP trigger function type. The `FunctionName` attribute on the method sets the name of the function, which by default is `HttpTrigger`. The `HttpTrigger` attribute specifies that the function is triggered by an HTTP request. O código automático envia uma resposta HTTP que inclui um valor da cadeia de consulta ou de corpo de pedido.

You can expand the capabilities of your function using input and output bindings by applying the appropriate attributes to the method. Para obter mais informações, veja a secção [Acionadores e enlaces](functions-dotnet-class-library.md#triggers-and-bindings) da [Referência para Programadores do Funções do Azure C#](functions-dotnet-class-library.md).

Agora que criou o projeto de função e uma função acionada por HTTP, pode testá-la no seu computador local.

## <a name="run-the-function-locally"></a>Executar localmente a função

Visual Studio integrates with Azure Functions Core Tools so that you can test your functions locally using the full Functions runtime.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Depois de verificar que a função é executada corretamente no computador local, deve publicar o projeto no Azure.

## <a name="publish-the-project-to-azure"></a>Publicar o projeto no Azure

Deverá ter uma aplicação de funções na sua subscrição do Azure antes de poder publicar o seu projeto. Visual Studio publishing creates a function app for you the first time you publish your project.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testar a sua função no Azure

1. Copie a URL de base da aplicação de funções a partir da página de perfil Publicar. Substitua a `localhost:port` parte da URL que utilizou ao testar a localmente a função com a nova URL de base. Tal como antes, certifique-se de que anexa a cadeia de consulta `?name=<YOUR_NAME>` a este URL e execute o pedido.

    O URL que chama a função de acionada por HTTP deve estar no seguinte formato:

        http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME> 

2. Cole este novo URL do pedido HTTP na barra de endereço do browser. A imagem seguinte mostra a resposta no browser em relação ao pedido GET devolvido pela função:

    ![Resposta da função no browser](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="next-steps"></a>Passos seguintes

You have used Visual Studio to create and publish a C# function app in Azure with a simple HTTP triggered function. To learn more about developing functions as .NET class libraries, see [Azure Functions C# developer reference](functions-dotnet-class-library.md).

> [!div class="nextstepaction"]
> [Add an Azure Storage queue binding to your function](functions-add-output-binding-storage-queue-vs.md)

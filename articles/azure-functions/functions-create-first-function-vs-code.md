---
title: Criar a sua primeira função no Azure com o Visual Studio Code
description: Criar e publicar no Azure uma simples função ativada por HTTP com a extensão das Funções do Azure no Visual Studio Code.
ms.topic: quickstart
ms.date: 06/25/2019
ms.custom: mvc, devcenter
ms.openlocfilehash: b89e6d75cd82a65dfbce29b949c4b7d463582bb9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230788"
---
# <a name="create-your-first-function-using-visual-studio-code"></a>Criar a sua primeira função com o Visual Studio Code

As Funções do Azure permitem-lhe executar o seu código num ambiente [sem servidor](https://azure.microsoft.com/solutions/serverless/), sem que tenha de criar primeiro uma VM ou publicar uma aplicação Web.

Neste artigo, vai aprender a utilizar a [Extensão das Funções do Azure para o Visual Studio Code], para criar e testar uma função "hello world" no seu computador local com o Microsoft Visual Studio Code. Em seguida, publique o código de função no Azure a partir do Visual Studio Code.

![Código de funções do Azure num projeto do Visual Studio](./media/functions-create-first-function-vs-code/functions-vscode-intro.png)

The extension currently supports C#, JavaScript, Java, and Python functions. The steps in this article and the article that follows support only JavaScript and C# functions. To learn how to use Visual Studio Code to create and publish Python functions, see [Create and deploy serverless Azure Functions in Python with Visual Studio Code](/azure/python/tutorial-vs-code-serverless-python-01). To learn how to use Visual Studio Code to create and publish PowerShell functions, see [Create your first PowerShell function in Azure](functions-create-first-function-powershell.md). 

A extensão está atualmente em pré-visualização. Para obter mais informações, veja a página de extensão [Extensão das Funções do Azure para o Visual Studio Code].

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

* Instale o [Visual Studio Code](https://code.visualstudio.com/) numa das [plataformas suportadas](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Install version 2.x of the [Azure Functions Core Tools](functions-run-local.md#v2).

* Instale os requisitos específicos para a linguagem escolhida:

    | Linguagem | Requisito |
    | -------- | --------- |
    | **C#** | [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)  |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> | 
 
    <sup>*</sup>Active LTS and Maintenance LTS versions (8.11.1 and 10.14.1 recommended).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../includes/functions-create-function-app-vs-code.md)]

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Depois de verificar que a função é executada corretamente no computador local, deve publicar o projeto no Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>Run the function in Azure

1. Copie o URL do acionador de HTTP no painel **Resultados**. This URL includes the function key, which is passed to the `code` query parameter. Tal como antes, certifique-se de que adiciona a cadeia de consulta `?name=<yourname>` ao final deste URL e execute o pedido.

    O URL que chama a função acionada por HTTP deve estar no seguinte formato:

        http://<functionappname>.azurewebsites.net/api/<functionname>?code=<function_key>&name=<yourname> 

1. Cole este novo URL do pedido HTTP na barra de endereço do browser. A imagem seguinte mostra a resposta no browser em relação ao pedido GET devolvido pela função: 

    ![Resposta da função no browser](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="next-steps"></a>Passos seguintes

Utilizou o Visual Studio Code para criar uma aplicação de funções com uma função simples acionada por HTTP. In the next article, you expand that function by adding an output binding. This binding writes the string from the HTTP request to a message in an Azure Queue Storage queue. The next article also shows you how to clean up these new Azure resources by removing the resource group you created.

> [!div class="nextstepaction"]
> [Add an Azure Storage queue binding to your function](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Extensão das Funções do Azure para o Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions

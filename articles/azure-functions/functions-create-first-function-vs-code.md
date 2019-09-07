---
title: Criar a sua primeira função no Azure com o Visual Studio Code
description: Criar e publicar no Azure uma simples função ativada por HTTP com a extensão das Funções do Azure no Visual Studio Code.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: funções do azure, funções, processamento de eventos, computação, arquitetura sem servidor
ms.service: azure-functions
ms.topic: quickstart
ms.date: 06/25/2019
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: 52f682f7c7f06056be122b33d27592a55a01be94
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70744077"
---
# <a name="create-your-first-function-using-visual-studio-code"></a>Criar a sua primeira função com o Visual Studio Code

As Funções do Azure permitem-lhe executar o seu código num ambiente [sem servidor](https://azure.microsoft.com/solutions/serverless/), sem que tenha de criar primeiro uma VM ou publicar uma aplicação Web.

Neste artigo, vai aprender a utilizar a [Extensão das Funções do Azure para o Visual Studio Code], para criar e testar uma função "hello world" no seu computador local com o Microsoft Visual Studio Code. Em seguida, publique o código de função no Azure a partir do Visual Studio Code.

![Código de funções do Azure num projeto do Visual Studio](./media/functions-create-first-function-vs-code/functions-vscode-intro.png)

Atualmente, a extensão C#dá suporte a funções JavaScript, Java e Python. As etapas neste artigo e o artigo a seguir dão suporte apenas a JavaScript C# e funções. Para saber como usar Visual Studio Code para criar e publicar funções Python, consulte [implantar Python no Azure Functions](https://code.visualstudio.com/docs/python/tutorial-azure-functions). Para saber como usar Visual Studio Code para criar e publicar funções do PowerShell, consulte [criar sua primeira função do PowerShell no Azure](functions-create-first-function-powershell.md). 

A extensão está atualmente em pré-visualização. Para obter mais informações, veja a página de extensão [Extensão das Funções do Azure para o Visual Studio Code].

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

* Instale o [Visual Studio Code](https://code.visualstudio.com/) numa das [plataformas suportadas](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Instale a versão 2. x do [Azure Functions Core Tools](functions-run-local.md#v2).

* Instale os requisitos específicos para a linguagem escolhida:

    | Idioma | Requisito |
    | -------- | --------- |
    | **C#** | [C#extensão](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)  |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> | 
 
    <sup>*</sup>Active LTS e manutenção LTS versões (8.11.1 e 10.14.1 recomendado).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../includes/functions-create-function-app-vs-code.md)]

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Depois de verificar que a função é executada corretamente no computador local, deve publicar o projeto no Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>Executar a função no Azure

1. Copie o URL do acionador de HTTP no painel **Resultados**. Essa URL inclui a chave de função, que é passada para `code` o parâmetro de consulta. Tal como antes, certifique-se de que adiciona a cadeia de consulta `?name=<yourname>` ao final deste URL e execute o pedido.

    O URL que chama a função acionada por HTTP deve estar no seguinte formato:

        http://<functionappname>.azurewebsites.net/api/<functionname>?code=<function_key>&name=<yourname> 

1. Cole este novo URL do pedido HTTP na barra de endereço do browser. A imagem seguinte mostra a resposta no browser em relação ao pedido GET devolvido pela função: 

    ![Resposta da função no browser](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="next-steps"></a>Passos Seguintes

Utilizou o Visual Studio Code para criar uma aplicação de funções com uma função simples acionada por HTTP. No próximo artigo, você expande essa função adicionando uma associação de saída. Essa associação grava a cadeia de caracteres da solicitação HTTP para uma mensagem em uma fila de armazenamento de filas do Azure. O próximo artigo também mostra como limpar esses novos recursos do Azure removendo o grupo de recursos que você criou.

> [!div class="nextstepaction"]
> [Adicionar uma associação de fila de armazenamento do Azure à sua função](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Extensão das Funções do Azure para o Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions

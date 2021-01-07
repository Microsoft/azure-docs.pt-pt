---
title: Criar uma função C# utilizando código de estúdio visual - Funções Azure
description: Aprenda a criar uma função C# e, em seguida, publique o projeto local para hospedagem sem servidor em Funções Azure utilizando a extensão de Funções Azure no Código do Estúdio Visual.
ms.topic: quickstart
ms.date: 11/03/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 791416a54fa75091facf1f7bc2aadf6fccf54b05
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2021
ms.locfileid: "97968624"
---
# <a name="quickstart-create-a-c-function-in-azure-using-visual-studio-code"></a>Quickstart: Criar uma função C# em Azure utilizando o Código do Estúdio Visual

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

Neste artigo, utiliza o Código do Estúdio Visual para criar uma função baseada na biblioteca de classe C# que responda aos pedidos HTTP. Depois de testar o código localmente, implemente-o para o ambiente sem servidor das Funções Azure.

Completar este quickstart incorre num pequeno custo de alguns usd ou menos na sua conta Azure.

Há também uma [versão baseada em CLI](create-first-function-cli-csharp.md) deste artigo.

## <a name="configure-your-environment"></a>Configurar o ambiente

Antes de começar, certifique-se de que tem os seguintes requisitos em vigor:

+ Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ A versão 3.x [das Ferramentas Principais do Azure Functions.](functions-run-local.md#install-the-azure-functions-core-tools)

+ [Código visual do estúdio](https://code.visualstudio.com/) numa das [plataformas suportadas.](https://code.visualstudio.com/docs/supporting/requirements#_platforms)

+ A [extensão C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) para Código de Estúdio Visual.  

+ A [extensão de Funções Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Código de Estúdio Visual.

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Crie o seu projeto local

Nesta secção, você usa Visual Studio Code para criar um projeto local de Funções Azure em C#. Mais tarde neste artigo, publicará o seu código de função para Azure.

1. Escolha o ícone Azure na barra de Atividade, em seguida, na área **Azure: Funções,** selecione o **novo projeto...** ícone.

    ![Escolha Criar um novo projeto](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Escolha um local de diretório para o seu espaço de trabalho do projeto e escolha **Select**.

    > [!NOTE]
    > Estes passos foram concebidos para serem concluídos fora de um espaço de trabalho. Neste caso, não selecione uma pasta de projeto que faz parte de uma área de trabalho.

1. Fornecer as seguintes informações nas instruções:

    + **Selecione um idioma para o seu projeto de função**: Escolha `C#` .

    + **Selecione um modelo para a primeira função do seu projeto**: Escolha `HTTP trigger` .

    + **Fornecer um nome de função**: `HttpExample` Digite .

    + **Fornecer um espaço de nome :** Tipo `My.Functions` .

    + **Nível de autorização**: `Anonymous` Escolha, que permite a qualquer pessoa ligar para o ponto final da sua função. Para saber mais sobre o nível de autorização, consulte [as teclas de Autorização](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Selecione como pretende abrir o seu projeto**: Escolha `Add to workspace` .

1. Utilizando esta informação, o Visual Studio Code gera um projeto Azure Functions com um gatilho HTTP. Pode ver os ficheiros de projeto locais no Explorer. Para saber mais sobre ficheiros que são criados, consulte [os ficheiros do projeto Gerados.](functions-develop-vs-code.md#generated-project-files)

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Depois de verificar que a função funciona corretamente no seu computador local, é hora de usar o Código do Estúdio Visual para publicar o projeto diretamente para o Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Passos seguintes

Utilizou o Visual Studio Code para criar uma aplicação de funções com uma função simples acionada por HTTP. No artigo seguinte, expande essa função adicionando uma ligação de saída. Esta ligação escreve o string do pedido HTTP para uma mensagem numa fila de armazenamento de fila Azure. 

> [!div class="nextstepaction"]
> [Ligue-se a uma fila de armazenamento Azure](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-csharp)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions

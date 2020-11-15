---
title: Criar uma função PowerShell utilizando código de estúdio visual - Funções Azure
description: Aprenda a criar uma função PowerShell e, em seguida, publique o projeto local para hospedagem sem servidor em Funções Azure utilizando a extensão de Funções Azure no Código do Estúdio Visual.
ms.topic: quickstart
ms.date: 11/04/2020
ms.openlocfilehash: 5d85c0362daa9dc3b42ed0cc1329329ffa598bee
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637052"
---
# <a name="quickstart-create-a-powershell-function-in-azure-using-visual-studio-code"></a>Quickstart: Criar uma função PowerShell em Azure utilizando o Código do Estúdio Visual

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

Neste artigo, utiliza o Código do Estúdio Visual para criar uma função PowerShell que responde a pedidos HTTP. Depois de testar o código localmente, implemente-o para o ambiente sem servidor das Funções Azure.

Completar este quickstart incorre num pequeno custo de alguns usd ou menos na sua conta Azure.

Há também uma [versão baseada em CLI](create-first-function-cli-powershell.md) deste artigo.

## <a name="configure-your-environment"></a>Configurar o ambiente

Antes de começar, certifique-se de que tem os seguintes requisitos em vigor:

+ Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ [Node.js](https://nodejs.org/), exigido pelo Windows para a npm. Apenas [versões LTS e De Manutenção LTS](https://nodejs.org/about/releases/). Utilize o `node --version` comando para verificar a sua versão.
    Não é necessário para o desenvolvimento local em macOS e Linux.

+ [PowerShell 7](/powershell/scripting/install/installing-powershell-core-on-windows)

+ Ambos [.NET Core 3.1 runtime](https://www.microsoft.com/net/download) e [.NET Core 2.1 runtime](https://dotnet.microsoft.com/download/dotnet-core/2.1)  

+ [Código visual do estúdio](https://code.visualstudio.com/) numa das [plataformas suportadas.](https://code.visualstudio.com/docs/supporting/requirements#_platforms)    

+ A [extensão PowerShell para Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).  

+ A [extensão de Funções Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Código de Estúdio Visual. 

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Crie o seu projeto local

Nesta secção, utiliza o Código do Estúdio Visual para criar um projeto local de Funções Azure em PowerShell. Mais tarde neste artigo, publicará o seu código de função para Azure.

1. Escolha o ícone Azure na barra de Atividade, em seguida, na área **Azure: Funções,** selecione o **novo projeto...** ícone.

    ![Escolha Criar um novo projeto](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Escolha um local de diretório para o seu espaço de trabalho do projeto e escolha **Select**.

    > [!NOTE]
    > Estes passos foram concebidos para serem concluídos fora de um espaço de trabalho. Neste caso, não selecione uma pasta de projeto que faz parte de uma área de trabalho.

1. Fornecer as seguintes informações nas instruções:

    + **Selecione um idioma para o seu projeto de função** : Escolha `PowerShell` .

    + **Selecione um modelo para a primeira função do seu projeto** : Escolha `HTTP trigger` .

    + **Fornecer um nome de função** : `HttpExample` Digite .

    + **Nível de autorização** : `Anonymous` Escolha, que permite a qualquer pessoa ligar para o ponto final da sua função. Para saber mais sobre o nível de autorização, consulte [as teclas de Autorização](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Selecione como pretende abrir o seu projeto** : Escolha `Add to workspace` .

1. Utilizando esta informação, o Visual Studio Code gera um projeto Azure Functions com um gatilho HTTP. Pode ver os ficheiros de projeto locais no Explorer. Para saber mais sobre ficheiros que são criados, consulte [os ficheiros do projeto Gerados.](functions-develop-vs-code.md#generated-project-files) 

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

Depois de verificar que a função funciona corretamente no seu computador local, é hora de usar o Código do Estúdio Visual para publicar o projeto diretamente para o Azure. 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Passos seguintes

Utilizou o Visual Studio Code para criar uma aplicação de funções com uma função simples acionada por HTTP. No artigo seguinte, expande essa função adicionando uma ligação de saída. Esta ligação escreve o string do pedido HTTP para uma mensagem numa fila de armazenamento de fila Azure. 

> [!div class="nextstepaction"]
> [Ligue-se a uma fila de armazenamento Azure](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-powershell)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions

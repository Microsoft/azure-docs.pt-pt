---
title: Criar uma função Java utilizando código de estúdio visual - Funções Azure
description: Aprenda a criar uma função Java e, em seguida, publique o projeto local para hospedagem sem servidor em Funções Azure usando a extensão de Funções Azure no Código do Estúdio Visual.
ms.topic: quickstart
ms.date: 11/03/2020
ms.openlocfilehash: daaa578b2842a6314706b3578f4c9e44d46aa6ce
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93425072"
---
# <a name="quickstart-create-a-java-function-in-azure-using-visual-studio-code"></a>Quickstart: Criar uma função Java em Azure usando Código de Estúdio Visual

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

Neste artigo, utiliza o Código do Estúdio Visual para criar uma função Java que responde aos pedidos HTTP. Depois de testar o código localmente, implemente-o para o ambiente sem servidor das Funções Azure.

Completar este quickstart incorre num pequeno custo de alguns usd ou menos na sua conta Azure.

> [!NOTE]
> Se o Visual Studio Code não é a sua ferramenta de desenvolvimento preferida, confira os nossos tutoriais semelhantes para desenvolvedores de Java usando [Maven,](create-first-function-cli-java.md) [Gradle](./functions-create-first-java-gradle.md) e [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions).

## <a name="configure-your-environment"></a>Configurar o ambiente

Antes de começar, certifique-se de que tem os seguintes requisitos em vigor:

+ Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ O [Kit de Desenvolvimento java,](/azure/developer/java/fundamentals/java-jdk-long-term-support)versão 8 ou 11.

+ [Apache Maven,](https://maven.apache.org)versão 3.0 ou superior.

+ [Código visual do estúdio](https://code.visualstudio.com/) numa das [plataformas suportadas.](https://code.visualstudio.com/docs/supporting/requirements#_platforms)

+ O [pacote de extensão java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)  

+ A [extensão de Funções Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Código de Estúdio Visual. 

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Crie o seu projeto local

Nesta secção, você usa o Código do Estúdio Visual para criar um projeto local de Funções Azure em Java. Mais tarde neste artigo, publicará o seu código de função para Azure. 

1. Escolha o ícone Azure na barra de Atividade, em seguida, na área **Azure: Funções,** selecione o **novo projeto...** ícone.

    ![Escolha Criar um novo projeto](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Escolha um local de diretório para o seu espaço de trabalho do projeto e escolha **Select**.

    > [!NOTE]
    > Estes passos foram concebidos para serem concluídos fora de um espaço de trabalho. Neste caso, não selecione uma pasta de projeto que faz parte de uma área de trabalho.

1. Fornecer as seguintes informações nas instruções:

    + **Selecione um idioma para o seu projeto de função** : Escolha `Java` .

    + **Selecione uma versão de Java** : Escolha `Java 8` `Java 11` ou, a versão Java em que as suas funções funcionam em Azure. Escolha uma versão Java que verificou localmente.

    + **Fornecer um ID de grupo** : Escolha `com.function` .

    + **Fornecer uma identificação de artefacto:** Escolha `myFunction` .

    + **Fornecer uma versão** : Escolha `1.0-SNAPSHOT` .

    + **Fornecer um nome de pacote** : Escolha `com.function` .

    + **Fornecer um nome de aplicação** : `myFunction-12345` Escolha.

    + **Nível de autorização** : `Anonymous` Escolha, que permite a qualquer pessoa ligar para o ponto final da sua função. Para saber mais sobre o nível de autorização, consulte [as teclas de Autorização](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Selecione como pretende abrir o seu projeto** : Escolha `Add to workspace` .

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
> [Ligue-se a uma fila de armazenamento Azure](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-java)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions

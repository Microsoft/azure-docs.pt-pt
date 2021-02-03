---
title: Criar uma função Java utilizando código de estúdio visual - Funções Azure
description: Aprenda a criar uma função Java e, em seguida, publique o projeto local para hospedagem sem servidor em Funções Azure usando a extensão de Funções Azure no Código do Estúdio Visual.
ms.topic: quickstart
ms.date: 11/03/2020
ms.openlocfilehash: 36516abd8b50c0b0b7d72e6bae0b67701509ca65
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493639"
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

+ [Visual Studio Code](https://code.visualstudio.com/) numa das [plataformas suportadas](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ O [pacote de extensão java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)  

+ A [extensão das Funções do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para o Visual Studio Code. 

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Crie o seu projeto local

Nesta secção, você usa o Código do Estúdio Visual para criar um projeto local de Funções Azure em Java. Mais adiante neste artigo, irá publicar o seu código de função no Azure. 

1. Selecione o ícone do Azure na barra de Atividade e, em seguida, na área **Azure: Funções**, selecione o ícone **Criar novo projeto...**.

    ![Selecione Criar um novo projeto](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Selecione uma localização do diretório para a área de trabalho do seu projeto e escolha **Selecionar**.

    > [!NOTE]
    > Estes passos foram concebidos para serem concluídos fora de um espaço de trabalho. Neste caso, não selecione uma pasta de projeto que faz parte de uma área de trabalho.

1. Forneça as seguintes informações nos pedidos:

    + **Selecione um idioma para o seu projeto de função**: Selecione `Java`.

    + **Selecione uma versão de Java**: Escolha `Java 8` `Java 11` ou, a versão Java em que as suas funções funcionam em Azure. Escolha uma versão Java que verificou localmente.

    + **Fornecer um ID de grupo**: Escolha `com.function` .

    + **Fornecer uma identificação de artefacto:** Escolha `myFunction` .

    + **Fornecer uma versão**: Escolha `1.0-SNAPSHOT` .

    + **Fornecer um nome de pacote**: Escolha `com.function` .

    + **Fornecer um nome de aplicação**: `myFunction-12345` Escolha.

    + **Nível de autorização**: `Anonymous` Escolha, que permite a qualquer pessoa ligar para o ponto final da sua função. Para saber mais sobre o nível de autorização, consulte [as teclas de Autorização](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Selecione como gostaria de abrir o seu projeto**: Selecione `Add to workspace`.

1. Utilizando esta informação, o Visual Studio Code gera um projeto Azure Functions com um gatilho HTTP. Pode ver os ficheiros de projeto locais no Explorer. Para saber mais sobre ficheiros que são criados, consulte [os ficheiros do projeto Gerados.](functions-develop-vs-code.md#generated-project-files) 

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Depois de verificar que a função funciona corretamente no seu computador local, é hora de usar o Código do Estúdio Visual para publicar o projeto diretamente para o Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Passos seguintes

Utilizou o [Código do Estúdio Visual](functions-develop-vs-code.md?tabs=java) para criar uma aplicação de função com uma função simples desencadeada por HTTP. No artigo seguinte, expande essa função ligando-se ao Azure Storage. Para saber mais sobre a ligação a outros serviços Azure, consulte [adicionar ligações a uma função existente em Funções Azure](add-bindings-existing-function.md?tabs=java). 

> [!div class="nextstepaction"]
> [Ligue-se a uma fila de armazenamento Azure](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-java)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions

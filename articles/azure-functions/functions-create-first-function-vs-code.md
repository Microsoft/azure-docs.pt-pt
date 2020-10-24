---
title: Criar a sua primeira função no Azure com o Visual Studio Code
description: Criar e publicar no Azure uma simples função ativada por HTTP com a extensão das Funções do Azure no Visual Studio Code.
ms.topic: quickstart
ms.date: 09/28/2020
ms.custom: devx-track-csharp, mvc, devcenter, seo, devx-track-python
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 4e50f6b2770f9f787c92e7a9c66f72cdbb252a94
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519695"
---
# <a name="quickstart-create-a-function-in-azure-using-visual-studio-code"></a>Quickstart: Criar uma função no Azure com o Visual Studio Code

::: zone pivot="programming-language-csharp"
Neste artigo, utiliza o Código do Estúdio Visual para criar uma função baseada na biblioteca de classe C# que responda aos pedidos HTTP. Depois de testar o código localmente, implemente-o para o ambiente sem servidor das Funções Azure.
::: zone-end
::: zone pivot="programming-language-javascript"
Neste artigo, utiliza o Código do Estúdio Visual para criar uma função JavaScript que responde a pedidos HTTP. Depois de testar o código localmente, implemente-o para o ambiente sem servidor das Funções Azure.
::: zone-end
::: zone pivot="programming-language-typescript"
Neste artigo, utiliza o Código do Estúdio Visual para criar uma função TypeScript que responde a pedidos HTTP. Depois de testar o código localmente, implemente-o para o ambiente sem servidor das Funções Azure.
::: zone-end 
::: zone pivot="programming-language-powershell"
Neste artigo, utiliza o Código do Estúdio Visual para criar uma função PowerShell que responde a pedidos HTTP. Depois de testar o código localmente, implemente-o para o ambiente sem servidor das Funções Azure.
::: zone-end
::: zone pivot="programming-language-python"
Neste artigo, utiliza o Código do Estúdio Visual para criar uma função Python que responde aos pedidos HTTP. Depois de testar o código localmente, implemente-o para o ambiente sem servidor das Funções Azure.
::: zone-end
::: zone pivot="programming-language-java"
Neste artigo, utiliza o Código do Estúdio Visual para criar uma função Java que responde aos pedidos HTTP. Depois de testar o código localmente, implemente-o para o ambiente sem servidor das Funções Azure.
::: zone-end

Completar este quickstart incorre num pequeno custo de alguns usd ou menos na sua conta Azure. 

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"
Há também uma [versão baseada em CLI](functions-create-first-azure-function-azure-cli.md) deste artigo.
::: zone-end
::: zone pivot="programming-language-java"
> [!NOTE]
> Se o Visual Studio Code não é a sua ferramenta de desenvolvimento preferencial, confira os nossos tutoriais semelhantes para desenvolvedores de Java usando [Maven,](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-java) [Gradle](./functions-create-first-java-gradle.md) e [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions).
::: zone-end

## <a name="configure-your-environment"></a>Configurar o ambiente

Antes de começar, certifique-se de que tem os seguintes requisitos em vigor:

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-python"
- [Node.js](https://nodejs.org/), exigido pelo Windows para a npm. Apenas [versões LTS e De Manutenção LTS](https://nodejs.org/about/releases/). Utilize o `node --version` comando para verificar a sua versão.
    Não é necessário para o desenvolvimento local em macOS e Linux. 
::: zone-end 
::: zone pivot="programming-language-javascript,programming-language-typescript"
- [Node.js](https://nodejs.org/), Versões LTS ativas e de manutenção (10.14.1 recomendado). Utilize o `node --version` comando para verificar a sua versão.
::: zone-end
::: zone pivot="programming-language-python"
- [Python 3.8](https://www.python.org/downloads/release/python-381/), [Python 3.7](https://www.python.org/downloads/release/python-375/), [Python 3.6](https://www.python.org/downloads/release/python-368/) são suportados por Azure Functions (x64).
::: zone-end 
::: zone pivot="programming-language-powershell"
- [PowerShell 7](/powershell/scripting/install/installing-powershell-core-on-windows)

- Ambos [.NET Core 3.1](https://www.microsoft.com/net/download) e [.NET Core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.2)
::: zone-end
::: zone pivot="programming-language-java"
- O [Kit de Desenvolvimento java,](/azure/developer/java/fundamentals/java-jdk-long-term-support)versão 8 ou 11.

- [Apache Maven,](https://maven.apache.org)versão 3.0 ou superior.
::: zone-end
- [Código visual do estúdio](https://code.visualstudio.com/) numa das [plataformas suportadas.](https://code.visualstudio.com/docs/supporting/requirements#_platforms)  
::: zone pivot="programming-language-csharp"
- A [extensão C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) para Código de Estúdio Visual.
::: zone-end
::: zone pivot="programming-language-python"
- A [extensão Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) para Código de Estúdio Visual.
::: zone-end
::: zone pivot="programming-language-powershell"
- A [extensão PowerShell para Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).
::: zone-end
::: zone pivot="programming-language-java"
- O [pacote de extensão java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
::: zone-end

- A [extensão de Funções Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Código de Estúdio Visual.

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Crie o seu projeto local

Nesta secção, utiliza o Código do Estúdio Visual para criar um projeto local de Funções Azure no seu idioma escolhido. Mais tarde neste artigo, publicará o seu código de função para Azure. 

1. Escolha o ícone Azure na barra de Atividade, em seguida, na área **Azure: Funções,** selecione o **novo projeto...** ícone.

    ![Escolha Criar um novo projeto](media/functions-create-first-function-vs-code/create-new-project.png)

1. Escolha um local de diretório para o seu espaço de trabalho do projeto e escolha **Select**.

    > [!NOTE]
    > Estes passos foram concebidos para serem concluídos fora de um espaço de trabalho. Neste caso, não selecione uma pasta de projeto que faz parte de uma área de trabalho.

1. Fornecer as seguintes informações nas instruções:

    ::: zone pivot="programming-language-csharp"
    - **Selecione um idioma para o seu projeto de função**: Escolha `C#` .
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    - **Selecione um idioma para o seu projeto de função**: Escolha `JavaScript` .
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    - **Selecione um idioma para o seu projeto de função**: Escolha `TypeScript` .
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    - **Selecione um idioma para o seu projeto de função**: Escolha `PowerShell` .
    ::: zone-end
    ::: zone pivot="programming-language-python"
    - **Selecione um idioma para o seu projeto de função**: Escolha `Python` .

    - **Selecione um pseudónimo Python para criar um ambiente virtual**: Escolha a localização do seu intérprete Python. Se a localização não for mostrada, escreva em todo o caminho para o binário Python.
    ::: zone-end

    ::: zone pivot="programming-language-java"
    - **Selecione um idioma para o seu projeto de função**: Escolha `Java` .

    - **Selecione uma versão de Java:** Escolha `Java 8` `Java 11` ou, em que as suas funções funcionam em Azure. Escolha uma versão Java que verificou localmente.

    - **Fornecer um ID de grupo**: Escolha `com.function` .

    - **Fornecer uma identificação de artefacto:** Escolha `myFunction` .

    - **Fornecer uma versão**: Escolha `1.0-SNAPSHOT` .

    - **Fornecer um nome de pacote**: Escolha `com.function` .

    - **Fornecer um nome de aplicação**: `myFunction-12345` Escolha.
    ::: zone-end
    ::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"
    - **Selecione um modelo para a primeira função do seu projeto**: Escolha `HTTP trigger` .
  
    - **Fornecer um nome de função**: `HttpExample` Digite .
    ::: zone-end
    ::: zone pivot="programming-language-csharp"
    - **Fornecer um espaço de nome :** Tipo `My.Functions` .
    ::: zone-end
    ::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"
    - **Nível de autorização**: `Anonymous` Escolha, que permite a qualquer pessoa ligar para o ponto final da sua função. Para saber mais sobre o nível de autorização, consulte [as teclas de Autorização](functions-bindings-http-webhook-trigger.md#authorization-keys).
    ::: zone-end
    - **Selecione como pretende abrir o seu projeto**: Escolha `Add to workspace` .

1. Utilizando esta informação, o Visual Studio Code gera um projeto Azure Functions com um gatilho HTTP. Pode ver os ficheiros de projeto locais no Explorer. Para saber mais sobre ficheiros que são criados, consulte [os ficheiros do projeto Gerados.](functions-develop-vs-code.md#generated-project-files) 

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python,programming-language-java"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

Depois de verificar que a função funciona corretamente no seu computador local, é hora de usar o Código do Estúdio Visual para publicar o projeto diretamente para o Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>Executar a função em Azure

1. De volta à área **Azure: Funções** na barra lateral, expanda a nova aplicação de função sob a sua subscrição. Expandir **funções**, clique à direita (Windows) ou <kbd>Ctrl -</kbd> clique (macOS) em **HttpExample**, e, em seguida, escolha **URL de função copiar**.

    ![Copie o URL de função para o novo gatilho HTTP](./media/functions-create-first-function-vs-code/function-copy-endpoint-url.png)

1. Cole este URL para o pedido HTTP na barra de endereços do seu navegador, adicione o `name` string de consulta quanto ao final deste URL `?name=Functions` e, em seguida, execute o pedido. O URL que chama a função acionada por HTTP deve estar no seguinte formato:

    ```http
    http://<functionappname>.azurewebsites.net/api/httpexample?name=Functions
    ```

    O exemplo a seguir mostra a resposta no navegador ao pedido remoto GET devolvido pela função: 

    ![Resposta da função no browser](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando continuar até ao próximo passo, [adicione uma fila de armazenamento Azure à sua função,](functions-add-output-binding-storage-queue-vs-code.md)terá de manter todos os seus recursos no lugar para se basear no que já fez.

Caso contrário, pode utilizar os seguintes passos para eliminar a aplicação de função e os seus recursos relacionados para evitar incorrer em custos adicionais.

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

Para saber mais sobre os custos das funções, consulte [os custos do plano de consumo](functions-consumption-costs.md)estimado.

## <a name="next-steps"></a>Passos seguintes

Utilizou o Visual Studio Code para criar uma aplicação de funções com uma função simples acionada por HTTP. No artigo seguinte, expande essa função adicionando uma ligação de saída. Esta ligação escreve o string do pedido HTTP para uma mensagem numa fila de armazenamento de fila Azure. 

> [!div class="nextstepaction"]
> [Adicione uma fila de armazenamento Azure à sua função](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions

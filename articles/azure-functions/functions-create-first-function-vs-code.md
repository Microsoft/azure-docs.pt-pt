---
title: Criar a sua primeira função no Azure com o Visual Studio Code
description: Criar e publicar no Azure uma simples função ativada por HTTP com a extensão das Funções do Azure no Visual Studio Code.
ms.topic: quickstart
ms.date: 01/10/2020
ms.custom: mvc, devcenter
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 42888060f206c89a597a1a18783070d0a805dfb9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79241305"
---
# <a name="quickstart-create-an-azure-functions-project-using-visual-studio-code"></a>Quickstart: Criar um projeto de funções Azure usando o Código de Estúdio Visual

Neste artigo, utiliza o Código do Estúdio Visual para criar uma função que responda aos pedidos do HTTP. Depois de testar o código localmente, implementa-o para o ambiente sem servidor estoirar das Funções Azure. Completar este quickstart incorre num pequeno custo de alguns cêntimos de USD ou menos na sua conta Azure. 

Há também uma [versão baseada em CLI](functions-create-first-azure-function-azure-cli.md) deste artigo.

## <a name="configure-your-environment"></a>Configurar o ambiente

Antes de começar, certifique-se de que tem os seguintes requisitos no lugar:

+ Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-python"  
+ [Node.js](https://nodejs.org/), exigido pelo Windows para as npm. Apenas [versões LTS ativas e de Manutenção LTS. ](https://nodejs.org/about/releases/) Use o comando `npm --version` para verificar a sua versão.
    Não é necessário para o desenvolvimento local em MacOS e Linux.   
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-typescript"  
+ [Node.js,](https://nodejs.org/)versões Ative LTS e Manutenção LTS (10.14.1 recomendado). Use o comando `npm --version` para verificar a sua versão.
::: zone-end 
::: zone pivot="programming-language-python"
+ [Python 3.8](https://www.python.org/downloads/release/python-381/), [Python 3.7](https://www.python.org/downloads/release/python-375/), [Python 3.6](https://www.python.org/downloads/release/python-368/) são suportados por Funções Azure.
::: zone-end   
::: zone pivot="programming-language-powershell"
+ [Núcleo PowerShell](/powershell/scripting/install/installing-powershell-core-on-windows)

+ O [Núcleo .NET SDK 2.2+](https://www.microsoft.com/net/download)  
::: zone-end  
+ [Código de Estúdio Visual](https://code.visualstudio.com/) numa das [plataformas suportadas.](https://code.visualstudio.com/docs/supporting/requirements#_platforms)  
::: zone pivot="programming-language-csharp"  
+ A [ C# extensão](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) para Visual Studio Code.  
::: zone-end  
::: zone pivot="programming-language-python"
+ A [extensão Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) para Visual Studio Code.  
::: zone-end  
::: zone pivot="programming-language-powershell"
+ A [extensão PowerShell para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).  
::: zone-end  

+ A [extensão de funções Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Código de Estúdio Visual. 

## <a name="create-an-azure-functions-project"></a>Crie o seu projeto local 

Nesta secção, você usa o Código de Estúdio Visual para criar um projeto local de Funções Azure no seu idioma escolhido. Mais tarde neste artigo, publicará o seu código de função ao Azure. 

1. Escolha o ícone Azure na barra de Atividades, em seguida, na área **Azure: Funções,** selecione o **novo projeto Criar...** ícone.

    ![Escolha Criar um novo projeto](media/functions-create-first-function-vs-code/create-new-project.png)

1. Escolha um local de diretório para o seu espaço de trabalho do projeto e escolha **Select**.

    > [!NOTE]
    > Estes passos foram concebidos para serem concluídos fora de um espaço de trabalho. Neste caso, não selecione uma pasta de projeto que faz parte de uma área de trabalho.

1. Forneça as seguintes informações nas instruções:

    ::: zone pivot="programming-language-csharp"
    + **Selecione um idioma para o seu projeto de função**: Escolha `C#`.
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    + **Selecione um idioma para o seu projeto de função**: Escolha `JavaScript`.
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    + **Selecione um idioma para o seu projeto de função**: Escolha `TypeScript`.
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    + **Selecione um idioma para o seu projeto de função**: Escolha `PowerShell`.
    ::: zone-end
    ::: zone pivot="programming-language-python"
    + **Selecione um idioma para o seu projeto de função**: Escolha `Python`.

    + **Selecione um pseudónimo Python para criar um ambiente virtual**: Escolha a localização do seu intérprete Python. Se a localização não for mostrada, escreva no caminho completo para o seu binário Python.  
    ::: zone-end

    + **Selecione um modelo para a primeira função do seu projeto:** Escolha `HTTP trigger`.
    
    + **Forneça um nome de função**: Escreva `HttpExample`.
    
    ::: zone pivot="programming-language-csharp"
    + **Forneça um espaço de nome**: Type `My.Functions`. 
    ::: zone-end

    + **Nível de autorização**: Escolha `Anonymous`, que permite a qualquer pessoa ligar para o seu ponto final de função. Para saber mais sobre o nível de autorização, consulte [as teclas de autorização.](functions-bindings-http-webhook-trigger.md#authorization-keys)

    + **Selecione como pretende abrir o seu projeto**: Escolha `Add to workspace`.

1. Utilizando esta informação, o Visual Studio Code gera um projeto de Funções Azure com um gatilho HTTP. Pode ver os ficheiros do projeto local no Explorer. Para saber mais sobre os ficheiros que são criados, consulte [ficheiros de projetos gerados](functions-develop-vs-code.md#generated-project-files). 

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

Depois de verificar que a função funciona corretamente no seu computador local, é hora de usar o Código do Estúdio Visual para publicar o projeto diretamente para o Azure. 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>Executar a função em Azure

1. De volta à área **Azure: Funções** na barra lateral, expanda a nova aplicação de funções sob a sua subscrição. Expandir **funções,** clicar à direita (Windows) ou Ctrl + clicar (MacOS) em **HttpExample**e, em seguida, escolher **Copy function URL**.

    ![Copiar o URL de função para o novo gatilho HTTP](./media/functions-create-first-function-vs-code/function-copy-endpoint-url.png)

1. Colá-lo este URL para o pedido http na barra de endereços do seu navegador, adicione a cadeia de consulta `name` como `?name=Functions` ao final deste URL e, em seguida, execute o pedido. O URL que chama a função acionada por HTTP deve estar no seguinte formato:

        http://<functionappname>.azurewebsites.net/api/httpexample?name=Functions 
        
    O exemplo seguinte mostra a resposta no navegador ao pedido REMOTO GET devolvido pela função: 

    ![Resposta da função no browser](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando continuar até ao próximo passo, Adicione uma fila de [armazenamento Azure ligando à sua função,](functions-add-output-binding-storage-queue-vs-code.md)terá de manter todos os seus recursos no lugar para basear no que já fez.

Caso contrário, pode utilizar os seguintes passos para eliminar a aplicação de funções e os seus recursos conexos para evitar incorrer em custos adicionais.

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

Para saber mais sobre os custos das Funções, consulte [estimar os custos do plano de consumo.](functions-consumption-costs.md)

## <a name="next-steps"></a>Passos seguintes

Utilizou o Visual Studio Code para criar uma aplicação de funções com uma função simples acionada por HTTP. No artigo seguinte, expande essa função adicionando uma ligação de saída. Esta ligação escreve o fio do pedido HTTP para uma mensagem numa fila de armazenamento de fila Azure. 

> [!div class="nextstepaction"]
> [Adicione uma ligação de fila de armazenamento Azure à sua função](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions

---
title: Ligue as funções Azure ao armazenamento do Azure utilizando o Código do Estúdio Visual
description: Saiba como ligar as Funções Azure a uma fila de armazenamento Azure adicionando uma ligação de saída ao seu projeto Visual Studio Code.
ms.date: 02/07/2020
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-js
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 96384d2c50e7d5b4b5b6e652d01c4a89cd519573
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99493404"
---
# <a name="connect-azure-functions-to-azure-storage-using-visual-studio-code"></a>Ligue as funções Azure ao armazenamento do Azure utilizando o Código do Estúdio Visual

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Este artigo mostra-lhe como usar o Código do Estúdio Visual para ligar o Azure Storage à função que criou no artigo de arranque rápido anterior. A vinculação de saída que adiciona a esta função escreve dados do pedido HTTP para uma mensagem numa fila de armazenamento da Fila Azure. 

A maioria das ligações requer uma cadeia de ligação armazenada que as funções usam para aceder ao serviço vinculado. Para facilitar, utiliza a conta de Armazenamento que criou com a sua aplicação de função. A ligação a esta conta já está armazenada numa definição de aplicação chamada `AzureWebJobsStorage` .  

## <a name="configure-your-local-environment"></a>Configure o seu ambiente local

Antes de iniciar este artigo, deve cumprir os seguintes requisitos:

* Instale a [extensão de armazenamento Azure para Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage).

* Instale [o Explorador de Armazenamento Azure](https://storageexplorer.com/). O Storage Explorer é uma ferramenta que utilizará para examinar as mensagens de fila geradas pela sua ligação de saída. O Storage Explorer é suportado em sistemas operativos baseados em macOS, Windows e Linux.

::: zone pivot="programming-language-csharp"
* Instale [as ferramentas .NET Core CLI](/dotnet/core/tools/?tabs=netcore2x).
::: zone-end

::: zone pivot="programming-language-csharp"  
* Complete os passos na [parte 1 do Quickstart Visual Studio Code](create-first-function-vs-code-csharp.md). 
::: zone-end  
::: zone pivot="programming-language-javascript"  
* Complete os passos na [parte 1 do Quickstart Visual Studio Code](create-first-function-vs-code-node.md). 
::: zone-end   
::: zone pivot="programming-language-java"  
* Complete os passos na [parte 1 do Quickstart Visual Studio Code](create-first-function-vs-code-java.md). 
::: zone-end   
::: zone pivot="programming-language-typescript"  
* Complete os passos na [parte 1 do Quickstart Visual Studio Code](create-first-function-vs-code-typescript.md). 
::: zone-end   
::: zone pivot="programming-language-python"  
* Complete os passos na [parte 1 do Quickstart Visual Studio Code](create-first-function-vs-code-python.md). 
::: zone-end   
::: zone pivot="programming-language-powershell"  
* Complete os passos na [parte 1 do Quickstart Visual Studio Code](create-first-function-vs-code-powershell.md). 
::: zone-end   

Este artigo pressupõe que já assinou a subscrição do Azure do Visual Studio Code. Pode iniciar sôs-se correndo `Azure: Sign In` a partir da paleta de comando. 

## <a name="download-the-function-app-settings"></a>Descarregue as definições da aplicação de funções

No [artigo de arranque rápido anterior,](./create-first-function-vs-code-csharp.md)criou uma aplicação de função em Azure juntamente com a conta de Armazenamento necessária. O fio de ligação desta conta é armazenado de forma segura nas definições de aplicações em Azure. Neste artigo, você escreve mensagens para uma fila de armazenamento na mesma conta. Para se ligar à sua conta de Armazenamento ao executar a função localmente, tem de transferir as definições da aplicação para o local.settings.jsno ficheiro. 

1. Pressione a tecla F1 para abrir a paleta de comando, em seguida, procure e execute o comando `Azure Functions: Download Remote Settings....` . 

1. Escolha a aplicação de função criada no artigo anterior. Selecione **Sim a todos** para substituir as definições locais existentes. 

    > [!IMPORTANT]  
    > Como contém segredos, o local.settings.jsem ficheiro nunca é publicado, e é excluído do controlo de origem.

1. Copie o valor `AzureWebJobsStorage` , que é a chave para o valor da cadeia de ligação de conta de armazenamento. Utilize esta ligação para verificar se a ligação de saída funciona como esperado.

## <a name="register-binding-extensions"></a>Registar as extensões de enlace

Como está a utilizar uma ligação de saída de armazenamento de fila, tem de ter a extensão de encadernações de armazenamento instalada antes de executar o projeto. 

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-java"

O seu projeto foi configurado para utilizar [pacotes de extensão,](functions-bindings-register.md#extension-bundles)que instala automaticamente um conjunto de pacotes de extensão predefinidos. 

A utilização de pacotes de extensão é ativada no host.jsficheiro na raiz do projeto, que aparece da seguinte forma:

:::code language="json" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/host.json":::

::: zone-end

::: zone pivot="programming-language-csharp"

Com exceção dos gatilhos HTTP e timer, as ligações são implementadas como pacotes de extensão. Executar o seguinte [comando de pacote de dotnet](/dotnet/core/tools/dotnet-add-package) na janela Terminal para adicionar o pacote de extensão de Armazenamento ao seu projeto.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage
```

::: zone-end

Agora, pode adicionar a ligação de saída de armazenamento ao seu projeto.

## <a name="add-an-output-binding"></a>Adicionar um enlace de saída

Em Funções, cada tipo de encadernação requer `direction` `type` um, e um único `name` a ser definido no function.jsem arquivo. A forma como define estes atributos depende do idioma da sua aplicação de função.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [functions-add-output-binding-java](../../includes/functions-add-output-binding-java.md)]

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>Adicione código que utiliza o enlace de saída

Depois de definido o encadernação, pode utilizar `name` a ligação para aceder a ela como um atributo na assinatura da função. Ao utilizar uma ligação de saída, não é preciso utilizar o código Azure Storage SDK para autenticação, obter uma referência de fila ou escrever dados. O tempo de funcionamento e a ligação de saída de fila das funções fazem essas tarefas para si.

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

::: zone-end

::: zone pivot="programming-language-csharp"  

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

::: zone-end  

::: zone pivot="programming-language-java"  

[!INCLUDE [functions-add-storage-binding-java-code](../../includes/functions-add-storage-binding-java-code.md)]

## <a name="update-the-tests"></a>Atualizar os testes

[!INCLUDE [functions-add-output-binding-java-test](../../includes/functions-add-output-binding-java-test.md)]

::: zone-end  

## <a name="run-the-function-locally"></a>Executar a função localmente

1. Como no artigo anterior, prima <kbd>F5</kbd> para iniciar o projeto de aplicação de função e Ferramentas Core. 

1. Com as Ferramentas Core em funcionamento, vá para a área **Azure: Funções.** Em **Funções**, expandir funções **de projeto**  >  **locais.** Clique com o botão direito (Ctrl-click em Mac) a `HttpExample` função e escolha **Executar Função Agora...**.

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Execute a função agora a partir do Código do Estúdio Visual":::

1. In **Enter request body** você vê o valor do corpo da mensagem de pedido de `{ "name": "Azure" }` . Prima Insira para enviar esta mensagem de pedido para a sua função.  
 
1. Depois de retornado, prima <kbd>Ctrl + C</kbd> para parar as Ferramentas Core.

Como está a utilizar a cadeia de ligação de armazenamento, a sua função liga-se à conta de armazenamento Azure quando funciona localmente. Uma nova fila chamada **outqueue** é criada na sua conta de armazenamento pelo tempo de execução de Funções quando a ligação de saída é usada pela primeira vez. Utilizará o Storage Explorer para verificar se a fila foi criada juntamente com a nova mensagem.

### <a name="connect-storage-explorer-to-your-account"></a>Ligar o Explorador de Armazenamento à sua conta

Salte esta secção se já tiver instalado o Azure Storage Explorer e ligá-lo à sua conta Azure.

1. Executar a ferramenta [Azure Storage Explorer], selecione o ícone de ligação à esquerda e **selecione Adicionar uma conta**.

    ![Adicione uma conta Azure ao Microsoft Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. No diálogo **Connect,** escolha **Adicionar uma conta Azure,** escolha o seu **ambiente Azure** e selecione **Iniciar sê-lo...**. 

    ![Inicie sessão na sua conta do Azure](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

Depois de iniciar seduca na sua conta com sucesso, vê todas as subscrições do Azure associadas à sua conta.

### <a name="examine-the-output-queue"></a>Examinar a fila de saída

1. No Código do Estúdio Visual, prima a tecla F1 para abrir a paleta de comando, depois procure e execute o comando e escolha o `Azure Storage: Open in Storage Explorer` nome da sua conta de Armazenamento. A sua conta de armazenamento abre no Azure Storage Explorer.  

1. Expanda o nó **Filas** nó e, em seguida, selecione a fila com o nome **outqueue**. 

   A fila contém a mensagem que a fila de enlace de saída da fila criou quando executou a função acionada por HTTP. Se invocou a função com o valor predefinido `name` do *Azure*, a mensagem de fila é *Nome transmitido para a função: Azure*.

    ![Mensagem de fila mostrada no Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Executar a função novamente, enviar outro pedido, e verá uma nova mensagem aparecer na fila.  

Agora, é hora de reeditar a aplicação de função atualizada para a Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>Reimplantar e verificar a aplicação atualizada

1. No Código do Estúdio Visual, prima F1 para abrir a paleta de comando. Na paleta de comando, procure e selecione `Azure Functions: Deploy to function app...` .

1. Escolha a aplicação de função que criou no primeiro artigo. Como está a recolocar o seu projeto na mesma aplicação, selecione **Implementar** para rejeitar o aviso sobre a sobreposição de ficheiros.

1. Após a implementação concluída, pode voltar a utilizar a **função Executar Agora...** para ativar a função em Azure.

1. Volte [a ver a mensagem na fila de armazenamento](#examine-the-output-queue) para verificar se a ligação de saída gera novamente uma nova mensagem na fila.

## <a name="clean-up-resources"></a>Limpar os recursos

No Azure, *os recursos* referem-se a apps de função, funções, contas de armazenamento, etc. Estão agrupados em *grupos de recursos,* e podes apagar tudo num grupo, eliminando o grupo.

Criou recursos para concluir estes guias de introdução. Poderá ser-lhe cobrado estes recursos, dependendo do seu [estado da conta](https://azure.microsoft.com/account/) e dos [preços dos serviços](https://azure.microsoft.com/pricing/). Se já não precisar dos recursos, pode eliminá-los da seguinte forma:

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

## <a name="next-steps"></a>Passos seguintes

Atualizou a sua função HTTP para escrever dados numa fila de armazenamento. Agora pode aprender mais sobre o desenvolvimento de funções utilizando o Código do Estúdio Visual:

+ [Desenvolver funções Azure usando código de estúdio visual](functions-develop-vs-code.md)

+ [Funções Azure aciona e encaderna](functions-triggers-bindings.md).
::: zone pivot="programming-language-csharp"  
+ [Exemplos de projetos completos de funções em C#](/samples/browse/?products=azure-functions&languages=csharp).

+ [Referência do programador Azure Functions C#](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Exemplos de projetos de função completa no JavaScript.](/samples/browse/?products=azure-functions&languages=javascript)

+ [Azure Functions JavaScript guia de desenvolvedores](functions-reference-node.md)  
::: zone-end  
::: zone pivot="programming-language-java"  
+ [Exemplos de projetos completos de função em Java.](/samples/browse/?products=azure-functions&languages=java)

+ [Guia de desenvolvedores de Java funções Azure Functions](functions-reference-java.md)  
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [Exemplos de projetos de função completos no TypeScript](/samples/browse/?products=azure-functions&languages=typescript).

+ [Guia de desenvolvedores tipo de funções Azure](functions-reference-node.md#typescript)  
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Exemplos de projetos completos de funções em Python.](/samples/browse/?products=azure-functions&languages=python)

+ [Guia de desenvolvedores de Azure Functions Python](functions-reference-python.md)  
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [Exemplos de projetos de função completos em PowerShell.](/samples/browse/?products=azure-functions&languages=azurepowershell)

+ [Guia de desenvolvedores powershell de funções Azure Functions](functions-reference-powershell.md) 
::: zone-end
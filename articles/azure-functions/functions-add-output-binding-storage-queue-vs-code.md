---
title: Conecte funções azure ao armazenamento azure usando código de estúdio visual
description: Aprenda a ligar as Funções Azure a uma fila de Armazenamento Azure adicionando uma ligação de saída ao seu projeto Visual Studio Code.
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 22f7df52e90a35a3ed9a26a7672f8354efc173e3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79241333"
---
# <a name="connect-azure-functions-to-azure-storage-using-visual-studio-code"></a>Conecte funções azure ao armazenamento azure usando código de estúdio visual

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Este artigo mostra-lhe como usar o Código do Estúdio Visual para ligar a função que criou no [artigo quickstart anterior](functions-create-first-function-vs-code.md) ao Azure Storage. O encadernação de saída que adiciona a esta função escreve dados do pedido HTTP para uma mensagem numa fila de armazenamento de fila Azure. 

A maioria das ligações requer uma cadeia de ligação armazenada que as Funções usam para aceder ao serviço de encadernação. Para facilitar, utiliza a conta de Armazenamento que criou com a sua aplicação de funções. A ligação a esta conta já está armazenada numa definição de aplicação chamada `AzureWebJobsStorage`.  

## <a name="configure-your-local-environment"></a>Configure o seu ambiente local

Antes de iniciar este artigo, deve cumprir os seguintes requisitos:

* Instale a extensão de [armazenamento Azure para Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage).

* Instale o Explorador de [Armazenamento Azure.](https://storageexplorer.com/) O Storage Explorer é uma ferramenta que utilizará para examinar as mensagens de fila geradas pela sua encadernação de saída. O Storage Explorer é suportado em sistemas operativos baseados em macOS, Windows e Linux.

::: zone pivot="programming-language-csharp"
* Instale [as ferramentas CLI do Núcleo .NET](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x).
::: zone-end

* Complete os passos na [parte 1 do Código do Estúdio Visual.](functions-create-first-function-vs-code.md) 

Este artigo assume que já assinou a subscrição do Azure a partir do Visual Studio Code. Pode iniciar sessão executando `Azure: Sign In` a partir da paleta de comando. 

## <a name="download-the-function-app-settings"></a>Descarregue as definições da aplicação de funções

No artigo anterior do [Quickstart,](functions-create-first-function-vs-code.md)criou uma aplicação de função em Azure juntamente com a conta de Armazenamento necessária. A cadeia de ligação para esta conta é armazenada de forma segura nas definições da aplicação em Azure. Neste artigo, escreve mensagens para uma fila de armazenamento na mesma conta. Para se ligar à sua conta de Armazenamento ao executar a função localmente, tem de descarregar as definições da aplicação para o ficheiro local.settings.json. 

1. Pressione a tecla F1 para abrir a paleta de comando, depois procure e execute o comando `Azure Functions: Download Remote Settings....`. 

1. Escolha a aplicação de funções que criou no artigo anterior. Selecione **Sim a todos** para substituir as definições locais existentes. 

    > [!IMPORTANT]  
    > Como contém segredos, o ficheiro local.settings.json nunca é publicado, e está excluído do controlo de fontes.

1. Copiar o valor `AzureWebJobsStorage`, que é a chave para o valor de cadeia de ligação da conta de armazenamento. Utilize esta ligação para verificar se a ligação de saída funciona como esperado.

## <a name="register-binding-extensions"></a>Registar as extensões de enlace

Como está a utilizar uma encadernação de saída de armazenamento de fila, tem de ter a extensão de encadernação de armazenamento instalada antes de executar o projeto. 

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

Com exceção dos gatilhos HTTP e temporizador, as encadernações são implementadas como pacotes de extensão. Execute o seguinte comando de pacote de [dotnet](/dotnet/core/tools/dotnet-add-package) na janela terminal para adicionar o pacote de extensão de armazenamento ao seu projeto.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

::: zone-end

Agora, pode adicionar a ligação de saída de armazenamento ao seu projeto.

## <a name="add-an-output-binding"></a>Adicionar um enlace de saída

Em Funções, cada tipo de encadernação requer uma `direction`, `type`, e um `name` único a ser definido no ficheiro função.json. A forma como define estes atributos depende do idioma da sua aplicação de funções.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>Adicione código que utiliza o enlace de saída

Após a definição da ligação, pode utilizar o `name` da ligação para aceder à sua assinatura de função. Ao utilizar uma ligação de saída, não é necessário utilizar o código SDK de Armazenamento Azure para autenticação, obtendo uma referência de fila ou escrevendo dados. O tempo de funcionamento das Funções e a ligação de saída da fila fazem essas tarefas para si.

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

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

Uma nova fila chamada **outqueue** é criada na sua conta de armazenamento pelo tempo de funcionamento das Funções quando a ligação de saída é usada pela primeira vez. Utilizará o Storage Explorer para verificar se a fila foi criada juntamente com a nova mensagem.

### <a name="connect-storage-explorer-to-your-account"></a>Ligar o Explorador de Armazenamento à sua conta

Ignore esta secção se já instalou o Azure Storage Explorer e ligou-o à sua conta Azure.

1. Executar a ferramenta [Azure Storage Explorer], selecione o ícone de ligação à esquerda e selecione **Adicionar uma conta**.

    ![Adicione uma conta Azure ao Microsoft Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. No diálogo **Connect,** escolha **Adicionar uma conta Azure,** escolha o seu **ambiente Azure,** e selecione **Iniciar sessão...** . 

    ![Inicie sessão na sua conta do Azure](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

Depois de iniciar sessão com sucesso na sua conta, vê todas as subscrições do Azure associadas à sua conta.

### <a name="examine-the-output-queue"></a>Examinar a fila de saída

1. No Visual Studio Code, prima a tecla F1 para abrir a paleta de comando, depois procure e execute o comando `Azure Storage: Open in Storage Explorer` e escolha o nome da sua conta de Armazenamento. A sua conta de armazenamento abre no Azure Storage Explorer.  

1. Expanda o nó **Filas** nó e, em seguida, selecione a fila com o nome **outqueue**. 

   A fila contém a mensagem que a fila de enlace de saída da fila criou quando executou a função acionada por HTTP. Se invocou a função com o valor predefinido `name` do *Azure*, a mensagem de fila é *Nome transmitido para a função: Azure*.

    ![Mensagem de fila mostrada no Explorador de Armazenamento Azure](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Faça a função novamente, envie outro pedido, e verá uma nova mensagem aparecer na fila.  

Agora, é hora de reeditar a aplicação de funções atualizada para o Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>Recolocar e verificar a aplicação atualizada

1. No Visual Studio Code, prima F1 para abrir a paleta de comando. Na paleta de comando, procure e selecione `Azure Functions: Deploy to function app...`.

1. Escolha a aplicação de funções que criou no primeiro artigo. Como está a recolocar o seu projeto na mesma aplicação, selecione **Deploy** para descartar o aviso sobre ficheiros de sobreposição.

1. Após a implementação concluída, pode voltar a utilizar cURL ou um browser para testar a função reimplantada. Como antes, anexar a cadeia de consulta `&name=<yourname>` ao URL, como no seguinte exemplo:

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. Volte [a ver a mensagem na fila de armazenamento](#examine-the-output-queue) para verificar se a ligação de saída gera novamente uma nova mensagem na fila.

## <a name="clean-up-resources"></a>Limpar recursos

Os *recursos* no Azure referem-se a aplicações de funções, funções, contas de armazenamento e assim sucessivamente. Estes são agrupados em *grupos de recursos* e pode eliminar tudo num grupo ao eliminá-lo.

Criou recursos para concluir estes guias de introdução. Poderá ser-lhe cobrado estes recursos, dependendo do seu [estado da conta](https://azure.microsoft.com/account/) e dos [preços dos serviços](https://azure.microsoft.com/pricing/). Se já não precisar dos recursos, pode eliminá-los da seguinte forma:

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Passos seguintes

Atualizou a sua função de http para escrever dados para uma fila de Armazenamento. Agora pode aprender mais sobre o desenvolvimento de Funções usando o Código de Estúdio Visual:

+ [Desenvolver funções azure usando código de estúdio visual](functions-develop-vs-code.md)
::: zone pivot="programming-language-csharp"  
+ [Exemplos de projetos C#de função completa em ](/samples/browse/?products=azure-functions&languages=csharp).
+ [Referência do C# desenvolvedor de Funções Azure](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Exemplos de projetos de função completa sintetmente em JavaScript](/samples/browse/?products=azure-functions&languages=javascript).
+ [Guia de desenvolvedores de funções azure JavaScript](functions-reference-node.md)  
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [Exemplos de projetos de função completa supor script](/samples/browse/?products=azure-functions&languages=typescript).
+ [Guia de desenvolvedor de funções azure TypeScript](functions-reference-node.md#typescript)  
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Exemplos de projetos de função completa em Python.](/samples/browse/?products=azure-functions&languages=python)
+ [Guia de desenvolvimento de funções azure Python](functions-reference-python.md)  
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [Exemplos de projetos de função completos no PowerShell](/samples/browse/?products=azure-functions&languages=azurepowershell).
+ [Guia de desenvolvimento de funções Azure PowerShell](functions-reference-powershell.md) 
::: zone-end
+ [Funções Azure aciona matoe e encadernações.](functions-triggers-bindings.md)
+ [Página de preços de funções](https://azure.microsoft.com/pricing/details/functions/)
+ [Estimativa do plano de consumo custa](functions-consumption-costs.md) artigo.

---
title: Ligar funções ao Armazenamento Azure usando o Código de Estúdio Visual
description: Aprenda a adicionar uma ligação de saída para ligar as suas funções a uma fila de Armazenamento Azure utilizando o Código do Estúdio Visual.
ms.date: 06/25/2019
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 5b7d7be7854a216b7cb7b610ea6d51fdc496a93f
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845649"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio-code"></a>Ligar funções ao Armazenamento Azure usando o Código de Estúdio Visual

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Este artigo mostra-lhe como usar o Código do Estúdio Visual para ligar a função que criou no [artigo quickstart anterior](functions-create-first-function-vs-code.md) ao Azure Storage. O encadernação de saída que adiciona a esta função escreve dados do pedido HTTP para uma mensagem numa fila de armazenamento de fila Azure. 

A maioria das ligações requer uma cadeia de ligação armazenada que as Funções usam para aceder ao serviço de encadernação. Para facilitar, utiliza a conta de Armazenamento que criou com a sua aplicação de funções. A ligação a esta conta já está armazenada numa definição de aplicação chamada `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este artigo, deve cumprir os seguintes requisitos:

* Instale a extensão de [armazenamento Azure para Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage).

* Instale o Explorador de [Armazenamento Azure.](https://storageexplorer.com/) O Storage Explorer é uma ferramenta que utilizará para examinar as mensagens de fila geradas pela sua encadernação de saída. O Storage Explorer é suportado em sistemas operativos baseados em macOS, Windows e Linux.

::: zone pivot="programming-language-csharp"
* Instale [as ferramentas CLI do Núcleo .NET](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x).
::: zone-end

* Complete os passos na [parte 1 do Código do Estúdio Visual.](functions-create-first-function-vs-code.md) 

Este artigo assume que já assinou a subscrição do Azure a partir do Visual Studio Code. Pode iniciar sessão executando `Azure: Sign In` a partir da paleta de comando. 

## <a name="download-the-function-app-settings"></a>Baixar as configurações do aplicativo de funções

No artigo anterior do [Quickstart,](functions-create-first-function-vs-code.md)criou uma aplicação de função em Azure juntamente com a conta de Armazenamento necessária. A cadeia de ligação para esta conta é armazenada de forma segura nas definições da aplicação em Azure. Neste artigo, escreve mensagens para uma fila de armazenamento na mesma conta. Para se ligar à sua conta de Armazenamento ao executar a função localmente, tem de descarregar as definições da aplicação para o ficheiro local.settings.json. 

1. Pressione a tecla F1 para abrir a paleta de comando, depois procure e execute o comando `Azure Functions: Download Remote Settings....`. 

1. Escolha a aplicação de funções que criou no artigo anterior. Selecione **Sim a todos** para substituir as definições locais existentes. 

    > [!IMPORTANT]  
    > Como contém segredos, o ficheiro local.settings.json nunca é publicado, e está excluído do controlo de fontes.

1. Copiar o valor `AzureWebJobsStorage`, que é a chave para o valor de cadeia de ligação da conta de armazenamento. Utilize esta ligação para verificar se a ligação de saída funciona como esperado.

## <a name="register-binding-extensions"></a>Registar as extensões de enlace

Como você está usando uma associação de saída de armazenamento de fila, você deve ter a extensão de associações de armazenamento instalada antes de executar o projeto. 

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

Com exceção dos gatilhos HTTP e Timer, as associações são implementadas como pacotes de extensão. Execute o comando [dotnet adicionar pacote](/dotnet/core/tools/dotnet-add-package) a seguir na janela do terminal para adicionar o pacote de extensão de armazenamento ao seu projeto.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

::: zone-end

Agora, pode adicionar a ligação de saída de armazenamento ao seu projeto.

## <a name="add-an-output-binding"></a>Adicionar um enlace de saída

Em funções, cada tipo de associação requer um `direction`, `type`e um `name` exclusivo a ser definido no arquivo function. JSON. A maneira como você define esses atributos depende do idioma do seu aplicativo de funções.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>Adicione código que utiliza o enlace de saída

Depois que a associação é definida, você pode usar a `name` da Associação para acessá-la como um atributo na assinatura da função. Usando uma associação de saída, você não precisa usar o código do SDK de armazenamento do Azure para autenticação, obter uma referência de fila ou gravar dados. O tempo de execução de funções e a associação de saída de fila executam essas tarefas para você.

::: zone pivot="programming-language-javascript"

[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]

::: zone-end

::: zone pivot="programming-language-typescript"

Adicione o código que utiliza o objeto de ligação de saída `msg` `context.bindings` criar uma mensagem de fila. Adicione este código antes da declaração de `context.res`.

```typescript
// Add a message to the Storage queue.
context.bindings.msg = "Name passed to the function: " + name;
```

Neste ponto, a sua função deve ser a seguinte:

```javascript
import { AzureFunction, Context, HttpRequest } from "@azure/functions"

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    context.log('HTTP trigger function processed a request.');
    const name = (req.query.name || (req.body && req.body.name));

    if (name) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " + name; 
        // Send a "hello" response.
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};

export default httpTrigger;
```

::: zone-end

::: zone pivot="programming-language-powershell"

Adicione o código que utiliza o `Push-OutputBinding` cmdlet para escrever texto à fila utilizando a ligação de saída `msg`. Adicione este código antes de definir o estado OK na declaração `if`.

```powershell
# Write the $name value to the queue.
$outputMsg = "Name passed to the function: $name"
Push-OutputBinding -name msg -Value $outputMsg
```

Neste ponto, a sua função deve ser a seguinte:

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$name = $Request.Query.Name
if (-not $name) {
    $name = $Request.Body.Name
}

if ($name) {
    # Write the $name value to the queue.
    $outputMsg = "Name passed to the function: $name"
    Push-OutputBinding -name msg -Value $outputMsg

    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
else {
    $status = [HttpStatusCode]::BadRequest
    $body = "Please pass a name on the query string or in the request body."
}

# Associate values to output bindings by calling 'Push-OutputBinding'.
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```

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

1. Executar a ferramenta [Explorador do Armazenamento do Azure] selecione o ícone de ligação à esquerda e selecione **Adicionar uma conta**.

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

1. Em Visual Studio Code, pressione F1 para abrir a paleta de comandos. Na paleta de comandos, procure e selecione `Azure Functions: Deploy to function app...`.

1. Escolha a aplicação de funções que criou no primeiro artigo. Como está a recolocar o seu projeto na mesma aplicação, selecione **Deploy** para descartar o aviso sobre ficheiros de sobreposição.

1. Após a implementação concluída, pode voltar a utilizar cURL ou um browser para testar a função reimplantada. Como antes, acrescente a cadeia de caracteres de consulta `&name=<yourname>` à URL, como no exemplo a seguir:

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. Volte [a ver a mensagem na fila de armazenamento](#examine-the-output-queue) para verificar se a ligação de saída gera novamente uma nova mensagem na fila.

## <a name="clean-up-resources"></a>Limpar recursos

Os *recursos* no Azure referem-se a aplicações de funções, funções, contas de armazenamento e assim sucessivamente. Estes são agrupados em *grupos de recursos* e pode eliminar tudo num grupo ao eliminá-lo.

Criou recursos para concluir estes guias de introdução. Poderá ser-lhe cobrado estes recursos, dependendo do seu [estado da conta](https://azure.microsoft.com/account/) e dos [preços dos serviços](https://azure.microsoft.com/pricing/). Se já não precisar dos recursos, pode eliminá-los da seguinte forma:

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Passos seguintes

Atualizou a sua função de http para escrever dados para uma fila de Armazenamento. Em seguida, você pode aprender mais sobre o desenvolvimento de Funções usando o Código de Estúdio Visual:

> [!div class="nextstepaction"]
> [Desenvolver funções azure usando código de estúdio visual](functions-develop-vs-code.md)

[Explorador do Armazenamento do Azure]: https://storageexplorer.com/

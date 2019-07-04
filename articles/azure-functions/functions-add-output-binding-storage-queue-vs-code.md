---
title: Ligar as funções para o armazenamento do Azure com o Visual Studio Code
description: Saiba como adicionar um enlace de saída para ligar as suas funções a uma fila de armazenamento do Azure com o Visual Studio Code.
author: ggailey777
ms.author: glenga
ms.date: 06/25/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
manager: jeconnoc
ms.openlocfilehash: b207064f691391af2c180c7a6ab03e42ed79fcb6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450997"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio-code"></a>Ligar as funções para o armazenamento do Azure com o Visual Studio Code

As funções do Azure permite-lhe ligar as funções serviços do Azure e outros recursos sem ter de escrever seu próprio código de integração. Estes *enlaces*, que representam a entrada e saída, são declarados dentro da definição de função. Dados a partir de ligações são fornecidos para a função como parâmetros. Um acionador é um tipo especial de enlace de entrada. Enquanto uma função tem apenas um acionador, ele pode ter várias entradas e ligações de saída. Para obter mais informações, consulte [acionadores de funções do Azure e conceitos de enlaces](functions-triggers-bindings.md).

Este artigo mostra-lhe como utilizar o Visual Studio Code para ligar a função que criou no [artigo de início rápido anterior](functions-create-first-function-vs-code.md) ao armazenamento do Azure. O enlace de saída que adicionar a esta função escreve dados de solicitação HTTP para uma mensagem numa fila de armazenamento de filas do Azure. 

A maioria dos enlaces necessitam de uma cadeia de ligação armazenadas que as funções utilizam para aceder ao serviço ligado. Para tornar mais fácil, utilize a conta de armazenamento que criou com a sua aplicação de função. A ligação a esta conta já armazenada numa definição com o nome de aplicação `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, tem de cumprir os seguintes requisitos:

* Instalar o [extensão de armazenamento do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage).
* Instale [Explorador de armazenamento do Azure](https://storageexplorer.com/). Explorador de armazenamento é uma ferramenta que usará para examinar as mensagens de fila geradas pelo seu enlace de saída. Explorador de armazenamento é suportado em macOS, Windows e sistemas operacionais baseados em Linux.
* Instale [ferramentas da CLI do .NET Core](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x) (C# projetos apenas).
* Conclua os passos na [parte 1 do início rápido do Visual Studio Code](functions-create-first-function-vs-code.md). 

Este artigo pressupõe que já têm sessão iniciada sua subscrição do Azure do Visual Studio Code. Pode iniciar sessão através da execução `Azure: Sign In` da paleta de comandos. 

## <a name="download-the-function-app-settings"></a>Transferir as definições de aplicação de função

Na [artigo de início rápido anterior](functions-create-first-function-vs-code.md), criou uma aplicação de funções no Azure, juntamente com a conta de armazenamento necessária. A cadeia de ligação para esta conta é armazenada em segurança nas definições da aplicação no Azure. Neste artigo, escrever mensagens numa fila de armazenamento na mesma conta. Para ligar à sua conta de armazenamento ao executar localmente a função, tem de transferir as definições da aplicação para o ficheiro Settings. 

1. Prima a tecla F1 para abrir a paleta de comandos, em seguida, procure e execute o comando `Azure Functions: Download Remote Settings....`. 

1. Escolha a aplicação de função que criou no artigo anterior. Selecione **Sim para todos** para substituir as definições de locais existentes. 

    > [!IMPORTANT]  
    > Porque contém segredos, o arquivo Settings nunca seja publicado e está excluído do controlo de origem.

1. Copie o valor `AzureWebJobsStorage`, que é a chave para o valor de cadeia de ligação de conta de armazenamento. Utilize esta ligação para verificar se o enlace de saída funciona conforme esperado.

## <a name="register-binding-extensions"></a>Registar as extensões de enlace

Uma vez que estiver a utilizar uma enlace de saída do armazenamento filas, tem de ter a extensão de enlaces de armazenamento instalada antes de executar o projeto. 

### <a name="javascript"></a>JavaScript

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

### <a name="c-class-library"></a>C\# biblioteca de classes

Com exceção dos acionadores HTTP e temporizador, os enlaces são implementados como pacotes de extensão. Execute o seguinte [dotnet Adicionar pacote](/dotnet/core/tools/dotnet-add-package) comando na janela de Terminal para adicionar o pacote de extensão de armazenamento ao seu projeto.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

Agora, pode adicionar a saída de armazenamento de ligação ao seu projeto.

## <a name="add-an-output-binding"></a>Adicionar um enlace de saída

Em funções, cada tipo de ligação requer um `direction`, `type`e um único `name` ser definidos no ficheiro Function. A forma como define esses atributos depende do idioma da sua aplicação de função.

### <a name="javascript"></a>JavaScript

Atributos do enlace são definidos diretamente no ficheiro de Function. Dependendo do tipo de ligação, as propriedades adicionais podem ser necessárias. O [configuração da fila de saída](functions-bindings-storage-queue.md#output---configuration) descreve os campos necessários para um enlace de fila de armazenamento do Azure. A extensão torna mais fácil adicionar enlaces para o ficheiro de Function. 

Para criar uma ligação, botão direito do mouse (Ctrl + clique em macOS) a `function.json` de ficheiros na pasta HttpTrigger e escolha **Adicionar enlace de...** . Siga as instruções para definir as seguintes propriedades de ligação para o novo enlace:

| Mensagem | Value | Descrição |
| -------- | ----- | ----------- |
| **Selecione a direção de ligação** | `out` | A associação é um enlace de saída. |
| **Selecione a ligação com a direção...** | `Azure Queue Storage` | A associação é um enlace de fila de armazenamento do Azure. |
| **O nome utilizado para identificar este enlace no código** | `msg` | Nome que identifica o parâmetro de ligação referenciado no seu código. |
| **A fila para o qual a mensagem será enviada** | `outqueue` | O nome da fila na qual o enlace escreve. Quando o *queueName* não existir, o enlace cria-o na primeira utilização. |
| **Selecione a definição de "local.setting.json"** | `AzureWebJobsStorage` | O nome de uma definição da aplicação que contém a cadeia de ligação para a conta de armazenamento. O `AzureWebJobsStorage` definição contém a cadeia de ligação para a conta de armazenamento que criou com a aplicação de funções. |

Um enlace é adicionado ao `bindings` matriz no seu ficheiro Function, que deverá agora ser semelhante ao seguinte exemplo:

```json
{
   ...

  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

### <a name="c-class-library"></a>C\# biblioteca de classes

Num C# projeto de biblioteca de classes, as ligações são definidas como atributos de ligação no método de função. O ficheiro de Function, em seguida, é gerado automaticamente com base nesses atributos.

Abra o ficheiro de projeto HttpTrigger.cs e adicione o seguinte `using` instrução:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

Adicione o seguinte parâmetro para o `Run` definição de método:

```cs
[Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
```

O `msg` parâmetro é um `ICollector<T>` tipo, que representa uma coleção de mensagens que são escritos para uma saída de ligação quando a função for concluída. Neste caso, o resultado é uma fila de armazenamento com o nome `outqueue`. A cadeia de ligação para a conta de armazenamento é definida pelo `StorageAccountAttribute`. Este atributo indica a definição que contém a cadeia de ligação de conta de armazenamento e pode ser aplicado à classe, método ou nível de parâmetro. Neste caso, poderia omitir `StorageAccountAttribute` porque já está a utilizar a conta de armazenamento predefinida.

A definição do método Run deve agora ter um aspeto semelhante ao seguinte:  

```cs
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
```

## <a name="add-code-that-uses-the-output-binding"></a>Adicione código que utiliza o enlace de saída

Depois da ligação é definida, pode utilizar o `name` do enlace para acessá-lo como um atributo na assinatura de função. Ao utilizar um enlace de saída, não tem de utilizar o código do SDK de armazenamento do Azure para a autenticação, obter uma referência de fila, ou escrever dados. A saída de tempo de execução e a fila de funções enlace executar essas tarefas para.

### <a name="javascript"></a>JavaScript

Adicione código que utiliza a `msg` objeto de enlace de saída no `context.bindings` para criar uma mensagem de fila. Adicione este código antes da declaração `context.res`.

```javascript
// Add a message to the Storage queue.
context.bindings.msg = "Name passed to the function: " + 
(req.query.name || req.body.name);
```

Neste momento, a função deve ter o seguinte aspeto:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    if (req.query.name || (req.body && req.body.name)) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " + 
        (req.query.name || req.body.name);
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
```

### <a name="c"></a>C\#

Adicione código que utiliza o `msg` objeto de enlace de saída para criar uma mensagem de fila. Adicione este código antes do método retorna.

```cs
if (!string.IsNullOrEmpty(name))
{
    // Add a message to the output collection.
    msg.Add(string.Format("Name passed to the function: {0}", name));
}
```

Neste momento, a função deve ter o seguinte aspeto:

```cs
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    if (!string.IsNullOrEmpty(name))
    {
        // Add a message to the output collection.
        msg.Add(string.Format("Name passed to the function: {0}", name));
    }
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Da primeira vez que o enlace de saída é utilizado, o runtime das Funções cria uma fila nova com o nome **outqueue** na sua conta de Armazenamento. Vai utilizar o Explorador de armazenamento para verificar se a fila foi criada, juntamente com a nova mensagem.

### <a name="connect-storage-explorer-to-your-account"></a>Ligar o Explorador de Armazenamento à sua conta

Ignore esta secção se já tiver instalado o Explorador de armazenamento do Azure e ligado à sua conta do Azure.

1. Executar o [Explorador do Armazenamento do Azure] ferramenta, selecione o ícone de ligação no lado esquerdo e selecione **adicionar uma conta**.

    ![Adicione um Azure da conta para o Explorador de armazenamento do Microsoft Azure](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. Na **Connect** caixa de diálogo, escolha **adicionar uma conta do Azure**, escolha sua **ambiente do Azure**e selecione **iniciar sessão...** . 

    ![Inicie sessão na sua conta do Azure](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

Depois que inicie sessão com êxito sua conta, verá todas as subscrições do Azure associadas à sua conta.

### <a name="examine-the-output-queue"></a>Examinar a fila de saída

1. No Visual Studio Code, prima a tecla F1 para abrir a paleta de comandos, em seguida, procure e execute o comando `Azure Storage: Open in Storage Explorer` e escolha o nome da sua conta de armazenamento. Abre-se da sua conta de armazenamento no Explorador de armazenamento do Azure.  

1. Expanda o nó **Filas** nó e, em seguida, selecione a fila com o nome **outqueue**. 

   A fila contém a mensagem que a fila de enlace de saída da fila criou quando executou a função acionada por HTTP. Se invocou a função com a predefinição `name` valor de *Azure*, a mensagem de fila é *nome transmitido para a função: Azure*.

    ![Mensagem de fila apresentada no Explorador de armazenamento do Azure](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Executar a função mais uma vez, enviar outro pedido, e verá uma nova mensagem aparecer na fila.  

Agora, é hora de voltar a publicar a aplicação de função atualizada para o Azure.

## <a name="redeploy-and-test-the-updated-app"></a>Voltar a implementar e testar a aplicação atualizada

1. No Visual Studio Code, prima F1 para abrir a paleta de comandos. Na paleta de comandos, procure e selecione `Azure Functions: Deploy to function app...`.

1. Escolha a aplicação de funções que criou no primeiro artigo. Uma vez que estiver a Reimplementar o seu projeto para a mesma aplicação, selecione **Deploy** para ignorar o aviso sobre Sobrescrever arquivos.

1. Depois de concluída a implementação, pode utilizar novamente cURL ou um browser para testar a função reimplantada. Como antes, anexe a cadeia de consulta `&name=<yourname>` para o URL, como no exemplo seguinte:

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. Novamente [ver a mensagem na fila de armazenamento](#examine-the-output-queue) para verificar que a saída novamente a vinculação gera uma nova mensagem na fila.

## <a name="clean-up-resources"></a>Limpar recursos

Os *recursos* no Azure referem-se a aplicações de funções, funções, contas de armazenamento e assim sucessivamente. Estes são agrupados em *grupos de recursos* e pode eliminar tudo num grupo ao eliminá-lo.

Criou recursos para concluir estes guias de introdução. Poderá ser-lhe cobrado estes recursos, dependendo do seu [estado da conta](https://azure.microsoft.com/account/) e dos [preços dos serviços](https://azure.microsoft.com/pricing/). Se já não precisar dos recursos, pode eliminá-los da seguinte forma:

1. No Visual Studio Code, prima F1 para abrir a paleta de comandos. Na paleta de comandos, procure e selecione `Azure Functions: Open in portal`.

1. Escolha a sua aplicação de função e prima Enter. A página da aplicação de função é aberta no [portal do Azure](https://portal.azure.com).

1. Na **descrição geral** separador, selecione a ligação com nome em **grupo de recursos**.

    ![Selecione o grupo de recursos a eliminar na página da aplicação de função.](./media/functions-add-output-binding-storage-queue-vs-code/functions-app-delete-resource-group.png)

1. Na página **Grupo de recursos**, reveja a lista dos recursos incluídos e certifique-se de que são aqueles que pretende eliminar.
 
1. Selecione **Eliminar grupo de recursos** e siga as instruções.

   A eliminação pode demorar alguns minutos. Quando terminar, é apresentada uma notificação durante alguns segundos. Também pode selecionar o ícone de sino na parte superior da página para ver a notificação.

## <a name="next-steps"></a>Passos Seguintes

Atualizou a função de acionada por HTTP para escrever dados para uma fila de armazenamento. Para saber mais sobre o desenvolvimento das funções, veja [desenvolver as funções do Azure com o Visual Studio Code](functions-develop-vs-code.md).

Em seguida, deve ativar a monitorização do Application Insights para a sua aplicação de função:

> [!div class="nextstepaction"]
> [Ativar a integração do Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Explorador do Armazenamento do Azure]: https://storageexplorer.com/

---
title: Conectar funções ao armazenamento do Azure usando o Visual Studio Code
description: Saiba como adicionar uma associação de saída para conectar suas funções a uma fila de armazenamento do Azure usando Visual Studio Code.
author: ggailey777
ms.author: glenga
ms.date: 06/25/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
manager: jeconnoc
ms.openlocfilehash: 40a912a94dc61342c04528e902bb0e084546904d
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592806"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio-code"></a>Conectar funções ao armazenamento do Azure usando o Visual Studio Code

Azure Functions permite que você conecte funções a serviços do Azure e a outros recursos sem precisar escrever seu próprio código de integração. Essas *associações*, que representam entrada e saída, são declaradas dentro da definição da função. Os dados das associações são fornecidos para a função como parâmetros. Um gatilho é um tipo especial de associação de entrada. Embora uma função tenha apenas um gatilho, ela pode ter várias associações de entrada e saída. Para saber mais, confira [Azure Functions os conceitos de gatilhos e associações](functions-triggers-bindings.md).

Este artigo mostra como usar Visual Studio Code para conectar a função que você criou no artigo de [início rápido anterior](functions-create-first-function-vs-code.md) ao armazenamento do Azure. A associação de saída que você adiciona a essa função grava dados da solicitação HTTP em uma mensagem em uma fila de armazenamento de filas do Azure. 

A maioria das associações requer uma cadeia de conexão armazenada que o Functions usa para acessar o serviço associado. Para facilitar, use a conta de armazenamento que você criou com seu aplicativo de funções. A conexão com essa conta já está armazenada em uma configuração de `AzureWebJobsStorage`aplicativo chamada.  

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este artigo, você deve atender aos seguintes requisitos:

* Instale a [extensão de armazenamento do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage).
* Instale o [Gerenciador de armazenamento do Azure](https://storageexplorer.com/). Gerenciador de Armazenamento é uma ferramenta que você usará para examinar as mensagens da fila geradas pela sua associação de saída. O Gerenciador de Armazenamento é compatível com sistemas operacionais macOS, Windows e Linux.
* Instalar as [ferramentas](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x) deC# CLI do .NET Core (somente projetos).
* Conclua as etapas na [parte 1 do guia de início rápido do Visual Studio Code](functions-create-first-function-vs-code.md). 

Este artigo pressupõe que você já tenha entrado em sua assinatura do Azure de Visual Studio Code. Você pode entrar executando `Azure: Sign In` na paleta de comandos. 

## <a name="download-the-function-app-settings"></a>Baixar as configurações do aplicativo de funções

No [artigo de início rápido anterior](functions-create-first-function-vs-code.md), você criou um aplicativo de funções no Azure junto com a conta de armazenamento necessária. A cadeia de conexão para essa conta é armazenada com segurança nas configurações do aplicativo no Azure. Neste artigo, você grava mensagens em uma fila de armazenamento na mesma conta. Para se conectar à sua conta de armazenamento ao executar a função localmente, você deve baixar as configurações do aplicativo para o arquivo local. Settings. JSON. 

1. Pressione a tecla F1 para abrir a paleta de comandos e, em seguida, procure e `Azure Functions: Download Remote Settings....`execute o comando. 

1. Escolha o aplicativo de funções criado no artigo anterior. Selecione **Sim para todos** para substituir as configurações locais existentes. 

    > [!IMPORTANT]  
    > Como ele contém segredos, o arquivo local. Settings. JSON nunca é publicado e é excluído do controle do código-fonte.

1. Copie o valor `AzureWebJobsStorage`, que é a chave para o valor da cadeia de conexão da conta de armazenamento. Use essa conexão para verificar se a associação de saída funciona conforme o esperado.

## <a name="register-binding-extensions"></a>Registar as extensões de enlace

Como você está usando uma associação de saída de armazenamento de fila, você deve ter a extensão de associações de armazenamento instalada antes de executar o projeto. 

### <a name="javascript"></a>JavaScript

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

### <a name="c-class-library"></a>Biblioteca\# de classes C

Com exceção dos gatilhos HTTP e Timer, as associações são implementadas como pacotes de extensão. Execute o comando [dotnet adicionar pacote](/dotnet/core/tools/dotnet-add-package) a seguir na janela do terminal para adicionar o pacote de extensão de armazenamento ao seu projeto.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

Agora, você pode adicionar a associação de saída de armazenamento ao seu projeto.

## <a name="add-an-output-binding"></a>Adicionar um enlace de saída

Em funções, cada tipo de associação requer um `direction`, `type`e um exclusivo `name` a ser definido no arquivo function. JSON. A maneira como você define esses atributos depende do idioma do seu aplicativo de funções.

### <a name="javascript"></a>JavaScript

Os atributos de associação são definidos diretamente no arquivo function. JSON. Dependendo do tipo de associação, podem ser necessárias propriedades adicionais. A [configuração de saída de fila](functions-bindings-storage-queue.md#output---configuration) descreve os campos necessários para uma associação de fila de armazenamento do Azure. A extensão facilita a adição de associações ao arquivo function. JSON. 

Para criar uma associação, clique com o botão direito do mouse (Ctrl + clique `function.json` em MacOS) o arquivo na pasta HttpTrigger e escolha **Adicionar associação...** . Siga os prompts para definir as seguintes propriedades de associação para a nova associação:

| Mensagem | Value | Descrição |
| -------- | ----- | ----------- |
| **Selecionar direção da Associação** | `out` | A associação é uma associação de saída. |
| **Selecionar associação com direção...** | `Azure Queue Storage` | A associação é uma associação de fila de armazenamento do Azure. |
| **O nome usado para identificar essa associação em seu código** | `msg` | Nome que identifica o parâmetro de associação referenciado em seu código. |
| **A fila para a qual a mensagem será enviada** | `outqueue` | O nome da fila na qual a associação é gravada. Quando o *QueueName* não existe, a associação o cria no primeiro uso. |
| **Selecione configuração em "local. setting. JSON"** | `AzureWebJobsStorage` | O nome de uma configuração de aplicativo que contém a cadeia de conexão para a conta de armazenamento. A `AzureWebJobsStorage` configuração contém a cadeia de conexão para a conta de armazenamento que você criou com o aplicativo de funções. |

Uma associação é adicionada à `bindings` matriz em seu arquivo function. JSON, que agora deve ser semelhante ao exemplo a seguir:

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

### <a name="c-class-library"></a>Biblioteca\# de classes C

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Adicione código que utiliza o enlace de saída

Depois que a associação é definida, você pode usar `name` o da Associação para acessá-la como um atributo na assinatura da função. Usando uma associação de saída, você não precisa usar o código do SDK de armazenamento do Azure para autenticação, obter uma referência de fila ou gravar dados. O tempo de execução de funções e a associação de saída de fila executam essas tarefas para você.

### <a name="javascript"></a>JavaScript

Adicione o código que usa `msg` o objeto de associação `context.bindings` de saída no para criar uma mensagem de fila. Adicione este código antes da declaração `context.res`.

```javascript
// Add a message to the Storage queue.
context.bindings.msg = "Name passed to the function: " + 
(req.query.name || req.body.name);
```

Neste ponto, sua função deve ter a seguinte aparência:

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

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Uma nova fila chamada **outqueue** é criada em sua conta de armazenamento pelo tempo de execução do Functions quando a associação de saída é usada pela primeira vez. Você usará Gerenciador de Armazenamento para verificar se a fila foi criada junto com a nova mensagem.

### <a name="connect-storage-explorer-to-your-account"></a>Ligar o Explorador de Armazenamento à sua conta

Ignore esta seção se você já tiver instalado Gerenciador de Armazenamento do Azure e conectado à sua conta do Azure.

1. Execute a ferramenta [Explorador do Armazenamento do Azure] , selecione o ícone conectar à esquerda e selecione **Adicionar uma conta**.

    ![Adicionar uma conta do Azure ao Gerenciador de Armazenamento do Microsoft Azure](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. Na caixa de diálogo **conectar** , escolha **Adicionar uma conta do Azure**, escolha o **ambiente do Azure**e selecione **entrar...** . 

    ![Inicie sessão na sua conta do Azure](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

Depois de entrar com êxito em sua conta, você verá todas as assinaturas do Azure associadas à sua conta.

### <a name="examine-the-output-queue"></a>Examinar a fila de saída

1. Em Visual Studio Code, pressione a tecla F1 para abrir a paleta de comandos e, em seguida, pesquise e `Azure Storage: Open in Storage Explorer` execute o comando e escolha o nome da conta de armazenamento. Sua conta de armazenamento é aberta no Gerenciador de Armazenamento do Azure.  

1. Expanda o nó **Filas** nó e, em seguida, selecione a fila com o nome **outqueue**. 

   A fila contém a mensagem que a fila de enlace de saída da fila criou quando executou a função acionada por HTTP. Se você tiver chamado a função com o `name` valor padrão do *Azure*, a mensagem da *fila será o nome passado para a função: Azure*.

    ![Mensagem da fila mostrada em Gerenciador de Armazenamento do Azure](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Execute a função novamente, envie outra solicitação e você verá uma nova mensagem aparecendo na fila.  

Agora, é hora de republicar o aplicativo de funções atualizado no Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>Reimplantar e verificar o aplicativo atualizado

1. Em Visual Studio Code, pressione F1 para abrir a paleta de comandos. Na paleta de comandos, procure e selecione `Azure Functions: Deploy to function app...`.

1. Escolha o aplicativo de funções que você criou no primeiro artigo. Como você está reimplantando seu projeto no mesmo aplicativo, selecione **implantar** para ignorar o aviso sobre a substituição de arquivos.

1. Após a conclusão da implantação, você pode novamente usar a rotação ou um navegador para testar a função reimplantada. Como antes, acrescente a cadeia de `&name=<yourname>` caracteres de consulta à URL, como no exemplo a seguir:

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. Novamente, [exiba a mensagem na fila de armazenamento](#examine-the-output-queue) para verificar se a associação de saída gera novamente uma nova mensagem na fila.

## <a name="clean-up-resources"></a>Limpar recursos

Os *recursos* no Azure referem-se a aplicações de funções, funções, contas de armazenamento e assim sucessivamente. Estes são agrupados em *grupos de recursos* e pode eliminar tudo num grupo ao eliminá-lo.

Criou recursos para concluir estes guias de introdução. Poderá ser-lhe cobrado estes recursos, dependendo do seu [estado da conta](https://azure.microsoft.com/account/) e dos [preços dos serviços](https://azure.microsoft.com/pricing/). Se já não precisar dos recursos, pode eliminá-los da seguinte forma:

1. Em Visual Studio Code, pressione F1 para abrir a paleta de comandos. Na paleta de comandos, procure e selecione `Azure Functions: Open in portal`.

1. Escolha seu aplicativo de funções e pressione Enter. A página do aplicativo de funções é aberta no [portal do Azure](https://portal.azure.com).

1. Na guia **visão geral** , selecione o link nomeado em **grupo de recursos**.

    ![Selecione o grupo de recursos a eliminar na página da aplicação de função.](./media/functions-add-output-binding-storage-queue-vs-code/functions-app-delete-resource-group.png)

1. Na página **Grupo de recursos**, reveja a lista dos recursos incluídos e certifique-se de que são aqueles que pretende eliminar.
 
1. Selecione **Eliminar grupo de recursos** e siga as instruções.

   A eliminação pode demorar alguns minutos. Quando terminar, é apresentada uma notificação durante alguns segundos. Também pode selecionar o ícone de sino na parte superior da página para ver a notificação.

## <a name="next-steps"></a>Passos seguintes

Você atualizou sua função disparada por HTTP para gravar dados em uma fila de armazenamento. Para saber mais sobre o desenvolvimento de funções, consulte [desenvolver Azure Functions usando Visual Studio Code](functions-develop-vs-code.md).

Em seguida, você deve habilitar o monitoramento de Application Insights para seu aplicativo de funções:

> [!div class="nextstepaction"]
> [Habilitar integração de Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Explorador do Armazenamento do Azure]: https://storageexplorer.com/

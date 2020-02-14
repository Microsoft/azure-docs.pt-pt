---
title: Ligue a sua função Java ao Armazenamento Azure
description: Aprenda a ligar uma função Java acionada pelo HTTP ao Armazenamento Azure utilizando uma ligação de saída de armazenamento de fila.
ms.date: 10/14/2019
ms.topic: quickstart
ms.openlocfilehash: 72e3aad15ea8ef922d89a67891e223b65473b909
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198552"
---
# <a name="connect-your-java-function-to-azure-storage"></a>Ligue a sua função Java ao Armazenamento Azure

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Este artigo mostra-lhe como integrar a função que criou no [artigo quickstart anterior](functions-create-first-java-maven.md) com uma fila de Armazenamento Azure. O encadernação de saída que adiciona a esta função escreve dados de um pedido HTTP para uma mensagem na fila.

A maioria das ligações requer uma cadeia de ligação armazenada que as Funções usam para aceder ao serviço de encadernação. Para facilitar esta ligação, utiliza a conta de Armazenamento que criou com a sua aplicação de funções. A ligação a esta conta já está armazenada numa definição de aplicação chamada `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este artigo, complete os passos na [parte 1 do início rápido de Java.](functions-create-first-java-maven.md)

## <a name="download-the-function-app-settings"></a>Descarregue as definições da aplicação de funções

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Ativar pacotes de extensão

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Agora pode adicionar a ligação de saída de Armazenamento ao seu projeto.

## <a name="add-an-output-binding"></a>Adicionar um enlace de saída

Num projeto java, as encadernações são definidas como anotações vinculativas no método da função. O ficheiro *função.json* é então autogerado com base nestas anotações.

Navegue na localização do seu código de funções sob _o sRC/main/java,_ abra o ficheiro do projeto *Function.java* e adicione o seguinte parâmetro à definição de método `run`:

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

O parâmetro `msg` é um tipo [`OutputBinding<T>`,](/java/api/com.microsoft.azure.functions.outputbinding) que representa uma coleção de cordas que são escritas como mensagens para uma ligação de saída quando a função completa. Neste caso, a saída é uma fila de armazenamento chamada `outqueue`. A cadeia de ligação para a conta de armazenamento é definida pelo método `connection`. Em vez da cadeia de ligação em si, passa a definição de aplicação que contém a cadeia de ligação da conta de armazenamento.

A definição de método `run` deve agora parecer o seguinte exemplo:  

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION)  
        HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    ...
}
```

## <a name="add-code-that-uses-the-output-binding"></a>Adicione código que utiliza o enlace de saída

Agora, pode usar o novo parâmetro de `msg` para escrever para a ligação de saída a partir do seu código de funcionamento. Adicione a seguinte linha de código antes da resposta de sucesso para adicionar o valor de `name` à ligação de saída `msg`.

```java
msg.setValue(name);
```

Quando utiliza uma ligação de saída, não é necessário utilizar o código SDK de Armazenamento Azure para autenticação, obtendo uma referência de fila ou escrevendo dados. O tempo de funcionamento das Funções e a ligação de saída da fila fazem essas tarefas para si.

O seu método `run` deve agora parecer o seguinte exemplo:

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    context.getLogger().info("Java HTTP trigger processed a request.");

    // Parse query parameter
    String query = request.getQueryParameters().get("name");
    String name = request.getBody().orElse(query);

    if (name == null) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST).body("Please pass a name on the query string or in the request body").build();
    } else {
        // Write the name to the message queue. 
        msg.setValue(name);

        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
    }
}
```

## <a name="update-the-tests"></a>Atualizar os testes

Como o arquétipo também cria um conjunto de testes, é necessário atualizar estes testes para lidar com o novo parâmetro de `msg` na assinatura do método `run`.  

Navegue na localização do seu código de teste sob _o sRC/test/java,_ abra o ficheiro do projeto *Function.java* e substitua a linha de código sob `//Invoke` pelo seguinte código.

```java
@SuppressWarnings("unchecked")
final OutputBinding<String> msg = (OutputBinding<String>)mock(OutputBinding.class);

// Invoke
final HttpResponseMessage ret = new Function().run(req, msg, context);
``` 

Está pronto para experimentar a nova ligação de saída local.

## <a name="run-the-function-locally"></a>Executar localmente a função

Como antes, utilize o seguinte comando para construir o projeto e iniciar o funcionamento das Funções localmente:

```bash
mvn clean package 
mvn azure-functions:run
```

> [!NOTE]  
> Uma vez que permitiu pacotes de extensão no host.json, a extensão de [encadernação de Armazenamento](functions-bindings-storage-blob.md#add-to-your-functions-app) foi descarregada e instalada para si durante o arranque, juntamente com as outras extensões de ligação da Microsoft.

Como antes, acionar a função a partir da linha de comando utilizando cURL numa nova janela de terminais:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

Desta vez, a ligação de saída também cria uma fila chamada `outqueue` na sua conta de Armazenamento e adiciona uma mensagem com esta mesma corda.

Em seguida, usa o Azure CLI para ver a nova fila e verificar se foi adicionada uma mensagem. Também pode ver a sua fila utilizando o [Microsoft Azure Storage Explorer][Azure Storage Explorer] ou no portal [Azure](https://portal.azure.com).

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Reutilizar o projeto 

Para atualizar a sua aplicação publicada, volte a executar o seguinte comando:  

```azurecli
mvn azure-functions:deploy
```

Mais uma vez, pode utilizar cURL para testar a função implantada. Como antes, passe o valor `AzureFunctions` no corpo do pedido post para o URL, como neste exemplo:

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Pode [examinar novamente a mensagem de fila](#query-the-storage-queue) de armazenamento para verificar se a ligação de saída gera uma nova mensagem na fila, como esperado.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Atualizou a sua função de HTTP para escrever dados para uma fila de Armazenamento. Para saber mais sobre o desenvolvimento de Funções Azure com Java, consulte o guia de desenvolvimento das [Funções Azure Java](functions-reference-java.md) e os [gatilhos e encadernações de Funções Azure.](functions-triggers-bindings.md) Por exemplo, em Java, consulte as amostras de [Funções Java.](/samples/browse/?products=azure-functions&languages=Java) 

Em seguida, deve ativar a monitorização de Insights de Aplicação para a sua aplicação de funções:

> [!div class="nextstepaction"]
> [Ativar a integração do Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)


[Azure Storage Explorer]: https://storageexplorer.com/
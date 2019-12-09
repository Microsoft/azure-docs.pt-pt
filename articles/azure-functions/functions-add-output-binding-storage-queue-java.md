---
title: Conectar sua função Java ao armazenamento do Azure
description: Saiba como conectar uma função Java disparada por HTTP ao armazenamento do Azure usando uma associação de saída de armazenamento de fila.
ms.date: 10/14/2019
ms.topic: quickstart
ms.openlocfilehash: 98f49338f0df935347a26798aceccb80f9f43f50
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926904"
---
# <a name="connect-your-java-function-to-azure-storage"></a>Conectar sua função Java ao armazenamento do Azure

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Este artigo mostra como integrar a função que você criou no artigo de [início rápido anterior](functions-create-first-java-maven.md) com uma fila de armazenamento do Azure. A associação de saída que você adiciona a essa função grava dados de uma solicitação HTTP em uma mensagem na fila.

A maioria das associações requer uma cadeia de conexão armazenada que o Functions usa para acessar o serviço associado. Para facilitar essa conexão, use a conta de armazenamento que você criou com seu aplicativo de funções. A conexão com essa conta já está armazenada em uma configuração de aplicativo chamada `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este artigo, conclua as etapas na [parte 1 do início rápido do Java](functions-create-first-java-maven.md).

## <a name="download-the-function-app-settings"></a>Baixar as configurações do aplicativo de funções

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Habilitar pacotes de extensão

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Agora você pode adicionar a associação de saída de armazenamento ao seu projeto.

## <a name="add-an-output-binding"></a>Adicionar um enlace de saída

Em um projeto Java, as associações são definidas como anotações de associação no método de função. O arquivo *Function. JSON* é gerado automaticamente com base nessas anotações.

Navegue até o local do seu código de função em _src/main/java_, abra o arquivo de projeto *Function. java* e adicione o seguinte parâmetro à definição do método `run`:

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

O parâmetro `msg` é um tipo [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) , que representa uma coleção de cadeias de caracteres que são gravadas como mensagens em uma associação de saída quando a função é concluída. Nesse caso, a saída é uma fila de armazenamento chamada `outqueue`. A cadeia de conexão para a conta de armazenamento é definida pelo método `connection`. Em vez da própria cadeia de conexão, você passa a configuração do aplicativo que contém a cadeia de conexão da conta de armazenamento.

A definição do método de `run` agora deve ser semelhante ao exemplo a seguir:  

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

Agora, você pode usar o novo parâmetro `msg` para gravar na associação de saída do seu código de função. Adicione a seguinte linha de código antes da resposta de êxito para adicionar o valor de `name` à associação de saída `msg`.

```java
msg.setValue(name);
```

Ao usar uma associação de saída, você não precisa usar o código do SDK de armazenamento do Azure para autenticação, obter uma referência de fila ou gravar dados. O tempo de execução de funções e a associação de saída de fila executam essas tarefas para você.

O método de `run` agora deve ser semelhante ao exemplo a seguir:

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

Como o arquétipo também cria um conjunto de testes, você precisa atualizar esses testes para manipular o novo parâmetro `msg` na assinatura do método `run`.  

Navegue até o local do seu código de teste em _src/Test/Java_, abra o arquivo de projeto *Function. java* e substitua a linha de código em `//Invoke` pelo código a seguir.

```java
@SuppressWarnings("unchecked")
final OutputBinding<String> msg = (OutputBinding<String>)mock(OutputBinding.class);

// Invoke
final HttpResponseMessage ret = new Function().run(req, msg, context);
``` 

Agora você está pronto para experimentar a nova associação de saída localmente.

## <a name="run-the-function-locally"></a>Executar localmente a função

Como antes, use o seguinte comando para compilar o projeto e iniciar o tempo de execução do Functions localmente:

```bash
mvn clean package 
mvn azure-functions:run
```

> [!NOTE]  
> Como você habilitou pacotes de extensão no host. JSON, a [extensão de associação de armazenamento](functions-bindings-storage-blob.md#packages---functions-2x-and-higher) foi baixada e instalada para você durante a inicialização, juntamente com as outras extensões de associação da Microsoft.

Como antes, dispare a função na linha de comando usando a rotação em uma nova janela de terminal:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

Desta vez, a associação de saída também cria uma fila chamada `outqueue` em sua conta de armazenamento e adiciona uma mensagem com essa mesma cadeia de caracteres.

Em seguida, use o CLI do Azure para exibir a nova fila e verificar se uma mensagem foi adicionada. Você também pode exibir sua fila usando o [Gerenciador de armazenamento do Microsoft Azure][Azure Storage Explorer] ou no [portal do Azure](https://portal.azure.com).

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Reimplantar o projeto 

Para atualizar seu aplicativo publicado, execute o seguinte comando novamente:  

```azurecli
mvn azure-functions:deploy
```

Novamente, você pode usar a rotação para testar a função implantada. Como antes, passe o valor `AzureFunctions` no corpo da solicitação POST para a URL, como neste exemplo:

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Você pode [examinar a mensagem da fila de armazenamento](#query-the-storage-queue) novamente para verificar se a associação de saída gera uma nova mensagem na fila, conforme o esperado.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Você atualizou sua função disparada por HTTP para gravar dados em uma fila de armazenamento. Para saber mais sobre como desenvolver Azure Functions com Java, confira o [Guia do desenvolvedor do java Azure Functions](functions-reference-java.md) e os [gatilhos e as associações do Azure Functions](functions-triggers-bindings.md). Para obter exemplos de projetos de função completos em Java, consulte os [exemplos de funções Java](/samples/browse/?products=azure-functions&languages=Java). 

Em seguida, você deve habilitar o monitoramento de Application Insights para seu aplicativo de funções:

> [!div class="nextstepaction"]
> [Ativar a integração do Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)


[Azure Storage Explorer]: https://storageexplorer.com/
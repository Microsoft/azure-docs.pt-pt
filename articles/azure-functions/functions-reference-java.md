---
title: Referência do desenvolvedor Java para Azure Functions | Microsoft Docs
description: Entenda como desenvolver funções com Java.
author: ggailey777
manager: gwallace
keywords: Azure functions, funções, processamento de eventos, WebHooks, computação dinâmica, arquitetura sem servidor, Java
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: glenga
ms.openlocfilehash: 97c721c504c460856796e296fefc33bf01f002f8
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176428"
---
# <a name="azure-functions-java-developer-guide"></a>Guia do desenvolvedor do Azure Functions Java

O tempo de execução do Azure Functions dá suporte a [Java se 8 LTS (Zulu 8.31.0.2-JRE 8.0.181-win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/). Este guia contém informações sobre as complexidades de escrever Azure Functions com Java.

Como acontece com outras linguagens, um Aplicativo de funções pode ter uma ou mais funções. Uma função Java é um método `public`, decorado com a `@FunctionName`de anotação. Esse método define a entrada para uma função Java e deve ser exclusivo em um pacote específico. Uma Aplicativo de funções escrita em Java pode ter várias classes com vários métodos públicos anotados com `@FunctionName`.

Este artigo pressupõe que você já tenha lido o [Azure Functions referência do desenvolvedor](functions-reference.md). Você também deve concluir o guia de início rápido do Functions para criar sua primeira função, usando [Visual Studio Code](functions-create-first-function-vs-code.md) ou [Maven](functions-create-first-java-maven.md).

## <a name="programming-model"></a>Modelo de programação 

Os conceitos de [gatilhos e associações](functions-triggers-bindings.md) são fundamentais para Azure functions. Os gatilhos iniciam a execução do seu código. As associações fornecem uma maneira de passar dados e retornar dados de uma função, sem precisar escrever código de acesso a dados personalizado.

## <a name="create-java-functions"></a>Criar funções Java

Para facilitar a criação de funções Java, há ferramentas baseadas no Maven e arquétipos que usam modelos Java predefinidos para ajudá-lo a criar projetos com um gatilho de função específico.    

### <a name="maven-based-tooling"></a>Ferramentas baseadas no Maven

Os seguintes ambientes de desenvolvedor têm Azure Functions ferramentas que permitem criar projetos de funções Java: 

+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

O artigo links acima mostra como criar suas primeiras funções usando o IDE de sua escolha. 

### <a name="project-scaffolding"></a>Scaffolding do projeto

Se você preferir o desenvolvimento de linha de comando do terminal, a maneira mais simples de scaffoldr projetos de funções baseados em Java é usar `Apache Maven` arquétipos. Atualmente, há duas funções arquétipos para Maven:

+ **Arquétipo Java**: publicado sob o seguinte GroupID e artefatoid [com. Microsoft. Azure: Azure-Functions-arquétipo](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/):

    ```
    mvn archetype:generate \
        -DarchetypeGroupId=com.microsoft.azure \
        -DarchetypeArtifactId=azure-functions-archetype 
    ```

    Para começar a usar esse arquétipo, consulte o [início rápido do Java](functions-create-first-java-maven.md). 

+ **Arquétipo Kotlin (visualização)** publicado sob o seguinte GroupID e artefatoid [com. Microsoft. Azure: Azure-Functions-Kotlin-arquétipo](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-kotlin-archetype/):

    ```
    mvn archetype:generate \
        -DarchetypeGroupId=com.microsoft.azure \
        -DarchetypeArtifactId=azure-functions-kotlin-archetype
    ```

O código-fonte desses arquétipos pode ser encontrado no [repositório GitHub de arquétipos do Azure Maven](https://github.com/microsoft/azure-maven-archetypes).


## <a name="folder-structure"></a>Estrutura de pastas

Aqui está a estrutura de pastas de um projeto Azure Functions Java:

```
FunctionsProject
 | - src
 | | - main
 | | | - java
 | | | | - FunctionApp
 | | | | | - MyFirstFunction.java
 | | | | | - MySecondFunction.java
 | - target
 | | - azure-functions
 | | | - FunctionApp
 | | | | - FunctionApp.jar
 | | | | - host.json
 | | | | - MyFirstFunction
 | | | | | - function.json
 | | | | - MySecondFunction
 | | | | | - function.json
 | | | | - bin
 | | | | - lib
 | - pom.xml
```

_* O projeto Kotlin parece muito semelhante, pois ainda é Maven_

Você pode usar um arquivo [host. JSON](functions-host-json.md) compartilhado para configurar o aplicativo de funções. Cada função tem seu próprio arquivo de código (. java) e o arquivo de configuração de associação (Function. JSON).

Você pode colocar mais de uma função em um projeto. Evite colocar suas funções em jars separados. O `FunctionApp` no diretório de destino é o que é implantado em seu aplicativo de funções no Azure.

## <a name="triggers-and-annotations"></a>Gatilhos e anotações

 As funções são invocadas por um gatilho, como uma solicitação HTTP, um temporizador ou uma atualização para os dados. Sua função precisa processar esse gatilho e quaisquer outras entradas para produzir uma ou mais saídas.

Use as anotações de Java incluídas no pacote [com. Microsoft. Azure. Functions. Annotation. *](/java/api/com.microsoft.azure.functions.annotation) para associar entradas e saídas aos seus métodos. Para obter mais informações, consulte os [documentos de referência do Java](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> Você deve configurar uma conta de armazenamento do Azure no seu [local. Settings. JSON](/azure/azure-functions/functions-run-local#local-settings-file) para executar o armazenamento de BLOBs do Azure, o armazenamento de filas do Azure ou os gatilhos de armazenamento de tabelas do Azure localmente.

Exemplo:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

Aqui está o `function.json` correspondente gerado pelo [plug-in Azure-Functions-Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin):

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.Function.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="jdk-runtime-availability-and-support"></a>Disponibilidade e suporte de tempo de execução do JDK 

Para o desenvolvimento local de aplicativos de funções Java, baixe e use o [azul Zulu Enterprise para o Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java 8 JDKs de [sistemas azul](https://www.azul.com/downloads/azure-only/zulu/). Azure Functions usa o tempo de execução do Java 8 JDK azul quando você implanta seus aplicativos de função na nuvem.

O [suporte do Azure](https://azure.microsoft.com/support/) para problemas com o JDKs e os aplicativos de função está disponível com um plano de [suporte qualificado](https://azure.microsoft.com/support/plans/).

## <a name="customize-jvm"></a>Personalizar JVM

O Functions permite que você personalize a JVM (máquina virtual Java) usada para executar suas funções Java. As [seguintes opções de JVM](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) são usadas por padrão:

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

Você pode fornecer argumentos adicionais em uma configuração de aplicativo chamada `JAVA_OPTS`. Você pode adicionar configurações de aplicativo ao seu aplicativo de funções implantado no Azure no portal do Azure ou no CLI do Azure.

### <a name="azure-portal"></a>Portal do Azure

No [portal do Azure](https://portal.azure.com), use a [guia Configurações do aplicativo](functions-how-to-use-azure-function-app-settings.md#settings) para adicionar a configuração de `JAVA_OPTS`.

### <a name="azure-cli"></a>CLI do Azure

Você pode usar o comando [AZ functionapp config appSettings Set](/cli/azure/functionapp/config/appsettings) para definir `JAVA_OPTS`, como no exemplo a seguir:

```azurecli-interactive
az functionapp config appsettings set --name <APP_NAME> \
--resource-group <RESOURCE_GROUP> \
--settings "JAVA_OPTS=-Djava.awt.headless=true"
```
Este exemplo habilita o modo sem periféricos. Substitua `<APP_NAME>` pelo nome do seu aplicativo de funções e `<RESOURCE_GROUP>` com o grupo de recursos.

> [!WARNING]  
> No [plano de consumo](functions-scale.md#consumption-plan), você deve adicionar a configuração de `WEBSITE_USE_PLACEHOLDER` com um valor de `0`.  
Essa configuração aumenta as horas de início frio para funções Java.

## <a name="third-party-libraries"></a>Bibliotecas de terceiros 

Azure Functions dá suporte ao uso de bibliotecas de terceiros. Por padrão, todas as dependências especificadas em seu projeto `pom.xml` arquivo são automaticamente agrupadas durante a meta [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) . Para bibliotecas não especificadas como dependências no arquivo de `pom.xml`, coloque-as em um diretório `lib` no diretório raiz da função. As dependências colocadas no diretório `lib` são adicionadas ao carregador de classe do sistema em tempo de execução.

A dependência de `com.microsoft.azure.functions:azure-functions-java-library` é fornecida no classpath por padrão e não precisa ser incluída no diretório `lib`. Além disso, o [Azure-Functions-Java-Worker](https://github.com/Azure/azure-functions-java-worker) adiciona dependências listadas [aqui](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) ao classpath.

## <a name="data-type-support"></a>Suporte ao tipo de dados

Você pode usar objetos Java simples (POJOs), tipos definidos em `azure-functions-java-library`ou tipos de dados primitivos, como String e Integer, para associar a associações de entrada ou saída.

### <a name="pojos"></a>POJOs

Para converter dados de entrada em POJO, o [Azure-Functions-Java-Worker](https://github.com/Azure/azure-functions-java-worker) usa a biblioteca [Gson](https://github.com/google/gson) . Os tipos POJO usados como entradas para funções devem ser `public`.

### <a name="binary-data"></a>Dados binários

Associe entradas binárias ou saídas a `byte[]`, definindo o campo `dataType` em seu function. JSON como `binary`:

```java
   @FunctionName("BlobTrigger")
    @StorageAccount("AzureWebJobsStorage")
     public void blobTrigger(
        @BlobTrigger(name = "content", path = "myblob/{fileName}", dataType = "binary") byte[] content,
        @BindingName("fileName") String fileName,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Blob trigger function processed a blob.\n Name: " + fileName + "\n Size: " + content.length + " Bytes");
    }
```

Se você espera valores nulos, use `Optional<T>`.

## <a name="bindings"></a>Enlaces

As associações de entrada e saída fornecem uma maneira declarativa de se conectar a dados de dentro do seu código. Uma função pode ter várias associações de entrada e saída.

### <a name="input-binding-example"></a>Exemplo de associação de entrada

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData,
    ) {
        testOutputData.setValue(new Person(httpbody + "Partition", httpbody + "Row", httpbody + "Name"));
        return "Hello, " + inputReq + " and " + inputData.getKey() + ".";
    }

    public static class TestInputData {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
    public static class Person {
        public String PartitionKey;
        public String RowKey;
        public String Name;

        public Person(String p, String r, String n) {
            this.PartitionKey = p;
            this.RowKey = r;
            this.Name = n;
        }
    }
}
```

Você invoca essa função com uma solicitação HTTP. 
- A carga de solicitação HTTP é passada como um `String` para o argumento `inputReq`.
- Uma entrada é recuperada do armazenamento de tabela e é passada como `TestInputData` para o argumento `inputData`.

Para receber um lote de entradas, você pode associar a `String[]`, `POJO[]`, `List<String>`ou `List<POJO>`.

```java
@FunctionName("ProcessIotMessages")
    public void processIotMessages(
        @EventHubTrigger(name = "message", eventHubName = "%AzureWebJobsEventHubPath%", connection = "AzureWebJobsEventHubSender", cardinality = Cardinality.MANY) List<TestEventData> messages,
        final ExecutionContext context)
    {
        context.getLogger().info("Java Event Hub trigger received messages. Batch size: " + messages.size());
    }
    
    public class TestEventData {
    public String id;
}

```

Essa função é disparada sempre que há novos dados no Hub de eventos configurado. Como o `cardinality` é definido como `MANY`, a função recebe um lote de mensagens do hub de eventos. `EventData` do hub de eventos é convertido em `TestEventData` para a execução da função.

### <a name="output-binding-example"></a>Exemplo de associação de saída

Você pode associar uma associação de saída ao valor de retorno usando `$return`. 

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

Se houver várias associações de saída, use o valor de retorno para apenas uma delas.

Para enviar vários valores de saída, use `OutputBinding<T>` definido no pacote `azure-functions-java-library`. 

```java
@FunctionName("QueueOutputPOJOList")
    public HttpResponseMessage QueueOutputPOJOList(@HttpTrigger(name = "req", methods = { HttpMethod.GET,
            HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "itemsOut", queueName = "test-output-java-pojo", connection = "AzureWebJobsStorage") OutputBinding<List<TestData>> itemsOut, 
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");
       
        String query = request.getQueryParameters().get("queueMessageId");
        String queueMessageId = request.getBody().orElse(query);
        itemsOut.setValue(new ArrayList<TestData>());
        if (queueMessageId != null) {
            TestData testData1 = new TestData();
            testData1.id = "msg1"+queueMessageId;
            TestData testData2 = new TestData();
            testData2.id = "msg2"+queueMessageId;

            itemsOut.getValue().add(testData1);
            itemsOut.getValue().add(testData2);

            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + queueMessageId).build();
        } else {
            return request.createResponseBuilder(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body("Did not find expected items in CosmosDB input list").build();
        }
    }

     public static class TestData {
        public String id;
    }
```

Você invoca essa função em um HttpRequest. Ele grava vários valores no armazenamento de fila.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage e HttpResponseMessage

 Eles são definidos em `azure-functions-java-library`. Eles são tipos auxiliares para trabalhar com as funções HttpTrigger.

| Tipo especializado      |       Visar        | Uso típico                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Acionador HTTP     | Obtém o método, os cabeçalhos ou as consultas |
| `HttpResponseMessage` | Associação de saída HTTP | Retorna o status diferente de 200   |

## <a name="metadata"></a>Metadados

Alguns gatilhos enviam [metadados de gatilho](/azure/azure-functions/functions-triggers-bindings) junto com os dados de entrada. Você pode usar a anotação `@BindingName` para associar aos metadados do gatilho.


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
No exemplo anterior, a `queryValue` está associada ao parâmetro de cadeia de caracteres de consulta `name` na URL de solicitação HTTP `http://{example.host}/api/metadata?name=test`. Aqui está outro exemplo, mostrando como associar a `Id` de metadados de gatilho de fila.

```java
 @FunctionName("QueueTriggerMetadata")
    public void QueueTriggerMetadata(
        @QueueTrigger(name = "message", queueName = "test-input-java-metadata", connection = "AzureWebJobsStorage") String message,@BindingName("Id") String metadataId,
        @QueueOutput(name = "output", queueName = "test-output-java-metadata", connection = "AzureWebJobsStorage") OutputBinding<TestData> output,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Queue trigger function processed a message: " + message + " with metadaId:" + metadataId );
        TestData testData = new TestData();
        testData.id = metadataId;
        output.setValue(testData);
    }
```

> [!NOTE]
> O nome fornecido na anotação precisa corresponder à propriedade de metadados.

## <a name="execution-context"></a>Contexto de execução

`ExecutionContext`, definido na `azure-functions-java-library`, contém métodos auxiliares para se comunicar com o tempo de execução do functions.

### <a name="logger"></a>Digita

Use `getLogger`, definido em `ExecutionContext`, para gravar logs do código de função.

Exemplo:

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>Exibir logs e rastreamento

Você pode usar o CLI do Azure para transmitir o log Java stdout e stderr, bem como outros logs de aplicativo. 

Veja como configurar seu aplicativo de funções para gravar o log do aplicativo usando o CLI do Azure:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Para transmitir a saída de log para seu aplicativo de funções usando o CLI do Azure, abra um novo prompt de comando, bash ou sessão de terminal e digite o seguinte comando:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
O comando [AZ webapp log tail](/cli/azure/webapp/log) tem opções para filtrar a saída usando a opção `--provider`. 

Para baixar os arquivos de log como um único arquivo ZIP usando o CLI do Azure, abra um novo prompt de comando, bash ou sessão de terminal e digite o seguinte comando:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Você deve ter habilitado o log do sistema de arquivos na portal do Azure ou na CLI do Azure antes de executar esse comando.

## <a name="environment-variables"></a>Variáveis de ambiente

Em funções, [as configurações de aplicativo](functions-app-settings.md), como cadeias de conexão de serviço, são expostas como variáveis de ambiente durante a execução. Você pode acessar essas configurações usando, `System.getenv("AzureWebJobsStorage")`.

O exemplo a seguir obtém a [configuração do aplicativo](functions-how-to-use-azure-function-app-settings.md#settings), com a chave chamada `myAppSetting`:

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o desenvolvimento em Java Azure Functions, consulte os seguintes recursos:

* [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
* [Referência para programadores das Funções do Azure](functions-reference.md)
* [Azure Functions gatilhos e associações](functions-triggers-bindings.md)
* Desenvolvimento local e depuração com [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md)e [Eclipse](functions-create-maven-eclipse.md)
* [Azure Functions Java de depuração remota com Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Plug-in do Maven para Azure Functions](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Simplifique a criação da função por meio do objetivo de `azure-functions:add` e prepare um diretório de preparo para a [implantação de arquivo zip](deployment-zip-push.md).

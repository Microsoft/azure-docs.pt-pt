---
title: Referência do desenvolvedor java para funções azure
description: Entenda como desenvolver funções com Java.
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: 4af2a860657f6066112146e1f88d81861d9430ea
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78358249"
---
# <a name="azure-functions-java-developer-guide"></a>Guia de desenvolvimento de Funções Azure Java

O tempo de funcionamento das Funções Azure suporta [o Java SE 8 LTS (zulu8.31.0.2-jre8.0.181-win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/). Este guia contém informações sobre as complexidades da escrita de Funções Azure com Java.

Como acontece com outros idiomas, uma App de Funções pode ter uma ou mais funções. Uma função Java é um método `public`, decorado com a anotação `@FunctionName`. Este método define a entrada para uma função Java, e deve ser único num determinado pacote. Uma aplicação de função escrita em Java pode ter várias classes com múltiplos métodos públicos anotados com `@FunctionName`.

Este artigo assume que já leu a referência do desenvolvedor de [Funções Azure](functions-reference.md). Também deve completar as Funções rapidamente para criar a sua primeira função, utilizando o [Visual Studio Code](functions-create-first-function-vs-code.md) ou [maven](functions-create-first-java-maven.md).

## <a name="programming-model"></a>Modelo de programação 

Os conceitos de [gatilhos e encadernações](functions-triggers-bindings.md) são fundamentais para as Funções Azure. Os gatilhos iniciam a execução do seu código. As encadernações dão-lhe uma forma de passar dados e devolver dados de uma função, sem ter de escrever código de acesso personalizado a dados.

## <a name="create-java-functions"></a>Criar funções java

Para facilitar a criação de funções java, existem ferramentas e arquétipos baseados em Maven que usam modelos java pré-definidos para ajudá-lo a criar projetos com um gatilho de função específica.    

### <a name="maven-based-tooling"></a>Ferramentas baseadas em Maven

Os seguintes ambientes de desenvolvimento têm ferramentas Azure Functions que permite criar projetos de função Java: 

+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

Os links de artigo acima mostram-lhe como criar as suas primeiras funções usando o seu IDE de eleição. 

### <a name="project-scaffolding"></a>Andaime do Projeto

Se preferir o desenvolvimento da linha de comando do Terminal, a forma mais simples de andaimes de função baseadas em Java é usar `Apache Maven` arquétipos. Existem atualmente dois arquétipos de funções para Maven:

+ **Arco java**: publicado sob o seguinte grupoId e artifactId [com.microsoft.azure:azure-functions-archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/):

    ```
    mvn archetype:generate \
        -DarchetypeGroupId=com.microsoft.azure \
        -DarchetypeArtifactId=azure-functions-archetype 
    ```

    Para começar a usar este arquétipo, veja o início rápido de [Java.](functions-create-first-java-maven.md) 

+ **Kotlin Archetype (Pré-visualização)** publicado sob o seguinte grupoId e artifactId [com.microsoft.azure:azure-functions-kotlin-archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-kotlin-archetype/):

    ```
    mvn archetype:generate \
        -DarchetypeGroupId=com.microsoft.azure \
        -DarchetypeArtifactId=azure-functions-kotlin-archetype
    ```

O código fonte destes arquétipos pode ser encontrado no [repositório Azure Maven Arquétipos GitHub](https://github.com/microsoft/azure-maven-archetypes).


## <a name="folder-structure"></a>Estrutura de pasta

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

_* O projeto Kotlin parece muito semelhante uma vez que ainda é Maven_

Pode utilizar um ficheiro [host.json](functions-host-json.md) partilhado para configurar a aplicação de função. Cada função tem o seu próprio ficheiro de código (.java) e ficheiro de configuração de ligação (função.json).

Pode colocar mais do que uma função num projeto. Evite colocar as suas funções em frascos separados. O `FunctionApp` no diretório-alvo é o que é implantado na sua aplicação de funções em Azure.

## <a name="triggers-and-annotations"></a>Gatilhos e anotações

 As funções são invocadas por um gatilho, como um pedido HTTP, um temporizador ou uma atualização aos dados. A sua função precisa de processar esse gatilho, e quaisquer outras inputs, para produzir uma ou mais saídas.

Utilize as anotações Java incluídas no pacote [com.microsoft.azure.functions.anotation.*](/java/api/com.microsoft.azure.functions.annotation) para ligar a entrada e as saídas aos seus métodos. Para mais informações, consulte os [médicos](/java/api/com.microsoft.azure.functions.annotation)de referência java .

> [!IMPORTANT] 
> Você deve configurar uma conta de Armazenamento Azure no seu [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) para executar armazenamento Azure Blob, armazenamento de fila Azure ou gatilhos de armazenamento de mesa Azure localmente.

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

Aqui está o `function.json` correspondente gerado pelo [azure-functions-maven-plugin:](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin)

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

## <a name="jdk-runtime-availability-and-support"></a>Disponibilidade e suporte de tempo de execução jDK 

Para o desenvolvimento local de aplicações de função Java, descarregue e use a [Blue Zulu Enterprise para Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java 8 JDKs da Azul [Systems.](https://www.azul.com/downloads/azure-only/zulu/) As Funções Azure utilizam o tempo de funcionamento do Blue Java 8 JDK quando implementa as suas aplicações de funções para a nuvem.

[O suporte azure](https://azure.microsoft.com/support/) para problemas com os JDKs e aplicações de função está disponível com um plano de [suporte qualificado.](https://azure.microsoft.com/support/plans/)

## <a name="customize-jvm"></a>Personalizar JVM

As funções permitem personalizar a máquina virtual Java (JVM) usada para executar as suas funções Java. As [seguintes opções JVM](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) são utilizadas por predefinição:

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

Pode fornecer argumentos adicionais numa definição de aplicação chamada `JAVA_OPTS`. Pode adicionar definições de aplicativos à sua aplicação de função implantada para o Azure no portal Azure ou no Azure CLI.

### <a name="azure-portal"></a>Portal do Azure

No [portal Azure,](https://portal.azure.com)utilize o [separador Definições](functions-how-to-use-azure-function-app-settings.md#settings) de Aplicação para adicionar a definição `JAVA_OPTS`.

### <a name="azure-cli"></a>CLI do Azure

Pode utilizar as definições de definição de definições de definição de definição de definições de definição de conjunto de [definições](/cli/azure/functionapp/config/appsettings) de conjunto de `JAVA_OPTS`, como no exemplo seguinte:

```azurecli-interactive
az functionapp config appsettings set --name <APP_NAME> \
--resource-group <RESOURCE_GROUP> \
--settings "JAVA_OPTS=-Djava.awt.headless=true"
```
Este exemplo permite o modo sem cabeça. Substitua `<APP_NAME>` pelo nome da sua aplicação de função e `<RESOURCE_GROUP>` pelo grupo de recursos.

> [!WARNING]  
> No [plano consumo,](functions-scale.md#consumption-plan)deve adicionar o cenário `WEBSITE_USE_PLACEHOLDER` com um valor de `0`.  
Esta definição aumenta os tempos de início frio para as funções java.

## <a name="third-party-libraries"></a>Bibliotecas de terceiros 

A Azure Functions apoia a utilização de bibliotecas de terceiros. Por predefinição, todas as dependências especificadas no seu projeto `pom.xml` ficheiro são automaticamente agregadas durante o objetivo [`mvn package`.](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) Para bibliotecas não especificadas como dependências no ficheiro `pom.xml`, coloque-as num diretório `lib` no diretório raiz da função. As dependências colocadas no diretório `lib` são adicionadas ao carregador de classe do sistema em tempo de funcionação.

A dependência `com.microsoft.azure.functions:azure-functions-java-library` é fornecida no caminho da classe por padrão, e não precisa de ser incluída no diretório `lib`. Além disso, [o azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) adiciona dependências listadas [aqui](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) para o caminho de classe.

## <a name="data-type-support"></a>Suporte de tipo de dados

Pode utilizar objetos Java antigos (POJOs), tipos definidos em `azure-functions-java-library`, ou tipos de dados primitivos, como String e Integer, para se ligar em ligações de entrada ou de saída.

### <a name="pojos"></a>POJOs

Para converter dados de entrada para POJO, o [azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) utiliza a biblioteca [gson.](https://github.com/google/gson) Os tipos POJO utilizados como inputs para funções devem ser `public`.

### <a name="binary-data"></a>Dados binários

Ligue as inputs ou saídas binárias a `byte[]`, definindo o campo `dataType` na sua função.json para `binary`:

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

Se espera valores nulos, utilize `Optional<T>`.

## <a name="bindings"></a>Enlaces

As encadernações de entrada e saída fornecem uma forma declarativa de se conectar em dados de dentro do seu código. Uma função pode ter várias encadernações de entrada e saída.

### <a name="input-binding-example"></a>Exemplo vinculativo de entrada

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

Invoca esta função com um pedido HTTP. 
- HTTP solicitar a carga útil é passado como um `String` para o argumento `inputReq`.
- Uma entrada é recuperada do armazenamento da mesa, e é passada como `TestInputData` ao argumento `inputData`.

Para receber um lote de entradas, pode ligar-se a `String[]`, `POJO[]`, `List<String>`ou `List<POJO>`.

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

Esta função é desencadeada sempre que há novos dados no centro de eventos configurado. Como o `cardinality` está definido para `MANY`, a função recebe um lote de mensagens do centro do evento. `EventData` do centro de eventos é convertido para `TestEventData` para a execução da função.

### <a name="output-binding-example"></a>Exemplo de encadernação de saída

Pode ligar uma ligação de saída ao valor de retorno utilizando `$return`. 

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

Se existirem várias encadernações de saída, utilize o valor de devolução para apenas uma delas.

Para enviar vários valores de saída, utilize `OutputBinding<T>` definidos no pacote `azure-functions-java-library`. 

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

Invoca esta função num HttpRequest. Escreve vários valores para o armazenamento de fila.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage e HttpResponseMessage

 Estes são definidos em `azure-functions-java-library`. São tipos de ajuda para trabalhar com funções httpTrigger.

| Tipo especializado      |       Destino        | Uso típico                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Acionador HTTP     | Obtém método, cabeçalhos ou consultas |
| `HttpResponseMessage` | Ligação de saída http | Estatuto de devoluções que não seja 200   |

## <a name="metadata"></a>Metadados

Poucos gatilhos enviam [metadados](/azure/azure-functions/functions-triggers-bindings) de gatilho juntamente com dados de entrada. Pode utilizar `@BindingName` de anotação para ligar ao gatilho dos metadados.


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
No exemplo anterior, o `queryValue` está ligado ao parâmetro de corda de consulta `name` no URL de pedido HTTP, `http://{example.host}/api/metadata?name=test`. Aqui está outro exemplo, mostrando como se ligar a `Id` a partir de metadados de gatilho de fila.

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
> O nome fornecido na anotação precisa de corresponder à propriedade dos metadados.

## <a name="execution-context"></a>Contexto de execução

`ExecutionContext`, definido no `azure-functions-java-library`, contém métodos de ajuda para comunicar com as funções de tempo de funcionamento.

### <a name="logger"></a>Madeireiro

Utilize `getLogger`, definido em `ExecutionContext`, para escrever registos a partir do código de funcionamento.

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

## <a name="view-logs-and-trace"></a>Ver troncos e vestígios

Pode utilizar o CLI Azure para transmitir stdout e stderr logging de Java, bem como outras explorações de registo de aplicações. 

Aqui está como configurar a sua aplicação de função para escrever registo de aplicações utilizando o Azure CLI:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Para transmitir a saída de registo para a sua aplicação de função utilizando o Azure CLI, abra um novo pedido de comando, sessão de Bash ou Terminal, e introduza o seguinte comando:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
O comando da cauda de [log az webapp](/cli/azure/webapp/log) tem opções para filtrar a saída utilizando a opção `--provider`. 

Para descarregar os ficheiros de registo como um único ficheiro ZIP utilizando o Azure CLI, abra um novo pedido de comando, sessão de Bash ou Terminal, e introduza o seguinte comando:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Deve ter ativado o registo do sistema de ficheiros no portal Azure ou no Azure CLI antes de executar este comando.

## <a name="environment-variables"></a>Variáveis de ambiente

Em Funções, [as definições](functions-app-settings.md)de aplicativos , tais como cordas de ligação ao serviço, são expostas como variáveis ambientais durante a execução. Pode aceder a estas definições utilizando, `System.getenv("AzureWebJobsStorage")`.

O exemplo seguinte obtém a definição da [aplicação,](functions-how-to-use-azure-function-app-settings.md#settings)com a chave denominada `myAppSetting`:

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o desenvolvimento das Funções Azure Java, consulte os seguintes recursos:

* [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
* [Referência para programadores das Funções do Azure](functions-reference.md)
* [Funções Azure desencadeiam e encadernam](functions-triggers-bindings.md)
* Desenvolvimento local e depuração com [Código de Estúdio Visual,](https://code.visualstudio.com/docs/java/java-azurefunctions) [IntelliJ](functions-create-maven-intellij.md)e [Eclipse](functions-create-maven-eclipse.md)
* [Funções remotas de Debug Java Azure com Código de Estúdio Visual](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Plugin Maven para funções azure](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Agilize a criação de funções através do objetivo `azure-functions:add` e prepare um diretório de encenação para a implementação de [ficheiros ZIP.](deployment-zip-push.md)

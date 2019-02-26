---
title: Referência para programadores de Java nas funções do Azure | Documentos da Microsoft
description: Aprenda a desenvolver as funções com Java.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: Azure funções, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor, java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: routlaw
ms.openlocfilehash: 96b22a6e44142fef8d47dc228d5491c31c65478f
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56823064"
---
# <a name="azure-functions-java-developer-guide"></a>Guia de programadores de Java de funções do Azure

O runtime das funções do Azure suporta [Java SE 8 LTS (zulu8.31.0.2-jre8.0.181-win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/).

## <a name="programming-model"></a>Modelo de programação

Os conceitos de [acionadores e enlaces](functions-triggers-bindings.md) são fundamentais para as funções do Azure. Acionadores de iniciar a execução do seu código. Enlaces dão-lhe uma forma de passar dados para e devolver dados de uma função, sem ter de escrever o código de acesso de dados personalizados.

Uma função deve ser um método sem monitoração de estado para processar a entrada e produzir a saída. A função não deve depender de quaisquer campos de instância da classe. Todos os métodos de função devem ser `public` e o método com anotação @FunctionName tem de ser exclusivo como o nome do método define a entrada para uma função.

## <a name="folder-structure"></a>estrutura de pastas

Esta é a estrutura de pastas de um projeto de Java de função do Azure:

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

Existe um partilhada [Host. JSON](functions-host-json.md) ficheiro que pode ser utilizado para configurar a aplicação de função. Cada função tem seu próprio arquivo de código (Java) e o ficheiro de configuração de vinculação (Function).

Pode colocar mais do que uma função num projeto. Evite colocar as suas funções em jars separados. FunctionApp no diretório de destino é o que é implementado para a aplicação de funções no Azure.

## <a name="triggers-and-annotations"></a>Acionadores e as anotações

 As funções do Azure são chamadas por um acionador, como um pedido HTTP, um temporizador ou uma atualização de dados. A função precisa processar esse acionador e todas as outras entradas para produzir uma ou mais saídas.

Utilize as anotações de Java incluídas nos [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) pacote para o enlace de entrada e saídas para seus métodos. Para obter mais informações, consulte [documentos de referência de Java](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> Tem de configurar uma conta de armazenamento do Azure no seu [Settings](/azure/azure-functions/functions-run-local#local-settings-file) para executar os acionadores de tabela, fila ou Blob de armazenamento do Azure localmente.

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

Aqui está o correspondente gerado `function.json` pelo [azure-funções--Plug-in maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin):

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

## <a name="jdk-runtime-availability-and-support"></a>Disponibilidade de tempo de execução do JDK e suporte 

Transferir e utilizar o [Azul Zulu Enterprise para o Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java 8 JDKs partir [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/) para o local de desenvolvimento de aplicações de funções do Java. As funções do Azure utiliza o tempo de execução do Azul Java 8 JDK quando implementar as suas aplicações de função para a cloud.

[Suporte do Azure](https://azure.microsoft.com/en-us/support/) problemas com o JDKs e a função de aplicações está disponível com um [plano de suporte qualificado](https://azure.microsoft.com/support/plans/).

## <a name="third-party-libraries"></a>Bibliotecas de terceiros 

As funções do Azure suporta a utilização de bibliotecas de terceiros. Por predefinição, todas as dependências especificado no seu projeto `pom.xml` ficheiro serão automaticamente agrupado durante o [ `mvn package` ](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) objetivo. Para bibliotecas não especificadas como dependências a `pom.xml` de ficheiros, colocá-los num `lib` diretório no diretório de raiz da função. Dependências colocadas o `lib` directory será adicionado ao carregador de classe do sistema no tempo de execução.

O `com.microsoft.azure.functions:azure-functions-java-library` dependência é fornecida no classpath por predefinição e não precisa de ser incluídos no `lib` diretório. Além disso, as dependências listada [aqui](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) são adicionados ao classpath por [do azure-funções-java-trabalho](https://github.com/Azure/azure-functions-java-worker).

## <a name="data-type-support"></a>Tipo de dados de suporte

Pode usar objetos de Java (POJOs) antigos sem formatação, tipos definidos em `azure-functions-java-library` ou tipos de dados primitivos como cadeia de caracteres, número inteiro para vincular a enlaces de entrada/saída.

### <a name="plain-old-java-objects-pojos"></a>Objetos de Java (POJOs) antigos sem formatação

Para a conversão de dados de entrada para POJO, [azure-funções-java-worker](https://github.com/Azure/azure-functions-java-worker) utiliza [gson](https://github.com/google/gson) biblioteca. Tipos POJO utilizados como entradas para as funções devem ser `public`.

### <a name="binary-data"></a>Dados binários

Vincular o binárias entradas ou saídas para `byte[]` ao definir o `dataType` campo no seu Function para `binary`:

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

Utilize `Optional<T>` para se espera valores nulos

## <a name="bindings"></a>Enlaces

Enlaces de entrada e saída proporcionam uma forma declarativa para ligar a dados a partir de seu código. Uma função pode ter várias entradas e ligações de saída.

### <a name="example-input-binding"></a>Exemplo enlace de entrada

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

Esta função é invocada por uma solicitação HTTP. 
- Payload de pedido HTTP é passado como um `String` para o argumento `inputReq`
- Uma entrada é obtida a partir do armazenamento de tabelas do Azure e é passada como `TestInputData` para o argumento `inputData`.

Para receber um lote de entradas, é possível vincular à `String[]`, `POJO[]`, `List<String>` ou `List<POJO>`.

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

Esta função é acionada sempre que houver dados novos no hub de eventos configurados. Como o `cardinality` está definido como `MANY`, função recebe um lote de mensagens do hub de eventos. EventData do hub de eventos é convertido em `TestEventData` para a execução de função.

### <a name="example-output-binding"></a>Exemplo enlace de saída

É possível vincular um enlace de saída para o valor de retorno com `$return` 

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

Se existirem vários enlaces de saída, utilize o valor de retorno para apenas um deles.

Para enviar vários valores de saída, utilize `OutputBinding<T>` definidos no `azure-functions-java-library` pacote. 

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

Acima da função é invocado num objeto HttpRequest e escreve vários valores para a fila do Azure

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage e HttpResponseMessage

 Tipos de HttpRequestMessage/HttpResponseMessage são definidos no `azure-functions-java-library` são tipos de programa auxiliar para trabalhar com funções de HttpTrigger

| Tipo especializado      |       Destino        | Utilização normal                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Acionador HTTP     | Obter o método, cabeçalhos ou consultas |
| `HttpResponseMessage` | Enlace de saída de HTTP | Status de retorno diferente de 200   |

## <a name="metadata"></a>Metadados

Enviar acionadores alguns [acionar metadados](/azure/azure-functions/functions-triggers-bindings) juntamente com dados de entrada. Pode usar a anotação `@BindingName` para vincular a acionar metadados


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
No exemplo acima, o `queryValue` está vinculado ao parâmetro de cadeia de caracteres de consulta `name` URL do pedido de Http `http://{example.host}/api/metadata?name=test`. Segue-se outro exemplo, a ligação a `Id` de metadados de Acionador de fila

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
> O nome fornecido a anotação precisa coincidir com a propriedade de metadados

## <a name="execution-context"></a>Contexto de execução

`ExecutionContext` definido no `azure-functions-java-library` contém métodos auxiliares para comunicar com o runtime das funções.

### <a name="logger"></a>Logger

Uso `getLogger` definidos no `ExecutionContext` para escrever os registos de código de função.

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

## <a name="view-logs-and-trace"></a>Ver registos e de rastreio

Pode utilizar a CLI do Azure para o stream Java stdout e stderr Registro em log, bem como outros logs do aplicativo. 

Configure a sua aplicação de função para escrever o registo de aplicações com a CLI do Azure:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Para transmitir o registo de saída para a sua aplicação de função com a CLI do Azure, abra uma nova linha de comandos, o Bash ou a sessão de Terminal e introduza o seguinte comando:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
O [cauda do az webapp log](/cli/azure/webapp/log) comando tem opções para filtrar a saída com o `--provider` opção. 

Para transferir os ficheiros de registo como um ficheiro ZIP único utilizando a CLI do Azure, abra uma nova linha de comandos, o Bash ou a sessão de Terminal e introduza o seguinte comando:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Tem de ter ativado o sistema de ficheiros de registo no Portal do Azure ou na CLI do Azure antes de executar este comando.

## <a name="environment-variables"></a>Variáveis de ambiente

Em funções, [as definições da aplicação](https://docs.microsoft.com/azure/azure-functions/functions-app-settings), por exemplo, a ligação de serviço são expostos cadeias de caracteres, como variáveis de ambiente durante a execução. Pode acessar essas configurações usando, `System.getenv("AzureWebJobsStorage")`

Exemplo:

Adicione [AppSetting](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) com testAppSettingValue testAppSetting e o valor de nome

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("testAppSetting "+ System.getenv("testAppSettingValue"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o desenvolvimento de Java de função do Azure, consulte os seguintes recursos:

* [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
* [Referência para programadores das Funções do Azure](functions-reference.md)
* [Acionadores de funções do Azure e enlaces](functions-triggers-bindings.md)
- Desenvolvimento local e de depuração com [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md), e [Eclipse](functions-create-maven-eclipse.md). 
* [Depuração remota do Java Azure funciona com o Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Plug-in do maven para as funções do Azure](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) -Simplifique a criação da função através do `azure-functions:add` objetivo e preparar um diretório de transição para [implementação de ficheiros ZIP](deployment-zip-push.md).

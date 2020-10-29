---
title: Referência do desenvolvedor java para funções Azure
description: Entenda como desenvolver funções com Java.
ms.topic: conceptual
ms.date: 09/14/2018
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: b25e548fe56c22458fe625f617fb076be13525cd
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927520"
---
# <a name="azure-functions-java-developer-guide"></a>Guia de desenvolvedores de Java funções Azure Functions

Este guia contém informações detalhadas para ajudá-lo a desenvolver funções Azure utilizando Java.

Como desenvolvedor de Java, se é novo em Azure Functions, por favor considere primeiro a leitura de um dos seguintes artigos:

| Introdução | Conceitos| 
| -- | -- |  
| <ul><li>[Função java usando Código de Estúdio Visual](./functions-create-first-function-vs-code.md?pivots=programming-language-java)</li><li>[Função Java/Maven com terminal/comando](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-java)</li><li>[Função java usando Gradle](functions-create-first-java-gradle.md)</li><li>[Função java usando Eclipse](functions-create-maven-eclipse.md)</li><li>[Função java usando IntelliJ IDEA](functions-create-maven-intellij.md)</li></ul> | <ul><li>[Guia para programadores](functions-reference.md)</li><li>[Opções de alojamento](functions-scale.md)</li><li>[&nbsp;Considerações de desempenho](functions-best-practices.md)</li></ul> |

## <a name="java-function-basics"></a>Básicos da função java

Uma função Java é um `public` método, decorado com a anotação. `@FunctionName` Este método define a entrada para uma função Java, e deve ser único num determinado pacote. O pacote pode ter várias classes com múltiplos métodos públicos anotados com `@FunctionName` . Um único pacote é implantado numa aplicação de função em Azure. Ao correr em Azure, a aplicação de função fornece o contexto de implementação, execução e gestão para as suas funções java individuais.

## <a name="programming-model"></a>Modelo de programação 

Os conceitos de [gatilhos e encadernações](functions-triggers-bindings.md) são fundamentais para as Funções Azure. Os gatilhos iniciam a execução do seu código. As ligações dão-lhe uma forma de transmitir dados e devolver dados de uma função, sem ter de escrever código de acesso a dados personalizado.

## <a name="create-java-functions"></a>Criar funções java

Para facilitar a criação de funções java, existem ferramentas e arquétipos baseados em Maven que usam modelos de Java predefinidos para ajudá-lo a criar projetos com um gatilho de função específica.    

### <a name="maven-based-tooling"></a>Ferramentas baseadas em Maven

Os seguintes ambientes de desenvolvimento têm ferramentas Azure Functions que lhe permitem criar projetos de função Java: 

+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

Os links de artigo acima mostram-lhe como criar as suas primeiras funções utilizando o seu IDE de eleição. 

### <a name="project-scaffolding"></a>Andaimes do projeto

Se preferir o desenvolvimento da linha de comando a partir do Terminal, a forma mais simples de andaimes projetos de função baseados em Java é usar `Apache Maven` arquétipos. O arquétipo Java Maven para Funções Azure é publicado sob o seguinte _grupoId_ : _artifactId_ : [com.microsoft.azure:azure-functions-archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/). 

O seguinte comando gera um novo projeto de função Java utilizando este arquétipo:

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

Para começar a usar este arquétipo, consulte o [arranque rápido de Java.](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-java) 

## <a name="folder-structure"></a>Estrutura de pasta

Aqui está a estrutura de pasta de um projeto Azure Functions Java:

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

Pode utilizar umahost.jspartilhada [ no](functions-host-json.md) ficheiro para configurar a aplicação de função. Cada função tem o seu próprio ficheiro de código (.java) e ficheiro de configuração de ligação (function.js).

Pode colocar mais do que uma função num projeto. Evite colocar as suas funções em frascos separados. O `FunctionApp` directório-alvo é o que é implementado na sua aplicação de função em Azure.

## <a name="triggers-and-annotations"></a>Gatilhos e anotações

 As funções são invocadas por um gatilho, como um pedido HTTP, um temporizador ou uma atualização aos dados. A sua função precisa de processar o gatilho, e quaisquer outras entradas, para produzir uma ou mais saídas.

Utilize as anotações java incluídas no pacote [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) para ligar entradas e saídas aos seus métodos. Para mais informações, consulte os [documentos de referência de Java.](/java/api/com.microsoft.azure.functions.annotation)

> [!IMPORTANT] 
> Tem de configurar uma conta de Armazenamento Azure no seu [local.settings.js](./functions-run-local.md#local-settings-file) para executar o armazenamento Azure Blob, o armazenamento da fila Azure ou os gatilhos de armazenamento da Mesa Azure localmente.

Exemplo:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {HttpMethod.POST},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

Aqui está o correspondente gerado `function.json` pelo [azure-funs-maven-plugin:](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin)

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

## <a name="java-versions"></a>Versões java

A versão de Java utilizada ao criar a aplicação de função em que as funções funciona no Azure é especificada no ficheiro pom.xml. O arquétipo Maven gera atualmente uma pom.xml para Java 8, que podes alterar antes de publicares. A versão Java em pom.xml deve coincidir com a versão na qual desenvolveu e testou localmente a sua aplicação. 

### <a name="supported-versions"></a>Versões suportadas

A tabela a seguir mostra as versões Java suportadas atuais para cada versão principal do tempo de execução das Funções, pelo sistema operativo:

| Versão de funções | Versões java (Windows) | Versões java (Linux) |
| ----- | ----- | --- |
| 3.x | 11 <br/>8 | 11 <br/>8 |
| 2.x | 8 | n/a |

A menos que especifique uma versão Java para a sua implantação, o arquétipo Maven desresímos de Java 8 durante a implementação para Azure.

### <a name="specify-the-deployment-version"></a>Especificar a versão de implantação

Pode controlar a versão de Java visada pelo arquétipo Maven utilizando o `-DjavaVersion` parâmetro. O valor deste parâmetro pode ser `8` `11` ou. 

O arquétipo maven gera uma pom.xml que visa a versão java especificada. Os seguintes elementos em pom.xml indicam a versão Java a utilizar:

| Elemento |  Valor de Java 8 | Valor de Java 11 | Description |
| ---- | ---- | ---- | --- |
| **`Java.version`** | 1.8 | 11 | Versão de Java usada pelo maven-compilador-plugin. |
| **`JavaVersion`** | 8 | 11 | Versão Java hospedada pela aplicação de função em Azure. |

Os exemplos a seguir mostram as definições de Java 8 nas secções relevantes do ficheiro pom.xml:

#### `Java.version`
:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="12-19" highlight="14":::

#### `JavaVersion`
:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="77-85" highlight="80":::

> [!IMPORTANT]
> Deve ter a variável ambiente JAVA_HOME corretamente definida no diretório JDK que é usado durante a compilação de códigos usando o Maven. Certifique-se de que a versão do JDK é pelo menos tão alta quanto a `Java.version` regulação. 

### <a name="specify-the-deployment-os"></a>Especificar o SISTEMA de implementação

A Maven também permite especificar o sistema operativo em que a sua aplicação de função funciona em Azure. Utilize o `os` elemento para escolher o sistema operativo. 

| Elemento |  Windows | Linux | Docker |
| ---- | ---- | ---- | --- |
| **`os`** | windows | linux | estivador |

O exemplo a seguir mostra a definição do sistema operativo na `runtime` secção do ficheiro pom.xml:

:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="77-85" highlight="79":::
 
## <a name="jdk-runtime-availability-and-support"></a>Disponibilidade e suporte de tempo de execução JDK 

Para o desenvolvimento local de aplicações de função Java, baixe e utilize a [Empresa Azul Zulu apropriada para Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java JDKs da [Azul Systems.](https://www.azul.com/downloads/azure-only/zulu/) O Azure Functions utiliza um tempo de execução Azul Java JDK quando implementa a sua aplicação de função na nuvem.

[O suporte do Azure](https://azure.microsoft.com/support/) para problemas com os JDKs e aplicações de função está disponível com um [plano de suporte qualificado.](https://azure.microsoft.com/support/plans/)

## <a name="customize-jvm"></a>Personalize jVM

As funções permitem personalizar a máquina virtual Java (JVM) utilizada para executar as suas funções java. As [seguintes opções JVM](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) são utilizadas por padrão:

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

Pode fornecer argumentos adicionais numa definição de aplicação chamada `JAVA_OPTS` . Pode adicionar definições de aplicações à sua aplicação de função implantada no Azure no portal Azure ou no Azure CLI.

> [!IMPORTANT]  
> No plano de Consumo, deve ainda adicionar a definição de WEBSITE_USE_PLACEHOLDER com um valor de 0 para a personalização funcionar. Esta definição aumenta os tempos de arranque frio para as funções de Java.

### <a name="azure-portal"></a>Portal do Azure

No [portal Azure,](https://portal.azure.com)utilize o [separador Definições de Aplicação](functions-how-to-use-azure-function-app-settings.md#settings) para adicionar a `JAVA_OPTS` definição.

### <a name="azure-cli"></a>CLI do Azure

Pode utilizar o comando de configuração de configuração de apps do [az functionapp para](/cli/azure/functionapp/config/appsettings) definir `JAVA_OPTS` , como no exemplo seguinte:

#### <a name="consumption-plan"></a>[Plano de consumo](#tab/consumption)
```azurecli-interactive
az functionapp config appsettings set \
--settings "JAVA_OPTS=-Djava.awt.headless=true" \
"WEBSITE_USE_PLACEHOLDER=0" \
--name <APP_NAME> --resource-group <RESOURCE_GROUP>
```
#### <a name="dedicated-plan--premium-plan"></a>[Plano dedicado / Plano Premium](#tab/dedicated+premium)
```azurecli-interactive
az functionapp config appsettings set \
--settings "JAVA_OPTS=-Djava.awt.headless=true" \
--name <APP_NAME> --resource-group <RESOURCE_GROUP>
```
---

Este exemplo permite o modo sem cabeça. `<APP_NAME>`Substitua-o pelo nome da sua aplicação de função e `<RESOURCE_GROUP>` pelo grupo de recursos. 

## <a name="third-party-libraries"></a>Bibliotecas de terceiros 

A Azure Functions suporta a utilização de bibliotecas de terceiros. Por predefinição, todas as dependências especificadas no seu ficheiro do projeto `pom.xml` são automaticamente agregadas durante o [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) objetivo. Para bibliotecas não especificadas como dependências no `pom.xml` ficheiro, coloque-as num `lib` diretório no diretório de raiz da função. As dependências colocadas no `lib` diretório são adicionadas ao carregador de classe do sistema em tempo de funcionação.

A `com.microsoft.azure.functions:azure-functions-java-library` dependência é fornecida no apêncil por defeito, e não precisa de ser incluída no `lib` diretório. Além disso, [azure-funs-java-worker](https://github.com/Azure/azure-functions-java-worker) adiciona dependências listadas [aqui](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) para o caminho de classe.

## <a name="data-type-support"></a>Suporte ao tipo de dados

Pode utilizar objetos Java simples (POJOs), tipos definidos em `azure-functions-java-library` , ou tipos de dados primitivos, como String e Inteiro, para se ligar às ligações de entrada ou saída.

### <a name="pojos"></a>POJOs

Para converter dados de entrada para POJO, [o azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) utiliza a biblioteca [gson.](https://github.com/google/gson) Os tipos de POJO utilizados como entradas para funções devem ser `public` .

### <a name="binary-data"></a>Dados binários

Ligue entradas ou saídas binárias `byte[]` para, definindo o `dataType` campo no seu function.jspara `binary` :

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

Se espera valores nulos, use `Optional<T>` .

## <a name="bindings"></a>Enlaces

As ligações de entrada e saída fornecem uma forma declarativa de se conectar aos dados a partir do seu código. Uma função pode ter múltiplas ligações de entrada e saída.

### <a name="input-binding-example"></a>Exemplo de ligação de entrada

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { HttpMethod.PUT }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData,
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData
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
- A carga útil do pedido HTTP é passada como um `String` para o argumento `inputReq` .
- Uma entrada é recuperada do armazenamento da mesa, e é passada quanto `TestInputData` ao argumento `inputData` .

Para receber um lote de entradas, pode ligar-se a `String[]` `POJO[]` , ou `List<String>` `List<POJO>` .

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

Esta função é ativada sempre que há novos dados no centro de eventos configurado. Como `cardinality` está definido para `MANY` , a função recebe um lote de mensagens do centro de eventos. `EventData` do centro de eventos é convertido `TestEventData` para a execução da função.

### <a name="output-binding-example"></a>Exemplo vinculativo de saída

Pode ligar uma ligação de saída ao valor de retorno utilizando `$return` . 

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

Se existirem múltiplas ligações de saída, utilize o valor de retorno para apenas um deles.

Para enviar vários valores de saída, utilize `OutputBinding<T>` definido na `azure-functions-java-library` embalagem. 

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

Invoca esta função num HttpRequest. Escreve vários valores para o armazenamento da fila.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage e HttpResponseMessage

 Estes são definidos em `azure-functions-java-library` . São tipos de ajuda para trabalhar com funções HttpTrigger.

| Tipo especializado      |       Destino        | Uso típico                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Acionador HTTP     | Obtém método, cabeçalhos ou consultas |
| `HttpResponseMessage` | Vinculação de saída HTTP | Estatuto de devolução que não seja 200   |

## <a name="metadata"></a>Metadados

Poucos gatilhos enviam [metadados de gatilho](./functions-triggers-bindings.md) juntamente com dados de entrada. Pode utilizar a anotação `@BindingName` para se ligar aos metadados.


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
No exemplo anterior, o `queryValue` está ligado ao parâmetro de cadeia de consulta no URL de pedido `name` HTTP, `http://{example.host}/api/metadata?name=test` . Aqui está outro exemplo, mostrando como ligar-se a `Id` partir de metadados de gatilho de fila.

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

`ExecutionContext`, definido no `azure-functions-java-library` , contém métodos auxiliares para comunicar com as funções tempo de execução. Para mais informações, consulte o [artigo de referência do Texto de Execução](/java/api/com.microsoft.azure.functions.executioncontext).

### <a name="logger"></a>Madeir

Utilizar `getLogger` , definido em , para escrever `ExecutionContext` registos a partir do código de função.

Exemplo:

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>Ver registos e vestígios

Pode utilizar o CLI Azure para transmitir a sessão de stdout de Java e o registo de registos stderr, bem como outros registos de aplicações. 

Aqui está como configurar a sua aplicação de função para escrever o registo de aplicações utilizando o Azure CLI:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Para transmitir a saída de registo para a sua aplicação de função utilizando o CLI Azure, abra uma nova sessão de comando, Bash ou Terminal e introduza o seguinte comando:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
O comando [de cauda de log webapp az](/cli/azure/webapp/log) tem opções para filtrar a saída utilizando a `--provider` opção. 

Para descarregar os ficheiros de registo como um único ficheiro ZIP utilizando o CLI Azure, abra uma nova sessão de comando, Bash ou Terminal e introduza o seguinte comando:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Deve ter ativado o registo do sistema de ficheiros no portal Azure ou no CLI Azure antes de executar este comando.

## <a name="environment-variables"></a>Variáveis de ambiente

Em Funções, [as configurações da aplicação](functions-app-settings.md), como as cadeias de ligação de serviço, são expostas como variáveis ambientais durante a execução. Pode aceder a estas definições utilizando, `System.getenv("AzureWebJobsStorage")` .

O exemplo a seguir obtém a [definição de aplicação,](functions-how-to-use-azure-function-app-settings.md#settings)com a chave denominada `myAppSetting` :

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

> [!NOTE]
> O valor do appSetting FUNCTIONS_EXTENSION_VERSION deve ser ~2 ou ~3 para uma experiência de arranque a frio otimizada.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o desenvolvimento de Java das Funções Azure, consulte os seguintes recursos:

* [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
* [Referência para programadores das Funções do Azure](functions-reference.md)
* [Funções Azure dispara e encaderna](functions-triggers-bindings.md)
* Desenvolvimento local e depurar com [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md)e [Eclipse](functions-create-maven-eclipse.md)
* [Funções de Debug Java remoto usando Código de Estúdio Visual](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Plugin maven para funções Azure](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Dinamize a criação de funções através do `azure-functions:add` objetivo e prepare um diretório de encenação para a [implementação de ficheiros ZIP](deployment-zip-push.md).

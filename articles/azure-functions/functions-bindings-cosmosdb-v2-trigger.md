---
title: Gatilho DB Azure Cosmos para funções 2.x
description: Aprenda a utilizar o gatilho Azure Cosmos DB em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: c006aa8c46864b78ae46aa9c351605cca1d1e425
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77606584"
---
# <a name="azure-cosmos-db-trigger-for-azure-functions-2x"></a>Gatilho DB Azure Cosmos para funções Azure 2.x

O Gatilho DB Azure Cosmos utiliza o [Azure Cosmos DB Change Feed](../cosmos-db/change-feed.md) para ouvir inserções e atualizações em divisórias. O feed de alterações publica inserções e atualizações, eliminações não.

Para obter informações sobre os detalhes da configuração e configuração, consulte a [visão geral](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

O exemplo que se segue mostra uma [ C# função](functions-dotnet-class-library.md) que é invocada quando há inserções ou atualizações na base de dados e recolha especificadas.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents,
            ILogger log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.LogInformation($"Documents modified: {documents.Count}");
                log.LogInformation($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

# <a name="c-script"></a>[C#Roteiro](#tab/csharp-script)

O exemplo seguinte mostra uma ligação do gatilho Cosmos DB num ficheiro *function.json* e uma [ C# função de script](functions-reference-csharp.md) que utiliza a ligação. A função escreve mensagens de registo quando os registos de Cosmos DB são adicionados ou modificados.

Aqui estão os dados vinculativos no ficheiro *função.json:*

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Aqui está o código de script do c#:

```cs
    #r "Microsoft.Azure.DocumentDB.Core"

    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using Microsoft.Extensions.Logging;

    public static void Run(IReadOnlyList<Document> documents, ILogger log)
    {
      log.LogInformation("Documents modified " + documents.Count);
      log.LogInformation("First document Id " + documents[0].Id);
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo seguinte mostra uma ligação do gatilho Cosmos DB num ficheiro *function.json* e uma [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função escreve mensagens de registo quando os registos de Cosmos DB são adicionados ou modificados.

Aqui estão os dados vinculativos no ficheiro *função.json:*

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Eis o código JavaScript:

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

# <a name="python"></a>[python](#tab/python)

O exemplo seguinte mostra uma ligação do gatilho Cosmos DB num ficheiro *function.json* e uma [função Python](functions-reference-python.md) que usa a ligação. A função escreve as mensagens de registo quando os registos de Cosmos DB são modificados.

Aqui estão os dados vinculativos no ficheiro *função.json:*

```json
{
    "name": "documents",
    "type": "cosmosDBTrigger",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Aqui está o código Python:

```python
    import logging
    import azure.functions as func


    def main(documents: func.DocumentList) -> str:
        if documents:
            logging.info('First document Id modified: %s', documents[0]['id'])
```

# <a name="java"></a>[Java](#tab/java)

Esta função é invocada quando há inserções ou atualizações na base de dados e recolha especificadas.

```java
    @FunctionName("cosmosDBMonitor")
    public void cosmosDbProcessor(
        @CosmosDBTrigger(name = "items",
            databaseName = "ToDoList",
            collectionName = "Items",
            leaseCollectionName = "leases",
            createLeaseCollectionIfNotExists = true,
            connectionStringSetting = "AzureCosmosDBConnection") String[] items,
            final ExecutionContext context ) {
                context.getLogger().info(items.length + "item(s) is/are changed.");
            }
```


Na biblioteca de [tempo de funcionamento](/java/api/overview/azure/functions/runtime)de Java, use a anotação `@CosmosDBTrigger` em parâmetros cujo valor viria de Cosmos DB.  Esta anotação pode ser usada com tipos nativos de Java, POJOs ou valores nuníveis usando `Optional<T>`.

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Nas [ C# bibliotecas de classes,](functions-dotnet-class-library.md)use o atributo [CosmosDBTrigger.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs)

Construtor do atributo pega o nome de base de dados e o nome da coleção. Para obter informações sobre essas definições e outras propriedades que possa configurar, consulte [O Gatilho - configuração](#configuration). Aqui está um exemplo de atributo `CosmosDBTrigger` numa assinatura de método:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        ILogger log)
    {
        ...
    }
```

Para um exemplo completo, consulte [O Gatilho](#example).

# <a name="c-script"></a>[C#Roteiro](#tab/csharp-script)

Os atributos C# não são suportados pelo Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

A partir da biblioteca de tempo de funcionamento das [funções java,](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)use o `@CosmosDBInput` anotação em parâmetros que lêem dados da Cosmos DB.

---

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no atributo `CosmosDBTrigger`.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | deve ser definido para `cosmosDBTrigger`. |
|**direção** | n/d | deve ser definido para `in`. Este parâmetro é definido automaticamente quando criar o acionador no portal do Azure. |
|**nome** | n/d | O nome da variável no código de função que representa a lista de documentos com as alterações. |
|**conexãoStringSetting**|**Definição de cordas de ligação** | O nome de uma definição de aplicação que contém a cadeia de ligação utilizada para ligar à conta do Azure Cosmos DB a ser monitorizada. |
|**nome da base de dados**|**Nome da base de dados**  | O nome da base de dados do Azure Cosmos DB com a coleção a ser monitorizada. |
|**coleçãoNome** |**Nome de coleção** | O nome da coleção a ser monitorizado. |
|**arrendamentoConnectionStringSetting** | **ArrendamentoConexStringSetting** | (Opcional) O nome de uma configuração de aplicação que contém a cadeia de ligação à conta Azure Cosmos DB que detém a coleção de arrendamento. Quando não definido, o valor `connectionStringSetting` é utilizado. Este parâmetro é definido automaticamente quando a associação é criada no portal. A cadeia de ligação para a coleção de concessões tem de ter permissões de escrita.|
|**arrendamentoNome basede-dados** |**Nome de leasing Database** | (Opcional) O nome da base de dados que contém a coleção utilizada para armazenar as concessões. Quando não está definido, é utilizado o valor da definição `databaseName`. Este parâmetro é definido automaticamente quando a associação é criada no portal. |
|**leaseCollectionName** | **LeaseCollectionName** | (Opcional) O nome da coleção utilizada para armazenar as concessões. Quando não definido, o valor `leases` é utilizado. |
|**criarLeaseCollectionIfNotExists** | **CreateLeaseCollectionifNotexists** | (Opcional) Quando definida para `true`, a coleção de arrendamentos é automaticamente criada quando já não existe. O valor predefinido é `false`. |
|**arrendamentosCollectionThroughputput**| **LeasesCollectionThroughput**| (Opcional) Define o número de Unidades de Pedido para atribuir quando a coleção de arrendamentos é criada. Esta definição só é utilizada quando `createLeaseCollectionIfNotExists` está definida para `true`. Este parâmetro é definido automaticamente quando a associação é criada com o portal.
|**arrendamentoCollectionPrefix**| **LeaseCollectionPrefix**| (Opcional) Quando definido, o valor é adicionado como prefixo para os arrendamentos criados na coleção lease para esta Função. A utilização de um prefixo permite que duas funções Azure separadas partilhem a mesma coleção de Arrendamento utilizando prefixos diferentes.
|**feedPollDelay**| **FeedPollDelay**| (Opcional) O tempo (em milissegundos) para o atraso entre a sondagem de uma partição para novas alterações no feed, depois de todas as alterações atuais serem drenadas. O padrão é de 5.000 milissegundos, ou 5 segundos.
|**arrendamentoAdquirirInterval**| **Intervalo de Arrendamento**| (Opcional) Quando definida, ela define, em milissegundos, o intervalo de disparar uma tarefa de computação se as partições são distribuídas uniformemente entre instâncias de host conhecidos. A predefinição é 13000 (13 segundos).
|**intervalo de validade**| **Intervalo de locação prazo**| (Opcional) Quando definida, ela define, em milissegundos, o intervalo para o qual a concessão é criada numa concessão que representa uma partição. Se a concessão não for renovada dentro deste intervalo, fará com que expire e propriedade da partição irá mudar para outra instância. A predefinição é 60000 (60 segundos).
|**arrendamentoRenewInterval**| **Intervalo de Arrendamento Renovado**| (Opcional) Quando definida, ela define, em milissegundos, o intervalo de renovação para todas as concessões para partições atualmente mantido por uma instância. A predefinição é 17000 (17 segundos).
|**checkpointFrequência**| **CheckpointFrequência**| (Opcional) Quando definida, ela define, em milissegundos, o intervalo entre pontos de verificação de concessão. O predefinido é sempre posterior a cada chamada de função.
|**maxItemsPerInvocação**| **MaxItemsPerInvocação**| (Opcional) Quando definido, esta propriedade define o número máximo de itens recebidos por chamada de Função. Se as operações na recolha monitorizada forem realizadas através de procedimentos armazenados, o âmbito de [transação](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) é preservado ao ler itens do Change Feed. Como resultado, o número de itens recebidos pode ser superior ao valor especificado, de modo a que os itens alterados pela mesma transação sejam devolvidos como parte de um lote atómico.
|**inícioFromBeginning**| **Início do início**| (Opcional) Esta opção diz ao Trigger para ler alterações desde o início do histórico de mudança da coleção em vez de começar no momento atual. A leitura desde o início só funciona na primeira vez que o Gatilho começa, uma vez que nas corridas subsequentes, os postos de controlo já estão armazenados. Definir esta opção para `true` quando há arrendamentos já criados não tem efeito.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

O gatilho requer uma segunda coleção que utiliza para armazenar _arrendamentos_ sobre as divisórias. A coleção a ser monitorizado e a coleção que contém as concessões tem de estar disponíveis para o acionador trabalhar.

>[!IMPORTANT]
> Se várias funções estiverem configuradas para utilizar um gatilho Cosmos DB para a mesma recolha, cada uma das funções deve utilizar uma coleção de arrendamento dedicada ou especificar um `LeaseCollectionPrefix` diferente para cada função. Caso contrário, será acionada apenas uma das funções. Para obter informações sobre o prefixo, consulte a [secção Configuração](#configuration).

O acionador não indica que se um documento foi atualizado ou inserido, ele fornece apenas o próprio documento. Se precisar manipular atualizações e inserções de forma diferente, poderia fazê-lo através da implementação timestamp campos para inserção ou atualização.

## <a name="next-steps"></a>Passos seguintes

- [Leia um documento Azure Cosmos DB (encadernação de entrada)](./functions-bindings-cosmosdb-v2-input.md)
- [Guardar alterações num documento Azure Cosmos DB (Encadernação de saída)](./functions-bindings-cosmosdb-v2-output.md)
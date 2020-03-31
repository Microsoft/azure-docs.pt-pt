---
title: Gatilho DB Azure Cosmos para funções 2.x
description: Aprenda a utilizar o gatilho Azure Cosmos DB em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: de8ad39ef731af3dc272d700eeee346acda64b53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277573"
---
# <a name="azure-cosmos-db-trigger-for-azure-functions-2x"></a>Gatilho DB Azure Cosmos para funções Azure 2.x

O Gatilho DB Azure Cosmos utiliza o [Azure Cosmos DB Change Feed](../cosmos-db/change-feed.md) para ouvir inserções e atualizações em divisórias. O feed de alteração publica inserções e atualizações, não eliminações.

Para obter informações sobre os detalhes da configuração e configuração, consulte a [visão geral](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

O exemplo que se segue mostra uma [função C#](functions-dotnet-class-library.md) que é invocada quando há inserções ou atualizações na base de dados e recolha especificadas.

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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

O exemplo seguinte mostra uma ligação do gatilho Cosmos DB num ficheiro *function.json* e uma [função de script C#](functions-reference-csharp.md) que utiliza a ligação. A função escreve mensagens de registo quando os registos de Cosmos DB são adicionados ou modificados.

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

Aqui está o código de script C#:

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

Aqui está o código JavaScript:

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

# <a name="python"></a>[Pitão](#tab/python)

O exemplo seguinte mostra uma ligação do gatilho Cosmos DB num ficheiro *function.json* e uma [função Python](functions-reference-python.md) que usa a ligação. A função escreve mensagens de registo quando os registos do Cosmos DB são modificados.

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


Na biblioteca de [tempo de funcionamento](/java/api/overview/azure/functions/runtime)de Java, use a `@CosmosDBTrigger` anotação em parâmetros cujo valor viria de Cosmos DB.  Esta anotação pode ser usada com tipos nativos de `Optional<T>`Java, POJOs ou valores nuníveis usando .

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C #](#tab/csharp)

Nas [bibliotecas da classe C#,](functions-dotnet-class-library.md)use o atributo [CosmosDBTrigger.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs)

O construtor do atributo tem o nome da base de dados e o nome da coleção. Para obter informações sobre essas definições e outras propriedades que possa configurar, consulte [O Gatilho - configuração](#configuration). Aqui está `CosmosDBTrigger` um exemplo de atributo numa assinatura de método:

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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados por C# Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Pitão](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

A partir da biblioteca de tempo `@CosmosDBInput` de funcionamento das [funções java,](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)use a anotação em parâmetros que lêem dados da Cosmos DB.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no `CosmosDBTrigger` atributo.

|propriedade fun.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Tem de `cosmosDBTrigger`ser definido para. |
|**direção** | n/d | Tem de `in`ser definido para. Este parâmetro é definido automaticamente quando cria o gatilho no portal Azure. |
|**nome** | n/d | O nome variável utilizado no código de função que representa a lista de documentos com alterações. |
|**conexãoStringSetting**|**Definição de cordas de ligação** | O nome de uma definição de aplicação que contém a cadeia de ligação utilizada para ligar à conta Azure Cosmos DB está a ser monitorizada. |
|**nome da base de dados**|**DatabaseName**  | O nome da base de dados Azure Cosmos DB com a coleção a ser monitorizada. |
|**coleçãoNome** |**CollectionName** | O nome da coleção está a ser monitorizado. |
|**arrendamentoConnectionStringSetting** | **ArrendamentoConexStringSetting** | (Opcional) O nome de uma configuração de aplicação que contém a cadeia de ligação à conta Azure Cosmos DB que detém a coleção de arrendamento. Quando não definido, o `connectionStringSetting` valor é utilizado. Este parâmetro é automaticamente definido quando a ligação é criada no portal. A corda de ligação para a coleção de arrendamentos deve ter permissões de escrita.|
|**arrendamentoNome basede-dados** |**Nome de leasing Database** | (Opcional) O nome da base de dados que detém a coleção utilizada para armazenar arrendamentos. Quando não está definido, `databaseName` o valor da definição é utilizado. Este parâmetro é automaticamente definido quando a ligação é criada no portal. |
|**leaseCollectionName** | **LeaseCollectionName** | (Opcional) O nome da coleção costumava armazenar arrendamentos. Quando não definido, `leases` o valor é utilizado. |
|**criarLeaseCollectionIfNotExists** | **CreateLeaseCollectionifNotexists** | (Opcional) Quando definida, `true`a coleção de arrendamentos é automaticamente criada quando já não existe. O valor predefinido é `false`. |
|**arrendamentosCollectionThroughputput**| **LeasesCollectionThroughput**| (Opcional) Define o número de Unidades de Pedido para atribuir quando a coleção de arrendamentos é criada. Esta definição só `createLeaseCollectionIfNotExists` é `true`utilizada quando está definida para . Este parâmetro é automaticamente definido quando a ligação é criada utilizando o portal.
|**arrendamentoCollectionPrefix**| **LeaseCollectionPrefix**| (Opcional) Quando definido, o valor é adicionado como prefixo para os arrendamentos criados na coleção lease para esta Função. A utilização de um prefixo permite que duas funções Azure separadas partilhem a mesma coleção de Arrendamento utilizando prefixos diferentes.
|**feedPollDelay**| **FeedPollDelay**| (Opcional) O tempo (em milissegundos) para o atraso entre a sondagem de uma partição para novas alterações no feed, depois de todas as alterações atuais serem drenadas. O padrão é de 5.000 milissegundos, ou 5 segundos.
|**arrendamentoAdquirirInterval**| **Intervalo de Arrendamento**| (Opcional) Quando definido, define, em milissegundos, o intervalo para iniciar uma tarefa de calcular se as divisórias são distribuídas uniformemente entre as instâncias conhecidas do anfitrião. O padrão é de 13000 (13 segundos).
|**intervalo de validade**| **Intervalo de locação prazo**| (Opcional) Quando definido, define, em milissegundos, o intervalo para o qual o arrendamento é assumido num contrato de arrendamento que representa uma partição. Se o contrato de arrendamento não for renovado dentro deste intervalo, irá fazê-lo expirar e a propriedade da partição passará para outra instância. O padrão é de 60000 (60 segundos).
|**arrendamentoRenewInterval**| **Intervalo de Arrendamento Renovado**| (Opcional) Quando definido, define, em milissegundos, o intervalo de renovação para todos os arrendamentos para divisórias atualmente detidos por exemplo. O padrão é de 17000 (17 segundos).
|**checkpointFrequência**| **CheckpointFrequência**| (Opcional) Quando definido, define, em milissegundos, o intervalo entre os pontos de verificação de locação. O predefinido é sempre posterior a cada chamada de função.
|**maxItemsPerInvocação**| **MaxItemsPerInvocação**| (Opcional) Quando definido, esta propriedade define o número máximo de itens recebidos por chamada de Função. Se as operações na recolha monitorizada forem realizadas através de procedimentos armazenados, o âmbito de [transação](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) é preservado ao ler itens do Change Feed. Como resultado, o número de itens recebidos pode ser superior ao valor especificado, de modo a que os itens alterados pela mesma transação sejam devolvidos como parte de um lote atómico.
|**inícioFromBeginning**| **Início do início**| (Opcional) Esta opção diz ao Trigger para ler alterações desde o início do histórico de mudança da coleção em vez de começar no momento atual. A leitura desde o início só funciona na primeira vez que o Gatilho começa, uma vez que nas corridas subsequentes, os postos de controlo já estão armazenados. Definir esta `true` opção para quando há arrendamentos já criados não tem efeito. |
|**preferredLocalizações**| **Localizações Preferenciais**| (Opcional) Define localizações preferenciais (regiões) para contas de base de dados geo-replicadas no serviço Azure Cosmos DB. Os valores devem ser separados pela vírposta. Por exemplo, "Leste dos EUA, Centro-Sul dos EUA, Norte da Europa". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

O gatilho requer uma segunda coleção que utiliza para armazenar _arrendamentos_ sobre as divisórias. Tanto a recolha que está a ser monitorizada como a recolha que contém os contratos de arrendamento devem estar disponíveis para o gatilho funcionar.

>[!IMPORTANT]
> Se várias funções estiverem configuradas para utilizar um gatilho Cosmos DB para a mesma recolha, `LeaseCollectionPrefix` cada uma das funções deve utilizar uma coleção de locação dedicada ou especificar um diferente para cada função. Caso contrário, apenas uma das funções será desencadeada. Para obter informações sobre o prefixo, consulte a [secção Configuração](#configuration).

O gatilho não indica se um documento foi atualizado ou inserido, apenas fornece o documento em si. Se precisar de lidar com atualizações e inserções de forma diferente, poderá fazê-lo implementando campos de carimbos de tempo para inserção ou atualização.

## <a name="next-steps"></a>Passos seguintes

- [Leia um documento Azure Cosmos DB (encadernação de entrada)](./functions-bindings-cosmosdb-v2-input.md)
- [Guardar alterações num documento Azure Cosmos DB (Encadernação de saída)](./functions-bindings-cosmosdb-v2-output.md)
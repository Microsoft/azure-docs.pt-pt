---
title: Gatilho DB Azure Cosmos para funções 2.x e superior
description: Aprenda a usar o gatilho Azure Cosmos DB em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 6f4e43efeb1882f52bd335d83a3660a94040ab8a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101729220"
---
# <a name="azure-cosmos-db-trigger-for-azure-functions-2x-and-higher"></a>Azure Cosmos DB gatilho para funções Azure 2.x e superior

O Azure Cosmos DB Trigger utiliza o [Azure Cosmos DB Change Feed](../cosmos-db/change-feed.md) para ouvir inserções e atualizações através de divisórias. O feed de alteração publica inserções e atualizações, não eliminações.

Para obter informações sobre detalhes de configuração e configuração, consulte a [visão geral](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que é invocada quando há inserções ou atualizações na base de dados e recolha especificadas.

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

O exemplo a seguir mostra uma ligação do gatilho Cosmos DB numa *function.jsno* ficheiro e uma [função de script C#](functions-reference-csharp.md) que utiliza a ligação. A função escreve mensagens de registo quando os registos de DB do Cosmos são adicionados ou modificados.

Aqui estão os dados vinculativos do *function.jsarquivado:*

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

Aqui está o código do guião C:

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


Na biblioteca de [funções java,](/java/api/overview/azure/functions/runtime)use a `@CosmosDBTrigger` anotação em parâmetros cujo valor viria do Cosmos DB.  Esta anotação pode ser usada com tipos nativos de Java, POJOs ou valores anulados usando `Optional<T>` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma ligação do gatilho Cosmos DB numa *function.jsno* ficheiro e numa [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função escreve mensagens de registo quando os registos de DB do Cosmos são adicionados ou modificados.

Aqui estão os dados vinculativos do *function.jsarquivado:*

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

O exemplo a seguir mostra como executar uma função à medida que os dados mudam em Cosmos DB.

```json
{
  "type": "cosmosDBTrigger",
  "name": "Documents",
  "direction": "in",
  "leaseCollectionName": "leases",
  "connectionStringSetting": "MyStorageConnectionAppSetting",
  "databaseName": "Tasks",
  "collectionName": "Items",
  "createLeaseCollectionIfNotExists": true
}
```

No ficheiro _run.ps1,_ tem acesso ao documento que desencadeia a função através do `$Documents` parâmetro.

```powershell
param($Documents, $TriggerMetadata) 

Write-Host "First document Id modified : $($Documents[0].id)" 
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir mostra uma ligação do gatilho Cosmos DB numa *function.jsem* ficheiro e numa [função Python](functions-reference-python.md) que usa a ligação. A função escreve mensagens de registo quando os registos de DB do Cosmos são modificados.

Aqui estão os dados vinculativos do *function.jsarquivado:*

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

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize o atributo [CosmosDBTrigger.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs)

O construtor do atributo tem o nome da base de dados e o nome da recolha. Para obter informações sobre as configurações e outras propriedades que possa configurar, consulte [a configuração Trigger -](#configuration). Aqui está um `CosmosDBTrigger` exemplo de atributo numa assinatura de método:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run([CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
        IReadOnlyList<Document> documents,
        ILogger log)
    {
        ...
    }
```

Para um exemplo completo, consulte [Trigger](#example).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados pelo Script C#.

# <a name="java"></a>[Java](#tab/java)

A partir da [biblioteca de funções java,](/java/api/overview/azure/functions/runtime)use a `@CosmosDBInput` anotação em parâmetros que lêem dados da Cosmos DB.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Os atributos não são suportados pela PowerShell.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pela Python.

---

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `CosmosDBTrigger` atributo.

|function.jsna propriedade | Propriedade de atributo |Description|
|---------|---------|----------------------|
|**tipo** | n/a | Deve ser definido para `cosmosDBTrigger` . |
|**direção** | n/a | Deve ser definido para `in` . Este parâmetro é definido automaticamente quando cria o gatilho no portal Azure. |
|**nome** | n/a | O nome variável usado no código de função que representa a lista de documentos com alterações. |
|**conexãoStringSetting**|**ConexãoStringSetting** | O nome de uma definição de aplicação que contém a cadeia de ligação usada para ligar à conta DB Azure Cosmos que está a ser monitorizada. |
|**base de dados Nome**|**DatabaseName**  | O nome da base de dados DB Azure Cosmos com a recolha a ser monitorizada. |
|**coleçãoName** |**CollectionName** | O nome da coleção está a ser monitorizado. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Opcional) O nome de uma definição de aplicação que contém a cadeia de ligação à conta DB Azure Cosmos que detém a coleção de arrendamento. Quando não definido, o `connectionStringSetting` valor é utilizado. Este parâmetro é automaticamente definido quando a ligação é criada no portal. A cadeia de ligação para a coleção de arrendamentos deve ter permissões de escrita.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Opcional) O nome da base de dados que detém a coleção usada para armazenar arrendamentos. Quando não estiver definido, o valor da `databaseName` definição é utilizado. Este parâmetro é automaticamente definido quando a ligação é criada no portal. |
|**leaseCollectionName** | **Contrato de ArrendamentoCollectionName** | (Opcional) O nome da coleção usada para armazenar arrendamentos. Quando não definido, o valor `leases` é utilizado. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (Opcional) Quando definido `true` para, a coleção de arrendamentos é criada automaticamente quando já não existe. O valor predefinido é `false`. |
|**locaçõesCollectionThroughput**| **LocaçõesCollectionThroughput**| (Opcional) Define o número de Unidades de Pedido a atribuir quando a coleção de arrendamentos é criada. Esta definição só é utilizada quando `createLeaseCollectionIfNotExists` está definida para `true` . Este parâmetro é automaticamente definido quando a ligação é criada utilizando o portal.
|**leaseCollectionPrefix**| **Contrato de Arrendamento**| (Opcional) Quando definido, o valor é adicionado como um prefixo aos arrendamentos criados na coleção Arrendamento para esta Função. A utilização de um prefixo permite que duas Funções Azure separadas partilhem a mesma coleção De Arrendamento utilizando prefixos diferentes.
|**feedPollDelay**| **FeedPollDelay**| (Opcional) O tempo (em milissegundos) para o atraso entre a votação de uma divisão para novas alterações no feed, depois de todas as alterações atuais serem drenadas. O padrão é de 5.000 milissegundos, ou 5 segundos.
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Opcional) Quando definido, define, em milissegundos, o intervalo para iniciar uma tarefa de cálculo se as divisórias forem distribuídas uniformemente entre instâncias de anfitrião conhecidas. O padrão é de 13000 (13 segundos).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (Opcional) Quando definido, define, em milissegundos, o intervalo para o qual o arrendamento é tomado em um arrendamento que representa uma partição. Se o arrendamento não for renovado dentro deste intervalo, fará com que expire e a propriedade da partição passará para outro caso. O padrão é de 60000 (60 segundos).
|**leaseRenewInterval**| **LeaseRenewInterval**| (Opcional) Quando definido, define, em milissegundos, o intervalo de renovação de todos os arrendamentos para divisórias atualmente detidos por exemplo. O padrão é de 17000 (17 segundos).
|**checkpointFrequência**| **CheckpointFrequência**| (Opcional) Quando definido, define, em milissegundos, o intervalo entre os pontos de verificação do arrendamento. O padrão é sempre após cada chamada de Função.
|**maxitemsPerInvocation**| **MaxItemsPerInvocation**| (Opcional) Quando definido, esta propriedade define o número máximo de itens recebidos por chamada de Função. Se as operações na recolha monitorizada forem efetuadas através de procedimentos armazenados, o âmbito de [transação](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) é preservado ao ler artigos do Feed de Alteração. Como resultado, o número de itens recebidos pode ser superior ao valor especificado de modo a que os itens alterados pela mesma transação sejam devolvidos como parte de um lote atómico.
|**inícioFromBeginning**| **Início DoBeginning**| (Opcional) Esta opção diz ao Gatilho para ler alterações desde o início do histórico de mudanças da coleção em vez de começar no momento atual. A leitura do início só funciona da primeira vez que o Gatilho começa, pois nas execuções subsequentes, os pontos de verificação já estão armazenados. Definir esta opção `true` para quando há arrendamentos já criados não tem efeito. |
|**preferênciasLoções**| **PreferênciasLocações**| (Opcional) Define localizações preferenciais (regiões) para contas de base de dados geo-replicadas no serviço DB Azure Cosmos. Os valores devem ser separados por vírgula. Por exemplo, "Leste dos EUA, Centro Sul dos EUA, Norte da Europa". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

O gatilho requer uma segunda coleção que usa para armazenar _arrendamentos_ sobre as divisórias. Tanto a recolha que está a ser monitorizada como a coleção que contém os contratos de arrendamento devem estar disponíveis para o gatilho funcionar.

>[!IMPORTANT]
> Se várias funções forem configuradas para usar um gatilho Cosmos DB para a mesma coleção, cada uma das funções deve usar uma coleção de locação dedicada ou especificar uma diferente `LeaseCollectionPrefix` para cada função. Caso contrário, apenas uma das funções será ativada. Para obter informações sobre o prefixo, consulte a [secção Configuração](#configuration).

O gatilho não indica se um documento foi atualizado ou inserido, apenas fornece o documento em si. Se precisar de lidar com atualizações e inserções de forma diferente, poderá fazê-lo implementando campos de tempotamp para inserção ou atualização.

## <a name="next-steps"></a>Passos seguintes

- [Leia um documento DB da Azure Cosmos (ligação à entrada)](./functions-bindings-cosmosdb-v2-input.md)
- [Guardar alterações num documento DB do Azure Cosmos (ligação à saída)](./functions-bindings-cosmosdb-v2-output.md)

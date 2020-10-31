---
title: Utilize o armazenamento da mesa Azure ou a API da tabela Azure Cosmos dB a partir de Node.js
description: Armazenar dados estruturados na nuvem utilizando o armazenamento da Tabela Azure ou a API da Tabela DB Azure Cosmos a partir de Node.js.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: nodejs
ms.topic: sample
ms.date: 07/23/2020
author: sakash279
ms.author: akshanka
ms.custom: devx-track-js
ms.openlocfilehash: 2d40b70d49b1934c9dd2d911369245b1b2e4f2ff
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93079712"
---
# <a name="how-to-use-azure-table-storage-or-the-azure-cosmos-db-table-api-from-nodejs"></a>Como utilizar o Armazenamento de tabelas do Azure ou a API de Tabelas do Azure Cosmos DB a partir de Node.js
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Este artigo mostra-lhe como criar tabelas, armazenar os seus dados e realizar operações CRUD nos dados. Escolha o serviço Azure Table ou a API da Tabela DB AZure Cosmos. As amostras estão escritas em Node.js.

## <a name="create-an-azure-service-account"></a>Criar uma conta de serviço do Azure

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Criar uma conta de armazenamento Azure**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Criar uma conta da API de Tabelas do Azure Cosmos DB**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="configure-your-application-to-access-azure-storage-or-the-azure-cosmos-db-table-api"></a>Configurar a aplicação para aceder ao Armazenamento do Azure ou à API de Tabelas do Azure Cosmos DB

Para utilizar o Armazenamento do Azure ou o Azure Cosmos DB, necessita do Azure Storage SDK para Node.js, o qual inclui um conjunto de bibliotecas úteis que comunicam com os serviços REST de Armazenamento.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Utilize o NPM (Node Package Manager, Gestor de Pacotes do Nó) para instalar o pacote

1. Utilize uma interface de linha de comandos, como o **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix) e navegue para a pasta onde criou a aplicação.
2. Escreva **npm install azure-storage** na janela de comandos. Os resultados do comando são semelhantes ao seguinte exemplo.

   ```bash
    azure-storage@0.5.0 node_modules\azure-storage
    +-- extend@1.2.1
    +-- xmlbuilder@0.4.3
    +-- mime@1.2.11
    +-- node-uuid@1.4.3
    +-- validator@3.22.2
    +-- underscore@1.4.4
    +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
    +-- xml2js@0.2.7 (sax@0.5.2)
    +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
   ```

3. Pode executar manualmente o comando **ls** para verificar se foi criada uma pasta **node_modules** . Nessa pasta, encontrará o pacote **azure-storage** , que contém as bibliotecas de que precisa para aceder ao armazenamento.

### <a name="import-the-package"></a>Importar o pacote

Adicione o seguinte código ao início do ficheiro **server.js** na sua aplicação:

```javascript
var azure = require('azure-storage');
```

## <a name="add-your-connection-string"></a>Adicione a sua cadeia de conexão

Pode ligar-se à conta de armazenamento Azure ou à conta AZure Cosmos DB Table API. Obtenha a cadeia de ligação com base no tipo de conta que está a utilizar.

### <a name="add-an-azure-storage-connection"></a>Adicionar uma ligação ao Armazenamento do Azure

O módulo do Azure lê as variáveis de ambiente AZURE_STORAGE_ACCOUNT e AZURE_STORAGE_ACCESS_KEY ou AZURE_STORAGE_CONNECTION_STRING para obter as informações necessárias para ligar à sua conta do Armazenamento do Azure. Se estas variáveis ambientais não estiverem definidas, deve especificar as informações da conta ao ligar `TableService` . Por exemplo, o seguinte código cria um `TableService` objeto:

```javascript
var tableSvc = azure.createTableService('myaccount', 'myaccesskey');
```

### <a name="add-an-azure-cosmos-db-connection"></a>Adicionar uma ligação do Azure Cosmos DB

Para adicionar uma ligação DB Azure Cosmos, crie um `TableService` objeto e especifique o nome da sua conta, chave primária e ponto final. Pode copiar estes **valores** a partir da Cadeia de Ligação de Definições  >  **Connection String** no portal Azure para a sua conta Cosmos DB. Por exemplo:

```javascript
var tableSvc = azure.createTableService('myaccount', 'myprimarykey', 'myendpoint');
```

## <a name="create-a-table"></a>Criar uma tabela

O código a seguir cria um `TableService` objeto e utiliza-o para criar uma nova tabela.

```javascript
var tableSvc = azure.createTableService();
```

A chamada para `createTableIfNotExists` criar uma nova tabela com o nome especificado se já não existir. O exemplo seguinte cria uma nova tabela chamada "mytable", se ainda não existir:

```javascript
tableSvc.createTableIfNotExists('mytable', function(error, result, response){
  if(!error){
    // Table exists or created
  }
});
```

`result.created` é `true` se for criada uma nova tabela e `false` se a tabela já existir. `response` contém informações sobre o pedido.

### <a name="apply-filters"></a>Aplicar filtros

Pode aplicar filtragem opcional às operações efetuadas utilizando `TableService` . As operações de filtragem podem incluir registos, retrórias automáticas, etc. Os filtros são objetos que implementam um método com a assinatura:

```javascript
function handle (requestOptions, next)
```

Depois de efetuar o pré-processamentos nas opções do pedido, o método tem de chamar **next** , transmitindo uma chamada de retorno com a seguinte assinatura:

```javascript
function (returnObject, finalCallback, next)
```

Nesta chamada, e após o processamento `returnObject` da (resposta do pedido ao servidor), a chamada deve invocar `next` se existir para continuar a processar outros filtros ou simplesmente invocar `finalCallback` o contrário para terminar a invocação do serviço.

Dois filtros que implementam a lógica de repetição estão incluídos no Azure SDK para Node.js, `ExponentialRetryPolicyFilter` e `LinearRetryPolicyFilter` . O seguinte cria um `TableService` objeto que utiliza `ExponentialRetryPolicyFilter` o:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var tableSvc = azure.createTableService().withFilter(retryOperations);
```

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

Para adicionar uma entidade, crie primeiro um objeto que defina as propriedades da sua entidade. Todas as entidades têm de conter **PartitionKey** e **RowKey** , que são identificadores exclusivos da entidade.

* **PartitionKey** - determina a partição na qual a entidade é armazenada.
* **RowKey** - identifica exclusivamente a entidade na partição.

**PartitionKey** e **RowKey** têm de ser valores de cadeia. Para obter mais informações, consulte [Noções Básicas sobre o Modelo de Dados do Serviço Tabela](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

Segue-se um exemplo de definição de uma entidade. O **dueDate** é definido como um tipo de `Edm.DateTime` . A especificação do tipo é opcional. Se não forem especificados, os tipos são inferidos.

```javascript
var task = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'},
  description: {'_':'take out the trash'},
  dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
};
```

> [!NOTE]
> Há também um `Timestamp` campo para cada registo, que é definido pelo Azure quando uma entidade é inserida ou atualizada.

Também pode usar `entityGenerator` o para criar entidades. O exemplo a seguir cria a mesma entidade de tarefa utilizando o `entityGenerator` .

```javascript
var entGen = azure.TableUtilities.entityGenerator;
var task = {
  PartitionKey: entGen.String('hometasks'),
  RowKey: entGen.String('1'),
  description: entGen.String('take out the trash'),
  dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
};
```

Para adicionar uma entidade à sua mesa, passe o objeto da entidade para o `insertEntity` método.

```javascript
tableSvc.insertEntity('mytable',task, function (error, result, response) {
  if(!error){
    // Entity inserted
  }
});
```

Se a operação for concluída com êxito, `result` contém o [ETag](https://en.wikipedia.org/wiki/HTTP_ETag) do registo inserido e `response` contém as informações sobre a operação.

Resposta de exemplo:

```javascript
{ '.metadata': { etag: 'W/"datetime\'2015-02-25T01%3A22%3A22.5Z\'"' } }
```

> [!NOTE]
> Por predefinição, `insertEntity` não devolve a entidade inserida como parte da `response` informação. Se planear efetuar outras operações nesta entidade ou se pretender colocar em cache as informações, pode ser útil devolvê-la como parte de `result`. Pode fazê-lo permitindo o `echoContent` seguinte:
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## <a name="update-an-entity"></a>Atualizar uma entidade

Existem vários métodos disponíveis para atualizar uma entidade existente:

* `replaceEntity` - Atualiza uma entidade existente substituindo-a.
* `mergeEntity` - Atualiza uma entidade existente através da fusão de novos valores imobiliários na entidade existente.
* `insertOrReplaceEntity` - Atualiza uma entidade existente substituindo-a. Se não existir nenhuma entidade, será inserida uma nova.
* `insertOrMergeEntity` - Atualiza uma entidade existente através da fusão de novos valores imobiliários no existente. Se não existir nenhuma entidade, será inserida uma nova.

O exemplo a seguir demonstra a atualização de uma entidade que `replaceEntity` utiliza:

```javascript
tableSvc.replaceEntity('mytable', updatedTask, function(error, result, response){
  if(!error) {
    // Entity updated
  }
});
```

> [!NOTE]
> Por predefinição, atualizar uma entidade não verifica se os dados que estão a ser atualizados foram modificados anteriormente por outro processo. Para suportar atualizações em simultâneo:
>
> 1. Obtenha o ETag do objeto que está a ser atualizado. Este é devolvido como parte de `response` de qualquer operação relacionada com a entidade e pode ser obtido através de `response['.metadata'].etag`.
> 2. Quando efetuar uma operação de atualização numa entidade, adicione as informações de ETag obtidas anteriormente para a nova entidade. Por exemplo:
>
>       entity2['.metadata'].etag = currentEtag;
> 3. Efetue a operação de atualização. Se a entidade tiver sido modificada desde que obteve o valor de ETag, tal como outra instância da sua aplicação, é devolvido um `error` a indicar que a condição de atualização especificada no pedido não foi cumprida.
>
>

Com `replaceEntity` `mergeEntity` e, se a entidade que está a ser atualizada não existir, então a operação de atualização falha; portanto, se pretender armazenar uma entidade independentemente de já existir, utilize `insertOrReplaceEntity` ou `insertOrMergeEntity` ...

O `result` para operações de atualização com êxito contém o **Etag** da entidade atualizada.

## <a name="work-with-groups-of-entities"></a>Trabalhar com grupos de entidades

Por vezes, é útil submeter várias operações em conjunto num batch para garantir um processamento atómico pelo servidor. Para isso, utilize a `TableBatch` classe para criar um lote e, em seguida, use o método para realizar as `executeBatch` `TableService` operações em lote.

 O exemplo seguinte demonstra como submeter duas entidades num batch:

```javascript
var task1 = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'},
  description: {'_':'Take out the trash'},
  dueDate: {'_':new Date(2015, 6, 20)}
};
var task2 = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '2'},
  description: {'_':'Wash the dishes'},
  dueDate: {'_':new Date(2015, 6, 20)}
};

var batch = new azure.TableBatch();

batch.insertEntity(task1, {echoContent: true});
batch.insertEntity(task2, {echoContent: true});

tableSvc.executeBatch('mytable', batch, function (error, result, response) {
  if(!error) {
    // Batch completed
  }
});
```

Para operações batch de êxito, `result` contém informações para cada operação no batch.

### <a name="work-with-batched-operations"></a>Trabalhar com operações em batch

Pode inspecionar as operações adicionadas a um batch visualizando a propriedade `operations`. Também pode utilizar os métodos seguintes para trabalhar com operações:

* **clear** - limpa todas as operações de um batch.
* **getOperations** - obtém uma operação do batch.
* **hasOperations** - devolve true se o batch contiver operações.
* **removeOperations** - remove uma operação.
* **size** - devolve o número de operações no batch.

## <a name="retrieve-an-entity-by-key"></a>Obter uma entidade por chave

Para devolver uma entidade específica com base em **PartitionKey** e **RowKey** , utilize o método **retrieveEntity** .

```javascript
tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
  if(!error){
    // result contains the entity
  }
});
```

Depois de concluir a operação, `result` contém a entidade.

## <a name="query-a-set-of-entities"></a>Consultar um conjunto de entidades

Para consultar uma tabela, utilize o objeto **TableQuery** para criar uma expressão de consulta utilizando as seguintes cláusulas:

* **select** - os campos a devolver pela consulta.
* **where** - cláusula where.

  * **and** - uma condição `and` where.
  * **or** - uma condição `or` where.
* **top** - o número de itens a obter.

O exemplo seguinte cria uma consulta que devolve os primeiros cinco itens com PartitionKey de 'hometasks'.

```javascript
var query = new azure.TableQuery()
  .top(5)
  .where('PartitionKey eq ?', 'hometasks');
```

Dado que não utiliza **select** , são devolvidos todos os campos. Para executar a consulta em relação a uma tabela, utilize **queryEntities** . O exemplo seguinte utiliza esta consulta para consultar as entidades de 'mytable'.

```javascript
tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
  if(!error) {
    // query was successful
  }
});
```

Se tiver êxito, `result.entries` contém uma matriz de entidades que correspondem à consulta. Se a consulta não conseguir devolver todas as entidades, `result.continuationToken` é não *null* e pode ser utilizado como o terceiro parâmetro de **queryEntities** para obter mais resultados. Para a consulta inicial, utilize *null* para o terceiro parâmetro.

### <a name="query-a-subset-of-entity-properties"></a>Consultar um subconjunto de propriedades de entidade

Uma consulta a uma tabela pode obter apenas alguns campos de uma entidade.
Isto reduz a largura de banda e pode melhorar o desempenho da consulta, especialmente para entidades grandes. Utilize a cláusula **select** e transmita os nomes dos campos a devolver. Por exemplo, a consulta seguinte devolve apenas os campos **description** e **dueDate** .

```javascript
var query = new azure.TableQuery()
  .select(['description', 'dueDate'])
  .top(5)
  .where('PartitionKey eq ?', 'hometasks');
```

## <a name="delete-an-entity"></a>Eliminar uma entidade

Pode eliminar uma entidade ao utilizar as chaves de partição e linha. Neste exemplo, o objeto **task1** contém os valores **RowKey** e **PartitionKey** da entidade a eliminar. Em seguida, o objeto é transmitido ao método **deleteEntity** .

```javascript
var task = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'}
};

tableSvc.deleteEntity('mytable', task, function(error, response){
  if(!error) {
    // Entity deleted
  }
});
```

> [!NOTE]
> Considere utilizar ETags quando eliminar itens para se certificar de que o item não foi modificado por outro processo. Consulte [Atualizar uma entidade](#update-an-entity) para obter informações sobre como utilizar ETags.
>
>

## <a name="delete-a-table"></a>Eliminar uma tabela

O código seguinte elimina uma tabela de uma conta de armazenamento.

```javascript
tableSvc.deleteTable('mytable', function(error, response){
    if(!error){
        // Table deleted
    }
});
```

Se não tiver a certeza se a tabela existe, utilize **deleteTableIfExists** .

## <a name="use-continuation-tokens"></a>Utilizar tokens de continuação

Quando estiver a consultar tabelas para grandes quantidades de resultados, procure os tokens de continuação. Poderão existir grandes quantidades de dados disponíveis para a consulta que podem passar despercebidos caso não efetue a compilação para determinar a existência de um token de continuação.

O objeto **results** devolvido durante a consulta de entidades define uma propriedade `continuationToken` quando um token desse tipo existe. Pode então utilizá-lo quando executar uma consulta para continuar a percorrer as entidades da partição e da tabela.

Durante a consulta, pode fornecer um parâmetro `continuationToken` entre a instância do objeto de consulta e a função de chamada de retorno:

```javascript
var nextContinuationToken = null;
dc.table.queryEntities(tableName,
    query,
    nextContinuationToken,
    function (error, results) {
        if (error) throw error;

        // iterate through results.entries with results

        if (results.continuationToken) {
            nextContinuationToken = results.continuationToken;
        }

    });
```

Se inspecionar o objeto `continuationToken`, encontrará propriedades como `nextPartitionKey`, `nextRowKey` e `targetLocation`, que podem ser utilizadas para iterar todos os resultados.

Também pode usar `top` para definir o tamanho da `continuationToken` página.

## <a name="work-with-shared-access-signatures"></a>Trabalhar com assinaturas de acesso partilhado

As assinaturas de acesso partilhado (SAS) são uma forma segura de fornecer acesso granular às tabelas sem fornecer o nome ou a chaves da sua conta de Armazenamento. As SAS são frequentemente utilizadas para fornecer acesso limitado aos seus dados, tal como permitir que uma aplicação móvel consulte registos.

Uma aplicação fidedigna, como um serviço baseado na cloud, gera uma SAS utilizando **generateSharedAccessSignature** de **TableService** e fornece-a a uma aplicação não fidedigna ou semifidedigna, como uma aplicação móvel. A SAS é gerada utilizando uma política, que descreve as datas de início e de fim durante as quais a SAS é válida, bem como o nível de acesso concedido ao titular da SAS.

O exemplo seguinte gera uma nova política de acesso partilhado, que permitirá ao titular da SAS consultar ('r') a tabela e que expira 100 minutos após a hora de criação.

```javascript
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
    Start: startDate,
    Expiry: expiryDate
  },
};

var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
var host = tableSvc.host;
```

Tenha em atenção que também tem de fornecer as informações do host, dado que é necessário quando o titular da SAS tenta aceder à tabela.

A aplicação cliente utiliza então a SAS com **TableServiceWithSAS** para executar operações na tabela. O exemplo seguinte liga-se à tabela e executa uma consulta. Consulte o Grant acesso limitado aos recursos de Armazenamento Azure utilizando o artigo [assinaturas de acesso partilhado (SAS)](../storage/common/storage-sas-overview.md) para o formato do tableSAS.

```javascript
// Note in the following command, host is in the format: `https://<your_storage_account_name>.table.core.windows.net` and the tableSAS is in the format: `sv=2018-03-28&si=saspolicy&tn=mytable&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D`;

var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
var query = azure.TableQuery()
  .where('PartitionKey eq ?', 'hometasks');

sharedTableService.queryEntities(query, null, function(error, result, response) {
  if(!error) {
    // result contains the entities
  }
});
```

Como a SAS foi gerada com acesso apenas de consulta, é devolvido um erro se tentar inserir, atualizar ou eliminar entidades.

### <a name="access-control-lists"></a>Listas de Controlo de Acesso

Também pode utilizar uma Lista de Controlo de Acesso (ACL) para definir a política de acesso para uma SAS. Isto é útil se pretender permitir que os vários clientes acedam à tabela, mas cada cliente deva ter uma política de acesso diferente.

Uma ACL é implementada com uma matriz de políticas de acesso, com um ID associado a cada política. O exemplo seguinte define duas políticas, uma para "user1" e outra para "user2":

```javascript
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

O exemplo seguinte obtém a ACL atual para a tabela **hometasks** e, em seguida, adiciona as novas políticas utilizando **setTableAcl** . Esta abordagem permite:

```javascript
var extend = require('extend');
tableSvc.getTableAcl('hometasks', function(error, result, response) {
if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    tableSvc.setTableAcl('hometasks', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

Depois de definir a ACL, pode criar uma SAS com base no ID de uma política. O exemplo seguinte cria uma nova SAS para "user2":

```javascript
tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte os seguintes recursos.

* O [Explorador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é uma aplicação autónoma e gratuita da Microsoft, que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.
* Repositório do [Azure Storage SDK para Node.js](https://github.com/Azure/azure-storage-node) no GitHub.
* [Programadores do Azure para Node.js](/azure/developer/javascript/)
* [Criar uma aplicação Web Node.js no Azure](../app-service/quickstart-nodejs.md)
* [Criar e implementar uma aplicação Node.js num Serviço Cloud do Azure](../cloud-services/cloud-services-nodejs-develop-deploy-app.md) (utilizando o Windows PowerShell)
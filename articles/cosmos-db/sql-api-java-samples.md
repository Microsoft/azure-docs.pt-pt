---
title: 'Azure Cosmos DB: Exemplos Java para a API SQL'
description: Encontre exemplos Java no GitHub para tarefas comuns com a API SQL do Azure Cosmos BD, incluindo operações do CRUD.
keywords: Exemplo de NoSQL
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: java
ms.topic: sample
ms.date: 02/08/2018
ms.author: sngun
ms.openlocfilehash: 135613be26ed0ebc5dfd180112fabb0401f60027
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52874180"
---
# <a name="azure-cosmos-db-java-examples-for-the-sql-api"></a>Azure Cosmos DB: Exemplos Java para a API SQL

> [!div class="op_single_selector"]
> * [Exemplos .NET](sql-api-dotnet-samples.md)
> * [Exemplos de Java](sql-api-java-samples.md)
> * [Exemplos de Async Java](sql-api-async-java-samples.md)
> * [Exemplos de Node.js](sql-api-nodejs-samples.md)
> * [Exemplos de Python](sql-api-python-samples.md)
> * [Galeria de Exemplo de Código do Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Estão incluídas as mais recentes aplicações de exemplo que executam operações CRUD e outras operações comuns em recursos do Azure Cosmos DB no repositório do GitHub [azure-documentdb-java](https://github.com/Azure/azure-documentdb-java). Este artigo fornece:

* Ligações para as tarefas em cada um dos ficheiros do projeto de Java de exemplo. 
* Ligações para o conteúdo relacionado de referência da API.

**Pré-requisitos**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- Pode [ativar os benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): a sua subscrição do Visual Studio dá-lhe créditos todos os meses que pode utilizar em serviços pagos do Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Precisa do seguinte para executar este exemplo de aplicação:

* Kit de desenvolvimento do Java 7
* SDK do Java do DocumentDB do Microsoft Azure

Opcionalmente, pode utilizar o Maven para obter os binários do SDK do Java do DocumentDB do Microsoft Azure mais recentes para utilizar no seu projeto. O Maven adiciona automaticamente quaisquer dependências necessárias. Caso contrário, pode transferir diretamente as dependências listadas no ficheiro pom.xml e adicioná-las ao seu caminho de compilação.

```bash
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-documentdb</artifactId>
    <version>LATEST</version>
</dependency>
```

**Executar os exemplos de aplicações**

Clonar o repositório de exemplo:
```bash
$ git clone https://github.com/Azure/azure-documentdb-java.git

$ cd azure-documentdb-java
```

Pode executar os exemplos com o Eclipse ou a partir da linha de comandos com o Maven.

Para executar a partir do Eclipse:
* Carregue o ficheiro pom.xml do projeto principal no Eclipse; ele deve carregar automaticamente exemplos do documentdb.
* Para executar os exemplos, precisa de um Ponto Final válido do Azure Cosmos DB. Os pontos finais são lidos a partir de `src/test/java/com/microsoft/azure/documentdb/examples/AccountCredentials.java`.
* Pode transmitir as suas credenciais de ponto final como argumentos de VM na Configuração de Execução do Eclipse JUnit ou pode colocar as suas credenciais de ponto final no AccountCredentials.java.
    ```bash
    -DACCOUNT_HOST="https://REPLACE_THIS.documents.azure.com:443/" -DACCOUNT_KEY="REPLACE_THIS"
    ```
* Já pode executar os exemplos como testes do JUnit no Eclipse.

Para executar a partir da linha de comandos:
* A outra forma de executar exemplos é ao utilizar o maven:
* Execute o Maven e transmita as suas credenciais do Ponto Final do Azure Cosmos DB:
    ```bash
    mvn test -DACCOUNT_HOST="https://REPLACE_THIS_WITH_YOURS.documents.azure.com:443/" -DACCOUNT_KEY="REPLACE_THIS_WITH_YOURS"
    ```

   > [!NOTE]
   > Cada amostra é independente, configura-se automaticamente e limpa-se em seguida. Os exemplos emitem várias chamadas para [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createcollection). Cada vez que isto é feito, a sua subscrição é cobrada por 1 hora de utilização pelo escalão de desempenho da coleção criada. 
   > 
   > 

## <a name="database-examples"></a>Exemplos de base de dados
O ficheiro [DatabaseCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java) mostra como realizar as seguintes tarefas:

| Tarefa | Referência da API |
| --- | --- |
| [Criar e ler uma base de dados](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L64-L79) | [DocumentClient.createDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createdatabase)<br>[DocumentClient.readDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.readdatabase)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._resource.setid) |
| [Criar e eliminar uma base de dados](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L82-L93) | [DocumentClient.deleteDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.deletedatabase) |
| [Criar e consultar uma base de dados](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L96-L111) | [DocumentClient.queryDatabases](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.querydatabases) |

## <a name="collection-examples"></a>Exemplos de coleção
O ficheiro [CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) mostra como realizar as seguintes tarefas:

| Tarefa | Referência da API |
| --- | --- |
| [Criar uma coleção de partições única](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L74-L84) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createcollection) |
| [Criar uma coleção de várias partições](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L103-L155) | [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_collection)<br>[PartitionKeyDefinition](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._partition_key_definition)<br>[RequestOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._request_options) |
| [Eliminar uma coleção](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L97-L99) | [DocumentClient.deleteCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.deletecollection) |

## <a name="document-examples"></a>Exemplos de documento
O ficheiro [DocumentCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) mostra como realizar as seguintes tarefas:

| Tarefa | Referência da API |
| --- | --- |
| [Criar, ler e eliminar um documento](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L84-L122) | [DocumentClient.createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createdocument)<br>[DocumentClient.readDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.readdocument)<br>[DocumentClient.deleteDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.deletedocument) |
| [Criar um documento com uma definição de documento programável](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L126-L147) | [Documento](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._resource.setid) |

## <a name="indexing-examples"></a>Exemplos de indexação
O ficheiro [CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) mostra como realizar as seguintes tarefas:

| Tarefa | Referência da API |
| --- | --- |
| [Criar um índice e definir política de indexação](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L125-L141) | [Índice](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._index)<br>[IndexingPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._indexing_policy) |

Para obter mais informações sobre a indexação, veja [Políticas de indexação do Azure Cosmos DB](index-policy.md).

## <a name="query-examples"></a>Exemplos de consultas
O ficheiro [DocumentQuerySamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java) mostra como realizar as seguintes tarefas:

| Tarefa | Referência da API |
| --- | --- |
| [Executar uma consulta de documento simples entre partições](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L108-L129) | [DocumentClient.queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.querydocuments)<br>[FeedOptions.setEnableCrossPartitionQuery](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options.setenablecrosspartitionquery) |
| [Ordenar por consulta](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L132-L154) | [FeedResponse<T>.getQueryIterator](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_response.getqueryiterator) |

Para obter mais informações sobre como escrever consultas, veja [Consulta SQL no Azure Cosmos DB](how-to-sql-query.md).

## <a name="offer-examples"></a>Exemplos de oferta
O ficheiro [OfferCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) mostra como realizar as seguintes tarefas:

| Tarefa | Referência da API |
| --- | --- |
| [Criar uma coleção e definir débito](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L76-L102) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createcollection)<br>[RequestOptions.setOfferThroughput ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._request_options.setofferthroughput) |
| [Ler uma coleção para encontrar a oferta associada](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L108-L132) | [Offer.getContent](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._offer.getcontent)<br>[DocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.replaceoffer)<br>[DocumentClient.readCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.readcollection)<br>[DocumentClient.queryOffers](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.queryoffers) |

## <a name="partition-key-examples"></a>Exemplos da chave de partição
O ficheiro [SinglePartitionCollectionDocumentCrudSample](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java) mostra como realizar as seguintes tarefas:

| Tarefa | Referência da API |
| --- | --- |
| [Criar uma coleção de partições única](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L164-L207) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createcollection) |
| [Alterar a oferta de débito para uma coleção de partições única](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L209-L223) | [DocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.replaceoffer) |

## <a name="stored-procedure-examples"></a>Exemplos do procedimento armazenado
O ficheiro [StoredProcedureSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java) mostra como realizar as seguintes tarefas:

| Tarefa | Referência da API |
| --- | --- |
| [Criar um procedimento armazenado](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L85-L118) | [StoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._stored_procedure)<br>[DocumentClient.createStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createstoredprocedure) |
| [Executar um procedimento armazenado com argumentos](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L121-L144) | [DocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.executestoredprocedure) |
| [Executar um procedimento armazenado com um argumento de objeto](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L147-L177) | [DocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.executestoredprocedure) |

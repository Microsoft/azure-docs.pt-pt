---
title: Azure Cosmos DB Java SDK v4 para notas e recursos de lançamento da SQL API
description: Saiba tudo sobre o Azure Cosmos DB Java SDK v4 para SQL API e SDK incluindo datas de lançamento, datas de aposentadoria e alterações feitas entre cada versão do Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/20/2020
ms.author: anfeldma
ms.openlocfilehash: 30d1ee46854a6bbe695bf2a70c266f71a2f906ec
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85080981"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK v4 para Core (SQL) API: notas de lançamento e recursos
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [SDK v2 de .NET](sql-api-sdk-dotnet.md)
> * [SDK v2 de .NET Core](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [SDK v4 de Java](sql-api-sdk-java-v4.md)
> * [SDK v2 Java assíncrono](sql-api-sdk-async-java.md)
> * [SDK v2 Java síncrono](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Fornecedor de Recursos REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor a granel - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor a granel - Java](sql-api-sdk-bulk-executor-java.md)

O Azure Cosmos DB Java SDK v4 para Core (SQL) combina uma API Async e uma API sync em um artefacto Maven. O V4 SDK traz um desempenho melhorado, novas funcionalidades da API e suporte Async baseado no Project Reator e na [biblioteca Netty.](https://netty.io/) Os utilizadores podem esperar um melhor desempenho com Azure Cosmos DB Java SDK v4 contra o [Azure Cosmos DB Async Java SDK v2](sql-api-sdk-async-java.md) e o [Azure Cosmos DB Sync Java SDK v2](sql-api-sdk-java.md).

> [!IMPORTANT]  
> Estas notas de lançamento são apenas para Azure Cosmos DB Java SDK v4. Se está a utilizar uma versão mais antiga do que v4, consulte o guia [Migrador para Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) para ajudar a atualizar para v4.
>
> Aqui estão três passos para ir rápido!
> 1. Instale o [tempo de execução java suportado mínimo, JDK 8](/java/azure/jdk/?view=azure-java-stable) para que possa utilizar o SDK.
> 2. Trabalhe através do [Quickstart Guide for Azure Cosmos DB Java SDK v4](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) que lhe dá acesso ao artefacto Maven e percorre os pedidos básicos de Azure Cosmos DB.
> 3. Leia as dicas de [desempenho](performance-tips-java-sdk-v4-sql.md) V4 do Azure Cosmos DB Java E4 e guias [de resolução de problemas](troubleshoot-java-sdk-v4-sql.md) para otimizar o SDK para a sua aplicação.
>
> Os [workshops e laboratórios DB da Azure Cosmos](https://aka.ms/cosmosworkshop) são outro grande recurso para aprender a usar a Azure Cosmos DB Java SDK v4!
>

| |  |
|---|---|
| **Download SDK** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**Documentação da API** | [Documentação de referência da API de Java](https://docs.microsoft.com/java/api/overview/azure/cosmosdb/client?view=azure-java-stable) |
|**Contribuir para a SDK** | [Azure SDK para Java Central Repo no GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos) | 
|**Introdução** | [Quickstart: Construir uma app Java para gerir dados da Azure Cosmos DB SQL API](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) · [GitHub repo com código de arranque rápido](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**Amostras de código básico** | [Azure Cosmos DB: Exemplos de Java para a API SQL](sql-api-java-sdk-samples.md) · [GitHub repo com código de amostra](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**App de consola com Change Feed**| [Alterar feed - Amostra java SDK v4](create-sql-api-java-changefeed.md) · [GitHub repo com código de amostra](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Amostra de aplicativo web**| [Construir uma aplicação web com Java SDK v4](sql-api-java-application.md) · [GitHub repo com código de amostra](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Sugestões de desempenho**| [Sugestões de desempenho para o SDK v4 Java](performance-tips-java-sdk-v4-sql.md)| 
| **Resolução de problemas** | [Resolver problemas do SDK v4 Java](troubleshoot-java-sdk-v4-sql.md) |
| **Migrar para v4 de um SDK mais antigo** | [Migrar para o SDK Java V4](migrate-java-v4-sdk.md) |
| **Tempo mínimo suportado**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 
| **Workshops e laboratórios DB da Azure Cosmos** |[Página inicial dos workshops do Cosmos DB](https://aka.ms/cosmosworkshop)

## <a name="release-history"></a>História do lançamento

### <a name="401-2020-06-10"></a>4.0.1 (2020-06-10)
#### <a name="new-features"></a>Novas Funcionalidades
* Renomeado `QueryRequestOptions` para `CosmosQueryRequestOptions` .
* Atualizado `ChangeFeedProcessorBuilder` para o padrão de construtor.
* Atualizado `CosmosPermissionProperties` com novo nome de contentor e APIs de recursos infantis.
* Adicionei mais amostras & médicos enriquecidos a `CosmosClientBuilder` . 
* `CosmosDatabase`  &  `CosmosContainer` APIs atualizados com propriedades de produção para suporte de autoescala/piloto automático. 
* Renomeado `CosmosClientException` para `CosmosException` . 
* Substituído `AccessCondition`  &  `AccessConditionType` por `ifMatchETag()`  &  `ifNoneMatchETag()` APIs. 
* Fundiu todos os `Cosmos*AsyncResponse`  &  `CosmosResponse` tipos com um único `CosmosResponse` tipo.
* Renomeado `CosmosResponseDiagnostics` para `CosmosDiagnostics` .  
* Embrulhado `FeedResponseDiagnostics` em `CosmosDiagnostics` . 
* Removido `jackson` a dependência do azure-cosmos & confiando no núcleo azul. 
* Substituído por `CosmosKeyCredential` `AzureKeyCredential` tipo. 
* `ProxyOptions`AdI adicionado a `GatewayConnectionConfig` . 
* SDK atualizado para usar `Instant` o tipo em vez de `OffsetDateTime` . 
* Adicionado novo tipo `OperationKind` enum. 
* Renomeado `FeedOptions` para `QueryRequestOptions` . 
* Adicionou `getETag()`  &  `getTimestamp()` APIs aos `Cosmos*Properties` tipos. 
* Informação `userAgent` adicionada em `CosmosException`  &  `CosmosDiagnostics` . 
* Novo personagem de linha atualizado `Diagnostics` para o personagem da nova linha do sistema. 
* APIs `readAll*` removidos, use consulta selecione todas as APIs em vez disso.
* `ChangeFeedProcessor`API de intervalo de estimativa adicionada.   
* Adicionado suporte de provisão de autoescala/piloto automático à SDK.  
* Substituído por `ConnectionPolicy` novas configurações de ligação. ApIs expostos `DirectConnectionConfig`  &  `GatewayConnectionConfig` através `CosmosClientBuilder` de configurações de ligação do modo Gateway de & direta.
* `JsonSerializable`  &  `Resource` Mudou-se para o pacote de implementação. 
* Adicionou `contentResponseOnWriteEnabled` API à CosmosClientBuilder que desativa o conteúdo de resposta completo em operações de escrita.
* APIs expostos `getETag()` em tipos de resposta.
* Mudou-se `CosmosAuthorizationTokenResolver` para a implementação. 
* Renomeado `preferredLocations`  &  `multipleWriteLocations` API para `preferredRegions`  &  `multipleWriteRegions` . 
* Atualizado `reactor-core` para 3.3.5.RELEASE, `reactor-netty` para 0.9.7.RELEASE & `netty` para 4.1.49.Versão final. 
* Apoio adicional `analyticalStoreTimeToLive` em SDK.     
* `CosmosClientException`estende-se `AzureException` . 
* APIs `maxItemCount`  &  `requestContinuationToken` removidos `FeedOptions` de, em vez disso, utilizar `byPage()` APIs de `CosmosPagedFlux`  &  `CosmosPagedIterable` .
* Introduzido `CosmosPermissionProperties` na superfície pública para `Permission` apis.
* Tipo removido `SqlParameterList` & substituído por`List`
* Corrigiu várias fugas de memória no cliente TCP direto. 
* Apoio adicional `DISTINCT` a consultas. 
* Eliminadas dependências externas em `fasterxml.uuid, guava, commons-io, commons-collection4, commons-text` .  
* Mudou-se `CosmosPagedFlux`  &  `CosmosPagedIterable` para o `utils` pacote. 
* Rede atualizada para 4.1.45.Reator final & projeto para a versão 3.3.3.
* Atualizado contratos de descanso público para `Final` as aulas.
* Apoio adicional para diagnósticos avançados para operações pontuais.
* Pacote atualizado para`com.azure.cosmos`
* `models`Pacote adicionado para contratos de modelo/descanso
* `utils`Pacote adicionado para `CosmosPagedFlux`  &  `CosmosPagedIterable` tipos. 
* APIs públicos atualizados para usar `Duration` em todo o SDK.
* Adicionei todos os contratos de descanso ao `models` pacote.
* `RetryOptions`renomeado para `ThrottlingRetryOptions` .
* Adicionados `CosmosPagedFlux`  &  `CosmosPagedIterable` tipos de paginação para APIs de consulta. 
* Apoio adicional para a partilha de TransportesClient em várias instâncias de CosmosClients usando uma nova API no`CosmosClientBuilder#connectionSharingAcrossClientsEnabled(true)`
* Otimização de consultas removendo serialização dupla/deserialização. 
* Otimizações de cabeçalhos de resposta removendo cópias desnecessárias para trás e para a frente. 
* Serialização `ByteBuffer` otimizada/deserialização removendo as instantâneas de cordas intermédias.
#### <a name="key-bug-fixes"></a>Correções de erros de chaves
* Ligação FixaPolítica `toString()` Exceção de ponteiro nulo.
* Problema fixo com análise de resultados de consulta em caso de ordem de valor por consultas. 
* Problemas de fuga de tomadas fixos com o cliente TCP direto.
* Fixado `orderByQuery` com incevejo de continuação.
* `ChangeFeedProcessor`correção de insetos para manusear divisórias divide-se & quando a partição não é encontrada.
* `ChangeFeedProcessor`correção de erros ao sincronizar atualizações de locação em diferentes linhas.
* Condição de corrida fixa que causa `ArrayIndexOutOfBound` exceção no StoreReader

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Ver também
Para saber mais sobre cosmos DB, consulte a página de serviço [do Microsoft Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/)
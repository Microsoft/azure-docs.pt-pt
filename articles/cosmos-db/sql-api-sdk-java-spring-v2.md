---
title: Dados de primavera Azure Cosmos DB v2 para notas e recursos de lançamento da SQL API
description: Saiba mais sobre os dados de primavera Azure Cosmos DB v2 para API SQL, incluindo datas de lançamento, datas de aposentadoria e alterações feitas entre cada versão do Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: d5c85095e767c0e92c22410054ac4f8fc5267660
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097146"
---
# <a name="spring-data-azure-cosmos-db-v2-for-core-sql-api-release-notes-and-resources"></a>Dados de primavera Azure Cosmos DB v2 para Core (SQL) API: Notas de lançamento e recursos
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [SDK .NET v3](sql-api-sdk-dotnet-standard.md)
> * [SDK .NET v2](sql-api-sdk-dotnet.md)
> * [SDK v2 de .NET Core](sql-api-sdk-dotnet-core.md)
> * [SDK Feed de Alterações .NET v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [SDK v4 de Java](sql-api-sdk-java-v4.md)
> * [SDK v2 Java assíncrono](sql-api-sdk-async-java.md)
> * [SDK v2 Java síncrono](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark Connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Fornecedor de Recursos REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Executor em massa - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor em massa - Java](sql-api-sdk-bulk-executor-java.md)

 Dados de primavera Azure Cosmos DB versão 2 para Core (SQL) permite que os desenvolvedores utilizem Azure Cosmos DB em aplicações primavera. A Spring Data Azure Cosmos DB expõe a interface de Dados da primavera para manipular bases de dados e coleções, trabalhar com documentos e emitir consultas. As APIs de Sincronização e Async (Reativa) são suportadas no mesmo artefacto Maven. 

O [Quadro primavera](https://spring.io/projects/spring-framework) é um modelo de programação e configuração que dinamiza o desenvolvimento de aplicações java. A mola simplifica a "canalização" das aplicações utilizando a injeção de dependência. Muitos desenvolvedores como a primavera porque torna as aplicações de construção e teste mais simples. [O Boot spring](https://spring.io/projects/spring-boot) estende este manuseamento da canalização com um olho para aplicação web e desenvolvimento de microserviços. [O Spring Data](https://spring.io/projects/spring-data) é um modelo de programação para aceder a datas como a Azure Cosmos DB a partir do contexto de uma aplicação Spring ou Spring Boot. 

Pode utilizar o Spring Data Azure Cosmos DB nas suas aplicações [Azure Spring Cloud.](https://azure.microsoft.com/services/spring-cloud/)

> [!IMPORTANT]  
> Estas notas de lançamento são para a versão 2 da Spring Data Azure Cosmos DB. Pode encontrar [aqui as notas de lançamento para a versão 3](sql-api-sdk-java-spring-v3.md). 
>
> Dados de primavera Azure Cosmos DB suporta apenas o SQL API.
>
> Consulte os seguintes artigos para obter informações sobre dados da primavera sobre outras APIs Azure Cosmos DB:
> * [Dados da primavera para Apache Cassandra com Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Dados de primavera MongoDB com Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin com Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> Quer ir rápido?
> 1. Instale o [tempo de execução java suportado mínimo, JDK 8,](/java/azure/jdk/?view=azure-java-stable&preserve-view=true)para que possa utilizar o SDK.
> 2. Crie uma aplicação DB de Dados de primavera Azure Cosmos utilizando o [arranque](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db). É fácil!
> 3. Trabalhe através do [guia de desenvolvedores da Spring Data Azure Cosmos DB,](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb)que percorre os pedidos básicos de DB do Azure Cosmos.
>
> Você pode girar as aplicações de Arranque de Arranque de Mola rapidamente usando [Spring Initializr](https://start.spring.io/)!
>

## <a name="resources"></a>Recursos

| Recurso | Ligação |
|---|---|
| **Transferência de SDK** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**Documentação da API** | [Documentação de referência DB de Dados de primavera Azure Cosmos]() |
|**Contribuir para o SDK** | [Dados de primavera Azure Cosmos DB repo no GitHub](https://github.com/microsoft/spring-data-cosmosdb) | 
|**Arranque de botas de primavera**| [Biblioteca de clientes Azure Cosmos DB Spring Boot Starter para Java](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Amostra de aplicativo Spring TODO com Azure Cosmos DB**| [Experiência Java de ponta a ponta no Serviço de Aplicações Linux (Parte 2)](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**Guia do desenvolvedor** | [Guia do programador do Azure Cosmos DB do Spring Data](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**Usando o arranque** | [Como utilizar o Arranque de Botas de primavera com o API API AZure Cosmos DB SQL](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [GitHub repo para Azure Cosmos DB Spring Boot Starter](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Amostra com Serviço de Aplicações Azure** | [Como utilizar o Spring e o Cosmos DB com o Serviço de Aplicações no Linux](/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [Amostra de aplicativos TODO](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>História do lançamento

### <a name="230-may-21-2020"></a>2.3.0 (21 de maio de 2020)
#### <a name="new-features"></a>Novas funcionalidades
* Atualiza a versão Do Boot de primavera para 2.3.0.


### <a name="225-may-19-2020"></a>2.2.5 (19 de maio de 2020)
#### <a name="new-features"></a>Novas funcionalidades
* Atualiza a versão DB do Azure Cosmos para 3.7.3.
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Contém correções de fugas de memória e atualizações da versão Netty da Azure Cosmos DB SDK 3.7.3.

### <a name="224-april-6-2020"></a>2.2.4 (6 de abril de 2020)
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Fixa `allowTelemetry` bandeira a ter em conta a partir de `CosmosDbConfig` .
* Corrige `TTL` a propriedade no contentor.

### <a name="223-february-25-2020"></a>2.2.3 (25 de fevereiro de 2020)
#### <a name="new-features"></a>Novas funcionalidades
* Adiciona novidade `findAll` por chave de partição API.
* Atualiza a versão DB do Azure Cosmos para 3.7.0.
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Correções `collectionName`  ->  `containerName` .
* Correções `entityClass` e `domainClass`  ->  `domainType` .
* Corrige "Recolha de entidade de retorno guardada por repositório em vez de entidade de entrada."

### <a name="2110-february-25-2020"></a>2.1.10 (25 de fevereiro de 2020)
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Correção de backports para "Recolha de entidade de retorno guardada por repositório em vez de entidade de entrada."

### <a name="222-january-15-2020"></a>2.2.2 (15 de janeiro de 2020)
#### <a name="new-features"></a>Novas funcionalidades
* Atualiza a versão DB do Azure Cosmos para 3.6.0.
#### <a name="key-bug-fixes"></a>Correções de erros chave

### <a name="221-december-31-2019"></a>2.2.1 (31 de dezembro de 2019)
#### <a name="new-features"></a>Novas funcionalidades
* Atualiza a versão Azure Cosmos DB SDK para 3.5.0.
* Adiciona campo de anotação para permitir ou desativar a criação automática de recolha.
* Melhora o manuseamento de exceções. Expõe `CosmosClientException` através `CosmosDBAccessException` de .
* Expõe `requestCharge` e `activityId` `ResponseDiagnostics` através.
#### <a name="key-bug-fixes"></a>Correções de erros chave
* SDK 3.5.0 atualizações "Exceção quando o cabeçalho de resposta Cosmos DB HTTP é maior que 8192 bytes", "ConsistênciaPolicy.defaultConsistencyLevel() falha na estagnação limitada e prefixo consistente."
* Corrige o `findById` comportamento do método. Anteriormente, este método voltava vazio se a entidade não fosse encontrada em vez de lançar uma exceção.
* Corrige um bug em que a triagem não foi aplicada na página seguinte quando `CosmosPageRequest` foi usada.

### <a name="219-december-26-2019"></a>2.1.9 (26 de dezembro de 2019)
#### <a name="new-features"></a>Novas funcionalidades
* Adiciona campo de anotação para permitir ou desativar a criação automática de recolha.
#### <a name="key-bug-fixes"></a>Correções de erros chave
*  Corrige o `findById` comportamento do método. Anteriormente, este método voltava vazio se a entidade não fosse encontrada em vez de lançar uma exceção.

### <a name="220-october-21-2019"></a>2.2.0 (21 de outubro de 2019)
#### <a name="new-features"></a>Novas funcionalidades
* Suporte completo do Repositório de Cosmos Reativo.
* Suporte de métricas de diagnóstico e métricas de diagnóstico Azure Cosmos.
* A atualização da versão Azure Cosmos DB SDK para 3.3.1.
* Atualização da versão Do Quadro de Mola para 5.2.0.RELEASE.
* Atualização da versão da primavera Data Commons para 2.2.0.RELEASE.
* Adds `findByIdAndPartitionKey` e `deleteByIdAndPartitionKey` APIs.
* Remove a dependência do azure-documentdb.
* Rebrands DocumentDB para Azure Cosmos DB.
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Corrige "Ordenar lança exceção quando a páginaSize é inferior ao total de itens no repositório."

### <a name="218-october-18-2019"></a>2.1.8 (18 de outubro de 2019)
#### <a name="new-features"></a>Novas funcionalidades
* Deprecia apis documentdb.
* Adds `findByIdAndPartitionKey` e `deleteByIdAndPartitionKey` APIs.
* Adiciona bloqueio otimista com base em `_etag` .
* Permite a expressão SpEL para o nome da recolha de documentos.
* Adiciona `ObjectMapper` melhorias.

### <a name="217-october-18-2019"></a>2.1.7 (18 de outubro de 2019)
#### <a name="new-features"></a>Novas funcionalidades
* Adiciona a dependência da versão 3 da Azure Cosmos DB SDK.
* Adiciona repositório de Cosmos Reativo.
* Atualiza a implementação para `DocumentDbTemplate` usar a versão 3 do Azure Cosmos DB SDK.
* Adiciona outras alterações de configuração para suporte do Repositório de Cosmos Reative.

### <a name="212-march-19-2019"></a>2.1.2 (19 de março de 2019)
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Remove `applicationInsights` a dependência para:
    * Risco potencial de dependências poluentes.
    * Incompatibilidade de Java 11.
    * Evitando o impacto potencial do desempenho no CPU e/ou na memória.

### <a name="207-march-20-2019"></a>2.0.7 (20 de março de 2019)
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Backport remove `applicationInsights` a dependência para:
    * Risco potencial de dependências poluentes.
    * Incompatibilidade de Java 11.
    * Evitando o impacto potencial do desempenho no CPU e/ou na memória.

### <a name="211-march-7-2019"></a>2.1.1 (7 de março de 2019)
#### <a name="new-features"></a>Novas funcionalidades
* Atualiza a versão principal para 2.1.1.

### <a name="206-march-7-2019"></a>2.0.6 (7 de março de 2019)
#### <a name="new-features"></a>Novas funcionalidades
* Ignore todas as exceções da telemetria.

### <a name="210-december-17-2018"></a>2.1.0 (17 de dezembro de 2018)
#### <a name="new-features"></a>Novas funcionalidades
* Atualiza versão para 2.1.0 para resolver problemas.

### <a name="205-september-13-2018"></a>2.0.5 (13 de setembro de 2018)
#### <a name="new-features"></a>Novas funcionalidades
* Adiciona palavras-chave `exists` e `startsWith` .
* Atualizações Readme.
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Corrige "Não se pode chamar auto-href diretamente para entidade."
* Corrige "FindAll falhará se a recolha não for criada."

### <a name="204-prerelease-august-23-2018"></a>2.0.4 (Pré-relanço) (23 de agosto de 2018)
#### <a name="new-features"></a>Novas funcionalidades
* Renomeia pacote de documentdb para cosmosdb.
* Adiciona nova característica da palavra-chave do método de consulta. 16 palavras-chave da SQL API já são suportadas.
* Adiciona uma nova funcionalidade de consulta com paging e triagem.
* Simplifica a configuração de dados-dados-cosmosdb.
* Adds `deleteCollection` e `deleteAll` APIs.

#### <a name="key-bug-fixes"></a>Correções de erros chave
* Correção de insetos e mitigação de defeitos.

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [a Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Saiba mais sobre o [Quadro da primavera.](https://spring.io/projects/spring-framework)

Saiba mais sobre [a Bota de primavera.](https://spring.io/projects/spring-boot)

Saiba mais sobre [os Dados da primavera.](https://spring.io/projects/spring-data)
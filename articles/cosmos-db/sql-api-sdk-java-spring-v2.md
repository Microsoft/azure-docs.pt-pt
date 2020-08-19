---
title: Dados de primavera Azure Cosmos DB v2 para notas e recursos de lançamento da SQL API
description: Saiba tudo sobre os dados de primavera Azure Cosmos DB v2 para API SQL incluindo datas de lançamento, datas de aposentadoria e alterações feitas entre cada versão do Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 47d9a8ff884e29dc5692c97d5e7867a856d01063
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590746"
---
# <a name="spring-data-azure-cosmos-db-v2-for-core-sql-api-release-notes-and-resources"></a>Dados de primavera Azure Cosmos DB v2 para Core (SQL) API: notas de lançamento e recursos
> [!div class="op_single_selector"]
> * [SDK .NET v3](sql-api-sdk-dotnet-standard.md)
> * [SDK .NET v2](sql-api-sdk-dotnet.md)
> * [SDK v2 de .NET Core](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [SDK v4 de Java](sql-api-sdk-java-v4.md)
> * [SDK v2 Java assíncrono](sql-api-sdk-async-java.md)
> * [SDK v2 Java síncrono](sql-api-sdk-java.md)
> * [Dados da primavera v2](sql-api-sdk-java-spring-v2.md)
> * [Dados da primavera v3](sql-api-sdk-java-spring-v3.md)
> * [Conector de faíscas](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Fornecedor de Recursos REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor a granel - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor a granel - Java](sql-api-sdk-bulk-executor-java.md)

O Spring Data Azure Cosmos DB v2 para Core (SQL) permite que os desenvolvedores utilizem Azure Cosmos DB em aplicações primavera. A Spring Data Azure Cosmos DB expõe a interface de Dados da primavera para manipular bases de dados e coleções, trabalhar com documentos e emitir consultas. As APIs de Sincronização e Async (Reativa) são suportadas no mesmo artefacto Maven. 

O [quadro primavera](https://spring.io/projects/spring-framework) é um modelo de programação e configuração que dinamiza o desenvolvimento de aplicações java. Para citar o site da organização, a primavera dinamiza a "canalização" das aplicações utilizando a injeção de dependência. Muitos desenvolvedores como a primavera porque as aplicações de construção e teste tornam-se mais simples. [O Spring Boot](https://spring.io/projects/spring-boot) estende esta ideia de manusear a canalização com um olho para a aplicação web e desenvolvimento de microserviços. [O Spring Data](https://spring.io/projects/spring-data) é um modelo de programação para aceder a datastores como a Azure Cosmos DB a partir do contexto de uma aplicação Spring ou Spring Boot. 

Pode utilizar o Spring Data Azure Cosmos DB nas suas aplicações [Azure Spring Cloud.](https://azure.microsoft.com/services/spring-cloud/)

> [!IMPORTANT]  
> Estas notas de lançamento são para v2 de Spring Data Azure Cosmos DB. Pode encontrar [aqui](sql-api-sdk-java-spring-v3.md)as notas de lançamento v3. 
>
> Dados de primavera Azure Cosmos DB suporta apenas API SQL.
>
> Os seguintes guias suportam dados de primavera em outras APIs Azure Cosmos DB:
> * [Dados da primavera para Apache Cassandra com Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Dados de primavera MongoDB com Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin com Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> Quer ir rápido?
> 1. Instale o [tempo de execução java suportado mínimo, JDK 8](/java/azure/jdk/?view=azure-java-stable) para que possa utilizar o SDK.
> 2. Crie uma aplicação DB de Dados de primavera Azure Cosmos utilizando o arranque - [é fácil](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)!
> 3. Trabalhe através do Guia de [Desenvolvedores DB do Spring Data Azure Cosmos](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) que percorre os pedidos básicos de DB do Azure Cosmos.
>
> Você pode girar as aplicações de Arranque de Arranque de primavera rapidamente com [o Starter primavera!](https://start.spring.io/)
>

## <a name="helpful-content"></a>Conteúdo útil

| Conteúdo | Ligação |
|---|---|
| **Download SDK** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**Documentação da API** | [Documentação de referência DB de Dados de primavera Azure Cosmos]() |
|**Contribuir para a SDK** | [Dados da primavera Azure Cosmos DB Repo no GitHub](https://github.com/microsoft/spring-data-cosmosdb) | 
|**Arranque de bota de primavera**| [Biblioteca de clientes Azure Cosmos DB Spring Boot Starter para Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**Amostra de aplicativo Spring TODO com Azure Cosmos DB**| [Experiência Java de ponta a ponta no Serviço de Aplicações Linux (Parte 2)](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**Guia de desenvolvedores** | [Guia de desenvolvedores de DB de dados de primavera Azure Cosmos](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**Guia para usar arranque** | [Como utilizar o Spring Boot Starter com a API de SQL do Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [GitHub repo para Azure Spring Boot Starter Cosmos DB](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**Amostra com Serviços de Aplicações** | [Como utilizar o Spring e o Cosmos DB com o Serviço de Aplicações no Linux](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [Amostra de aplicativos TODO](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>História do lançamento

### <a name="230-2020-05-21"></a>2.3.0 (2020-05-21)
#### <a name="new-features"></a>Novas funcionalidades
* Atualizar versão de arranque de mola para 2.3.0 
#### <a name="key-bug-fixes"></a>Correções de erros chave

### <a name="225-2020-05-19"></a>2.2.5 (2020-05-19)
#### <a name="new-features"></a>Novas funcionalidades
* Versão DB Azure Cosmos atualizada para v3.7.3
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Contém correções de fugas de memória e atualizações de versão netty da Cosmos SDK v3.7.3 

### <a name="224-2020-04-06"></a>2.2.4 (2020-04-06)
#### <a name="new-features"></a>Novas funcionalidades
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Autorizo a partir da CosmosDbConfig
* Propriedade TTL fixa no recipiente

### <a name="223-2020-02-25"></a>2.2.3 (2020-02-25)
#### <a name="new-features"></a>Novas funcionalidades
* Adicionado novo findAll by partition key API
* Versão azure-cosmos atualizada para 3.7.0
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Coleção fixaName -> contentorName
* Entidade fixaClass & domínios Classe ->Type
* "Recolha de entidade de retorno guardada por repositório em vez de entidade de entrada"

### <a name="2110-2020-02-25"></a>2.1.10 (2020-02-25)
#### <a name="new-features"></a>Novas funcionalidades
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Correção ressarcada para "Recolha de entidade de retorno guardada por repositório em vez de entidade de entrada"

### <a name="222-2020-01-15"></a>2.2.2 (2020-01-15)
#### <a name="new-features"></a>Novas funcionalidades
* Versão azure-cosmos atualizada para v3.6.0
#### <a name="key-bug-fixes"></a>Correções de erros chave

### <a name="221-2019-12-31"></a>2.2.1 (2019-12-31)
#### <a name="new-features"></a>Novas funcionalidades
* Versão atualizada do Cosmos DDK para 3.5.0
* Campo de anotação adicionado para permitir/desativar a recolha automática
* Melhor tratamento de exceção, cosmosClientExcepção exposta através da CosmosDBAccessException
* Pedido expostoAcar e atividadeId através deDiagnósticos de Resposta
#### <a name="key-bug-fixes"></a>Correções de erros chave
* SDK 3.5.0 atualização corrige "Exceção quando o cabeçalho de resposta Cosmos DB HTTP é maior que 8192 bytes", "ConsistênciaPolicy.defaultConsistencyLevel() falha na estagnação limitada e prefixo consistente"
* Comportamento fixo de IDP Desvend, retorno vazio em não encontrado, em vez de lançar exceção
* Corrigiu um bug onde a triagem não foi aplicada na página seguinte quando se utiliza o CosmosPageRequest

### <a name="219-2019-12-26"></a>2.1.9 (2019-12-26)
#### <a name="new-features"></a>Novas funcionalidades
* Campo de anotação adicionado para permitir/desativar a recolha automática
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Comportamento fixo de IDP Desvend, retorno vazio em não encontrado, em vez de lançar exceção

### <a name="220-2019-10-21"></a>2.2.0 (2019-10-21)
#### <a name="new-features"></a>Novas funcionalidades
* Suporte reativo completo do repositório cosmos
* Cosmos DB Solicitar Suporte de Métricas de Diagnóstico e Consulta
* Atualização da versão Cosmos DDK para 3.3.1
* Atualização da versão do Quadro da primavera para 5.2.0.RELEASE
* Atualização da versão da primavera Data Commons para 2.2.0.RELEASE
* Adicionado findByIdAndPartitionKey, deleteByIdAndPartitionKey APIs
* Removida dependência do azure-doumentdb
* Documentdb rebranded para Cosmos
#### <a name="key-bug-fixes"></a>Correções de erros chave
* "Classificação fixa lança exceção quando pageSize é inferior ao total de itens no repositório"

### <a name="218-2019-10-18"></a>2.1.8 (2019-10-18)
#### <a name="new-features"></a>Novas funcionalidades
* Depreciar documento DB APIs
* Adicionado findByIdAndPartitionKey, deleteByIdAndPartitionKey APIs.
* Bloqueio otimista adicionado baseado em _etag
* Expressão SPeL ativada para nome de coleção de documentos
* Melhorias objectMapper.
#### <a name="key-bug-fixes"></a>Correções de erros chave

### <a name="217-2019-10-18"></a>2.1.7 (2019-10-18)
#### <a name="new-features"></a>Novas funcionalidades
* Dependência de Cosmos SDK v3
* Repositório de Cosmos Reativo adicionado
* Implementação atualizada do DocumentDbTemplate para utilizar o Cosmos SDK v3.
* Outras alterações de configuração para suporte do Repositório de Cosmos Reative.
#### <a name="key-bug-fixes"></a>Correções de erros chave

### <a name="212-2019-03-19"></a>2.1.2 (2019-03-19)
#### <a name="new-features"></a>Novas funcionalidades
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Remover aplicaçãoInsights dependência para
    * Risco potencial de dependências poluentes
    * Incompatibilidade de Java 11
    * Evitando o impacto potencial do desempenho no CPU e/ou na memória.

### <a name="207-2019-03-20"></a>2.0.7 (2019-03-20)
#### <a name="new-features"></a>Novas funcionalidades
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Backport remove aplicaçãoInsights dependência para
    * Risco potencial de dependências poluentes
    * Incompatibilidade de Java 11
    * Evitando o impacto potencial do desempenho no CPU e/ou na memória.

### <a name="211-2019-03-07"></a>2.1.1 (2019-03-07)
#### <a name="new-features"></a>Novas funcionalidades
* Atualizar a versão principal para 2.1.1
#### <a name="key-bug-fixes"></a>Correções de erros chave

### <a name="206-2019-03-07"></a>2.0.6 (2019-03-07)
#### <a name="new-features"></a>Novas funcionalidades
* Ignore todas as exceções da telemetria
#### <a name="key-bug-fixes"></a>Correções de erros chave

### <a name="210-2018-12-17"></a>2.1.0 (2018-12-17)
#### <a name="new-features"></a>Novas funcionalidades
* Atualizar versão para 2.1.0 para resolver o problema
#### <a name="key-bug-fixes"></a>Correções de erros chave

### <a name="205-2018-09-13"></a>2.0.5 (2018-09-13)
#### <a name="new-features"></a>Novas funcionalidades
* Adicionar palavra-chave existe, começaCom
* Ler atualização
#### <a name="key-bug-fixes"></a>Correções de erros chave
* Corrigir "Cant call self href diretamente para Entidade"
* Corrigir "findAll falhará se a recolha não for criada"

### <a name="204-pre-release-2018-08-23"></a>2.0.4 (Pré-lançamento) (2018-08-23)
#### <a name="new-features"></a>Novas funcionalidades
* Rebatizando pacote de documentdb para cosmosdb,
* Adicione nova funcionalidade de palavra-chave do método de consulta, 16 palavras-chave suportadas pela Sql API.
* Adicione uma nova funcionalidade de consulta com paging e triagem.
* Simplificar a configuração de dados-dados-cosmosdb.
* Adicione eliminarCollection e eliminar Toda a API.

#### <a name="key-bug-fixes"></a>Correções de erros chave
* Correção de erros e melhoramento de defeito.

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre cosmos DB, consulte a página de serviço [do Microsoft Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/)

Para saber mais sobre o quadro da primavera, consulte a página inicial do [projeto.](https://spring.io/projects/spring-framework)

Para saber mais sobre a Bota de primavera, consulte a página inicial do [projeto.](https://spring.io/projects/spring-boot)

Para saber mais sobre os Dados da primavera, consulte a página inicial do [projeto.](https://spring.io/projects/spring-data)
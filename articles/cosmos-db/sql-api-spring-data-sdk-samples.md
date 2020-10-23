---
title: 'Azure Cosmos DB SQL API: Dados de primavera v3 exemplos'
description: Encontre dados de molas v3 exemplos no GitHub para tarefas comuns usando o API API AZure Cosmos DB SQL, incluindo operações CRUD.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 09/23/2020
ms.custom: devx-track-java
ms.author: anfeldma
ms.openlocfilehash: aaa9a78342cf64461d85d7542c7ed8a69186906b
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92275115"
---
# <a name="azure-cosmos-db-sql-api-spring-data-azure-cosmos-db-v3-examples"></a>Azure Cosmos DB SQL API: Dados de primavera Azure Cosmos DB v3 exemplos

> [!div class="op_single_selector"]
> * [.NET V2 SDK Exemplos](sql-api-dotnet-samples.md)
> * [.NET V3 SDK Exemplos](sql-api-dotnet-v3sdk-samples.md)
> * [Exemplos java V4 SDK](sql-api-java-sdk-samples.md)
> * [Dados de primavera V3 Exemplos SDK](sql-api-spring-data-sdk-samples.md)
> * [Exemplos de Node.js](sql-api-nodejs-samples.md)
> * [Exemplos de Python](sql-api-python-samples.md)
> * [Galeria de Exemplo de Código do Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

> [!IMPORTANT]  
> Estas notas de lançamento são para a versão 3 da Spring Data Azure Cosmos DB. Pode encontrar [aqui as notas de lançamento para a versão 2](sql-api-sdk-java-spring-v2.md). 
>
> Dados de primavera Azure Cosmos DB suporta apenas o SQL API.
>
> Consulte estes artigos para obter informações sobre dados da primavera sobre outras APIs Azure Cosmos DB:
> * [Dados da primavera para Apache Cassandra com Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Dados de primavera MongoDB com Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin com Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

> [!IMPORTANT]  
>[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
>  
>- Pode [ativar os benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): a sua subscrição do Visual Studio dá-lhe créditos todos os meses que pode utilizar em serviços pagos do Azure.
>
>[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]
>

As aplicações de amostra mais recentes que realizam operações CRUD e outras operações comuns em recursos DB Azure Cosmos estão incluídas no [repositório azure-spring-data-cosmos-java-sql-api-samples](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples) GitHub. Este artigo fornece:

* Ligações às tarefas em cada um dos ficheiros de projetos de dados de primavera Azure Cosmos DB. 
* Ligações para o conteúdo de referência da API relacionada.

**Pré-requisitos**

Precisa do seguinte apra executar esta aplicação de exemplo:

* Java Development Kit 8
* Dados da primavera Azure Cosmos DB v3

Você pode opcionalmente usar Maven para obter os mais recentes binários DB dB v3 da Spring Data Para uso no seu projeto. O Maven adiciona automaticamente quaisquer dependências necessárias. Caso contrário, pode baixar diretamente as dependências listadas no ficheiro **pom.xml** e adicioná-las ao seu caminho de construção.

```bash
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-cosmos</artifactId>
    <version>LATEST</version>
</dependency>
```

**Executar os exemplos de aplicações**

Clonar o repositório de exemplo:
```bash
$ git clone https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples

$ cd azure-spring-data-cosmos-java-sql-api-samples
```

Pode executar as amostras utilizando um IDE (Eclipse, IntelliJ ou VSCODE) ou a partir da linha de comando utilizando o Maven.

Em **aplicação.propriedades** estas variáveis ambientais devem ser definidas

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

a fim de dar às amostras acesso de leitura/escrita à sua conta, bases de dados e contentores.

O seu IDE pode fornecer a capacidade de executar o código de amostra de dados de mola. Caso contrário, poderá utilizar o seguinte comando terminal para executar a amostra:

```bash
mvn spring-boot:run
```

## <a name="document-crud-examples"></a>Documentos EXEMPLOS CRUD
O ficheiro [de amostras](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) mostra como executar as seguintes tarefas. Para saber mais sobre os documentos da Azure Cosmos antes de executar as [seguintes amostras, consulte Trabalhar com bases de dados, contentores e artigos conceptuais.](account-databases-containers-items.md)

| Tarefa | Referência da API |
| --- | --- |
| [Criar um documento](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L46-L47) | CosmosRepository.save |
| [Leia um documento por ID](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L56-L58) | CosmosRepository.derivadoQueryMethod |
| [Apagar todos os documentos](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L39-L41) | CosmosRepository.deleteTom |

## <a name="derived-query-method-examples"></a>Exemplos de métodos de consulta derivados
O ficheiro [de amostras](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) mostra como executar as seguintes tarefas. Para saber mais sobre as consultas de Azure Cosmos DB antes de executar as seguintes amostras, você pode achar útil ler [os Métodos de Consulta Derivada de Baeldung no](https://www.baeldung.com/spring-data-derived-queries) artigo da primavera.

| [Consulta de documentos](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L73-L77) CosmosRepository.derivedQueryMethod

## <a name="custom-query-examples"></a>Exemplos de consulta personalizada
O ficheiro [de amostras](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) mostra como fazer as seguintes tarefas utilizando a gramática de consulta SQL. Para saber mais sobre a referência de consulta SQL em Azure Cosmos DB antes de executar as [seguintes amostras, consulte exemplos de consulta SQL para Azure Cosmos DB](how-to-sql-query.md). 


| Tarefa | Referência da API |
| --- | --- |
| [Consultar todos os documentos](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L20-L22) | @Query anotação |
| [Consultar igualdade com ==](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L24-L26) | @Query anotação |
| [Consultar desigualdade com != e NOT](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L28-L38) | @Query anotação |
| [Consultar com operadores de intervalo como >, <, >=, <=](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L40-L42) | @Query anotação |
| [Consultar com operadores de intervalo em relação a cadeias](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L44-L46) | @Query anotação |
| [Consulta com ORDER BY](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L48-L50) | @Query anotação |
| [Consulta com DISTINCT](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L52-L54) | @Query anotação |
| [Consulta com funções agregadas](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L56-L62) | @Query anotação |
| [Trabalhar com subdocumentos](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L64-L66) | @Query anotação |
| [Consultar com Associações dentro de documentos](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L68-L85) | @Query anotação |
| [Consulta com operadores de cordas, matemática e matrizes](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L87-L97) | @Query anotação |
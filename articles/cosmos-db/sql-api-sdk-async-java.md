---
title: 'Azure Cosmos DB: SQL Async Java API, SDK & recursos'
description: Saiba tudo sobre o SQL Async Java API e SDK incluindo datas de lançamento, datas de aposentadoria e alterações feitas entre cada versão do Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/11/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 5b656286a2958957e8518deca88ec22d7279d767
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323864"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Async Java SDK para SQL API: Lançar notas e recursos
> [!div class="op_single_selector"]
> * [SDK .NET v3](sql-api-sdk-dotnet-standard.md)
> * [SDK .NET v2](sql-api-sdk-dotnet.md)
> * [SDK v2 de .NET Core](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [SDK v4 de Java](sql-api-sdk-java-v4.md)
> * [SDK v2 Java assíncrono](sql-api-sdk-async-java.md)
> * [SDK v2 Java síncrono](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [DESCANSAR] (/repouso/api
> * [Fornecedor de Recursos REST](/azure/azure-resource-manager/management/azure-services-resource-providers)
> * [SQL](sql-api-query-reference.md)
> * [Executor a granel - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor a granel - Java](sql-api-sdk-bulk-executor-java.md)

O SQL API Async Java SDK difere do SQL API Java SDK, fornecendo operações assíncronos com o apoio da [biblioteca Netty.](https://netty.io/) O [SQL API Java SDK](sql-api-sdk-java.md) pré-existente não suporta operações assíncronos. 

> [!IMPORTANT]  
> Este *não* é o mais recente Java SDK para Azure Cosmos DB! Considere usar [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) para o seu projeto. Para atualizar, siga as instruções no [guia Migrador para Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) e o guia [Reator vs RxJava.](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) 
>

| |  |
|---|---|
| **SDK Download** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**Documentação da API** |[Documentação de referência da API de Java](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?view=azure-java-stable) | 
|**Contribuir para a SDK** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**Introdução** | [Começa com o Async Java SDK](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Exemplo de código** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **Sugestões de desempenho**| [GitHub lerme](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **Tempo mínimo suportado**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 

[!INCLUDE [Release notes](~/azure-cosmosdb-java-v2/changelog/README.md)]
## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Veja também
Para saber mais sobre cosmos DB, consulte a página de serviço [do Microsoft Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/)


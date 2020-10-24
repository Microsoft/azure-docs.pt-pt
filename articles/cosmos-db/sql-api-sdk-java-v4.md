---
title: Azure Cosmos DB Java SDK v4 para notas e recursos de lançamento da SQL API
description: Saiba tudo sobre o Azure Cosmos DB Java SDK v4 para SQL API e SDK incluindo datas de lançamento, datas de aposentadoria e alterações feitas entre cada versão do Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 2632d5c4fe637222cb6248d35671057430d935e7
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92477322"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK v4 para Core (SQL) API: notas de lançamento e recursos
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

O Azure Cosmos DB Java SDK v4 para Core (SQL) combina uma API Async e uma API sync em um artefacto Maven. O V4 SDK traz um desempenho melhorado, novas funcionalidades da API e suporte Async baseado no Project Reator e na [biblioteca Netty.](https://netty.io/) Os utilizadores podem esperar um melhor desempenho com Azure Cosmos DB Java SDK v4 contra o [Azure Cosmos DB Async Java SDK v2](sql-api-sdk-async-java.md) e o [Azure Cosmos DB Sync Java SDK v2](sql-api-sdk-java.md).

> [!IMPORTANT]  
> Estas notas de lançamento são apenas para Azure Cosmos DB Java SDK v4. Se está a utilizar uma versão mais antiga do que v4, consulte o guia [Migrador para Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) para ajudar a atualizar para v4.
>
> Aqui estão três passos para ir rápido!
> 1. Instale o [tempo de execução java suportado mínimo, JDK 8](/java/azure/jdk/?view=azure-java-stable&preserve-view=true) para que possa utilizar o SDK.
> 2. Trabalhe através do [Quickstart Guide for Azure Cosmos DB Java SDK v4](./create-sql-api-java.md) que lhe dá acesso ao artefacto Maven e percorre os pedidos básicos de Azure Cosmos DB.
> 3. Leia as dicas de [desempenho](performance-tips-java-sdk-v4-sql.md) V4 do Azure Cosmos DB Java E4 e guias [de resolução de problemas](troubleshoot-java-sdk-v4-sql.md) para otimizar o SDK para a sua aplicação.
>
> Os [workshops e laboratórios DB da Azure Cosmos](https://aka.ms/cosmosworkshop) são outro grande recurso para aprender a usar a Azure Cosmos DB Java SDK v4!
>

## <a name="helpful-content"></a>Conteúdo útil

| Conteúdo | Ligação |
|---|---|
|**Transferência de SDK**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**Documentação da API** | [Documentação de referência da API de Java](/java/api/overview/azure/cosmosdb/client?preserve-view=true&view=azure-java-stable) |
|**Contribuir para a SDK** | [Azure SDK para Java Central Repo no GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos) | 
|**Introdução** | [Quickstart: Construa uma app Java para gerir dados da Azure Cosmos DB SQL API](./create-sql-api-java.md) <br> [GitHub repo com código de arranque rápido](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**Amostras de código básico** | [Azure Cosmos DB: Exemplos Java para a API SQL](sql-api-java-sdk-samples.md) <br> [GitHub repo com código de amostra](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**App de consola com Change Feed**| [Alterar feed - Amostra java SDK v4](create-sql-api-java-changefeed.md) <br> [GitHub repo com código de amostra](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Amostra de aplicativo web**| [Construa uma aplicação web com Java SDK v4](sql-api-java-application.md) <br> [GitHub repo com código de amostra](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Sugestões de desempenho**| [Sugestões de desempenho para o SDK v4 Java](performance-tips-java-sdk-v4-sql.md)| 
| **Resolução de problemas** | [Resolver problemas do SDK v4 Java](troubleshoot-java-sdk-v4-sql.md) |
| **Migrar para v4 de um SDK mais antigo** | [Migrar para o SDK Java V4](migrate-java-v4-sdk.md) |
| **Tempo mínimo suportado**|[JDK 8](/java/azure/jdk/?view=azure-java-stable&preserve-view=true) | 
| **Workshops e laboratórios DB da Azure Cosmos** |[Página inicial dos workshops do Cosmos DB](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-cosmos/CHANGELOG.md)]

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)] 

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o Azure Cosmos DB, veja a página do serviço [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
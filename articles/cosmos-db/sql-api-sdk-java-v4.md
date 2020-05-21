---
title: Azure Cosmos DB Java SDK v4 para notas e recursos de lançamento da SQL API
description: Saiba tudo sobre o Azure Cosmos DB Java SDK v4 para SQL API e SDK, incluindo datas de lançamento, datas de aposentadoria e alterações efetuadas entre cada versão do Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/20/2020
ms.author: anfeldma
ms.openlocfilehash: b222a94ee754b24192261451d8ddc429886e705c
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725657"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK v4 para Core (SQL) API: notas de lançamento e recursos
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET Change Feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [SDK v2 Java assíncrono](sql-api-sdk-async-java.md)
> * [SDK v2 Java síncrono](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Fornecedor de Recursos REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor a granel - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor a granel - Java](sql-api-sdk-bulk-executor-java.md)

O Azure Cosmos DB Java SDK v4 para Core (SQL) combina um API Async e um Sync API em um artefacto Maven. O V4 SDK traz um desempenho melhorado, novas funcionalidades de API e suporte a Sync baseado no Project Reator e na [biblioteca Netty.](https://netty.io/) Os utilizadores podem esperar um melhor desempenho com o Azure Cosmos DB Java SDK v4 contra o [Azure Cosmos DB Async Java SDK v2](sql-api-sdk-async-java.md) e o [Azure Cosmos DB Sync Java SDK v2](sql-api-sdk-java.md).

> [!IMPORTANT]  
> Estas notas de lançamento são apenas para Azure Cosmos DB Java SDK v4. Se está a utilizar atualmente uma versão mais antiga do que a v4, consulte o guia [Migrate to Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) para ajudar a atualizar para v4.
>
> Aqui estão três passos para ir rápido!
> 1. Instale o [tempo de execução mínimo suportado por Java, JDK 8](/java/azure/jdk/?view=azure-java-stable) para que possa utilizar o SDK.
> 2. Trabalhe através do [Quickstart Guide for Azure Cosmos DB Java SDK v4](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) que lhe dá acesso ao artefacto Maven e percorre pedidos básicos de Azure Cosmos DB.
> 3. Leia as dicas de [desempenho](performance-tips-java-sdk-v4-sql.md) do Azure Cosmos DB Java SDK v4 e guias de resolução de [problemas](troubleshoot-java-sdk-v4-sql.md) para otimizar o SDK para a sua aplicação.
>
> Os [workshops e laboratórios Azure Cosmos DB](https://aka.ms/cosmosworkshop) são outro grande recurso para aprender a usar o Azure Cosmos DB Java SDK v4!
>

| |  |
|---|---|
| **SDK Download** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**Documentação da API** | [Documentação de referência da API java](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-cosmos/4.0.1-beta.3/index.html) |
|**Contribuir para o SDK** | [Azure SDK para Java Central Repo no GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos) | 
|**Começar** | [Quickstart: Construa uma app Java para gerir os dados da API Da Azure Cosmos DB SQL](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) · [GitHub repo com código de arranque rápido](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**Amostras de código básico** | [Azure Cosmos DB: Exemplos de Java para a SQL API](sql-api-java-sdk-samples.md) · [GitHub repo com código de amostra](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**App de consola com Change Feed**| [Alterar feed - Java SDK v4 amostra](create-sql-api-java-changefeed.md) · [GitHub repo com código de amostra](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Amostra de aplicativo web**| [Construa uma aplicação web com Java SDK v4](sql-api-java-application.md) · [GitHub repo com código de amostra](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Sugestões de desempenho**| [Sugestões de desempenho para o SDK v4 Java](performance-tips-java-sdk-v4-sql.md)| 
| **Resolução de problemas** | [Resolver problemas do SDK v4 Java](troubleshoot-java-sdk-v4-sql.md) |
| **Migrar para v4 de um SDK mais velho** | [Migrar para o SDK Java V4](migrate-java-v4-sdk.md) |
| **Tempo mínimo de execução suportado**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 
| **Workshops e laboratórios azure Cosmos DB** |[Cosmos DB workshops página inicial](https://aka.ms/cosmosworkshop)


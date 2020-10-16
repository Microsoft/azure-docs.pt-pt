---
title: 'Azure Cosmos DB: Executor a granel Java API, SDK & recursos'
description: Saiba tudo sobre o executor a granel Java API e SDK incluindo datas de lançamento, datas de aposentadoria e alterações feitas entre cada versão do executor a granel Azure Cosmos DB Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 72a224d4ad1807d095baa8db819878bf2e22e133
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91802723"
---
# <a name="java-bulk-executor-library-download-information"></a>Biblioteca de executor a granel java: Descarregar informações

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
> * [Spark Connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Fornecedor de Recursos REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor a granel - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor a granel - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Descrição**|A biblioteca de executor a granel permite que as aplicações do cliente realizem operações a granel nas contas DB da Azure Cosmos. biblioteca executor a granel fornece bulkImport, e bulkUpdate espaços de nome. O módulo BulkImport pode ingerir documentos em massa de forma otimizada, de modo a que a produção prevista para uma recolha seja consumida na sua máxima extensão. O módulo BulkUpdate pode atualizar em massa os dados existentes em recipientes Azure Cosmos como patches.|
|**Download SDK**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**Biblioteca de executor a granel em GitHub**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **Documentação da API**| [Documentação de referência da API de Java](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**Introdução**|[Começa com a biblioteca de executor a granel Java SDK](bulk-executor-java.md)|
|**Tempo mínimo suportado**|[Kit de Desenvolvimento de Java (JDK) 7+](/java/azure/jdk/?view=azure-java-stable&preserve-view=true)|

## <a name="release-notes"></a>Notas de versão

### <a name="2100"></a><a name="2.10.0"></a>2.10.0

* Correção para DocumentAnalyzer.java para extrair corretamente valores-chave de partição aninhada do json.

### <a name="294"></a><a name="2.9.4"></a>2.9.4

* Adicione funcionalidades em operações bulkDelete para tentar novamente em falhas específicas e também devolver uma lista de falhas ao utilizador que poderiam ser novamente julgadas.

### <a name="293"></a><a name="2.9.3"></a>2.9.3

* Atualização para a versão Cosmos SDK 2.4.7.

### <a name="292"></a><a name="2.9.2"></a>2.9.2

* Correção para 'mergeAll' para continuar no valor da chave 'id' e partição para que quaisquer propriedades de documento remendadas que sejam colocadas após 'id' e valor chave de partição sejam adicionadas à lista de item atualizada.

### <a name="291"></a><a name="2.9.1"></a>2.9.1

* Atualize o grau de concordância inicial a 1 e adicione registos de depuramento para minibatch.



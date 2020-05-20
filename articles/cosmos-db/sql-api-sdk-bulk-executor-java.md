---
title: 'Azure Cosmos DB: Executor a granel Java API, SDK & recursos'
description: Saiba tudo sobre o executor a granel Java API e SDK, incluindo datas de lançamento, datas de reforma e alterações feitas entre cada versão do executor a granel do Azure Cosmos DB Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/11/2020
ms.author: anfeldma
ms.openlocfilehash: 6fe075d2679ec13217200bc8b0da132e8f4d9b57
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83660435"
---
# <a name="java-bulk-executor-library-download-information"></a>Biblioteca executor a granel de Java: Descarregue informações

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET Change Feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [SDK v2 Java assíncrono](sql-api-sdk-async-java.md)
> * [SDK v2 Java síncrono](sql-api-sdk-java.md)
> * [Pitão](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Fornecedor de Recursos REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor a granel - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor a granel - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Descrição**|A biblioteca de executora a granel permite que as aplicações dos clientes realizem operações a granel nas contas da Azure Cosmos DB. a biblioteca de executores a granel fornece espaços de nome bulkImport e BulkUpdate. O módulo BulkImport pode ingerir a granel documentos de forma otimizada, de modo a que o serviço de entrada previsto para uma recolha seja consumido na sua extensão máxima. O módulo BulkUpdate pode atualizar em massa os dados existentes nos contentores Do Cosmos azure como patches.|
|**Download sDK**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**Biblioteca executora a granel em GitHub**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **Documentação da API**| [Documentação de referência da API java](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**Introdução**|[Começar com a biblioteca de executor esfarofoneiro Java SDK](bulk-executor-java.md)|
|**Tempo mínimo de execução suportado**|[Kit de Desenvolvimento Java (JDK) 7+](/java/azure/jdk/?view=azure-java-stable)|

## <a name="release-notes"></a>Notas de versão

### <a name="2100"></a><a name="2.10.0"/>2.10.0

* Fixe o DocumentAnalyzer.java para extrair corretamente valores-chave de divisória sinuosa do json.

### <a name="294"></a><a name="2.9.4"/>2.9.4

* Adicione funcionalidade nas operações BulkDelete para voltar a tentar falhas específicas e também devolver uma lista de falhas ao utilizador que poderiam ser novamente tentadas.

### <a name="293"></a><a name="2.9.3"/>2.9.3

* Atualização para cosmos SDK versão 2.4.7.

### <a name="292"></a><a name="2.9.2"/>2.9.2

* Correção para que o 'mergeAll' continue no valor chave 'id' e divisória de modo a que quaisquer propriedades de documentos remendadas que sejam colocadas após 'id' e divisória valor chave sejam adicionados à lista de itens atualizados.

### <a name="291"></a><a name="2.9.1"/>2.9.1

* Atualizar o grau de início da moeda a 1 e adicionar registos de depuração para minibatch.



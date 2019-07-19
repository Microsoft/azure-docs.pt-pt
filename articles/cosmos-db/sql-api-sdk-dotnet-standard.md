---
title: 'o Azure Cosmos DB: API do SQL .NET Standard, recursos de & do SDK'
description: Saiba tudo sobre a API do SQL e o SDK do .NET, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão do SDK do .NET Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 07/12/2019
ms.author: dech
ms.openlocfilehash: 01645e66907e01b2e8fcb771976b50e5bbc8abf4
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229027"
---
# <a name="azure-cosmos-db-net-standard-sdk-for-sql-api-download-and-release-notes"></a>SDK do .NET Standard do Azure Cosmos DB para a API do SQL: Download e notas de versão
> [!div class="op_single_selector"]
> * [.NET Standard](sql-api-sdk-dotnet-standard.md)
> * [.NET](sql-api-sdk-dotnet.md)
> * [Feed de alterações de .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Fornecedor de Recursos REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Transferência de SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)|
|**Documentação da API**|[Documentação de referência da .NET API](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Amostras**|[Exemplos de código do .NET](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples)|
|**Introdução**|[Introdução ao SDK do .NET Azure Cosmos DB](sql-api-get-started.md)|
|**Tutorial da aplicação Web**|[Desenvolvimento de aplicativos Web com o Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Estrutura de suporte atual**|[Microsoft .NET Standard 2,0](/dotnet/standard/net-standard)|

## <a name="release-notes"></a>Notas de versão

### <a name="a-name300300"></a><a name="3.0.0"/>3.0.0 
* Disponibilidade geral da [versão 3.0.0](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) do SDK do .net
* Destinos .NET Standard 2,0, que oferece suporte ao .NET Framework 4.6.1 + e ao .NET Core 2.0 +
* Novo modelo de objeto, com CosmosClient de nível superior e métodos divididos em classes de contêiner e banco de dados relevantes
* Novas APIs de fluxo de alto desempenho
* Suporte interno para APIs do processador do feed de alterações
* APIs do Fluent Builder para CosmosClient, contêiner e processador do feed de alterações
* APIs de gerenciamento de taxa de transferência idiomática
* Requestoptions e ResponseTypes granulares para solicitações de banco de dados, contêiner, item, consulta e taxa de transferência
* Capacidade de dimensionar contêineres não particionados 
* Serializador extensível e personalizável
* Pipeline de solicitação extensível com suporte para manipuladores personalizados


## <a name="release--retirement-dates"></a>Datas de lançamento & desativação
A Microsoft fornece notificações com pelo menos **12 meses** de antecedência para desativar um SDK a fim de suavizar a transição para uma versão mais recente/com suporte.

Novos recursos e funcionalidades e otimizações são adicionados somente ao SDK atual, por isso é recomendável que você sempre atualize para a versão mais recente do SDK o mais cedo possível. 

Todas as solicitações para Azure Cosmos DB usando um SDK desativado são rejeitadas pelo serviço.

<br/>

| Version | Data de lançamento | Data de retirada |
| --- | --- | --- |
| [3.0.0](#3.0.0) |15 de julho de 2019 |--- |

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também
Para saber mais sobre o Cosmos DB, veja [do Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) página do serviço. 


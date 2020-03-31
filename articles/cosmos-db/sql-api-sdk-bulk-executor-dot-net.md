---
title: 'Azure Cosmos DB: Executor a granel .NET API, SDK & recursos'
description: Saiba tudo sobre o executor a granel .NET API e SDK, incluindo datas de lançamento, datas de reforma e alterações efetuadas entre cada versão do executor a granel Do Azure Cosmos DB .NET SDK.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/16/2020
ms.author: ramkris
ms.openlocfilehash: 1a8040fc397b526b540ce9343baa985cab49e2b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169405"
---
# <a name="net-bulk-executor-library-download-information"></a>Biblioteca de executor a granel .NET: Descarregamento de informações 

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET Change Feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Nó.js](sql-api-sdk-node.md)
> * [Java assíncrono](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Pitão](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Fornecedor de Recursos REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor a granel - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor a granel - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **Descrição**| A biblioteca de executores a granel .Net permite que as aplicações dos clientes realizem operações a granel nas contas da Azure Cosmos DB. Esta biblioteca fornece espaços de nomeBulkImport, BulkUpdate e BulkDelete. O módulo BulkImport pode ingerir a granel documentos de forma otimizada, de modo a que o serviço de entrada previsto para uma recolha seja consumido na sua extensão máxima. O módulo BulkUpdate pode atualizar em massa os dados existentes nos contentores Do Cosmos azure como patches. O módulo BulkDelete pode eliminar em massa documentos de forma otimizada, de modo a que a entrada prevista para uma recolha seja consumida na sua máxima extensão.|
|**Download sDK**| [Nuget](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Biblioteca executora a granel em GitHub**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**Documentação da API**|[.NET Documentação de referência da API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**Começar**|[Começar com a biblioteca de executor a granel .NET SDK](bulk-executor-dot-net.md)|
| **Quadro apoiado atual**| Microsoft .NET Framework 4.5.2, 4.6.1 e .NET Standard 2.0 |

> [!NOTE]
> Se estiver a utilizar o executor a granel, consulte a versão mais recente 3.x do [.NET SDK,](tutorial-sql-api-dotnet-bulk-import.md)que tem executor a granel incorporado no SDK. 

## <a name="release-notes"></a>Notas de versão

### <a name="241-preview"></a><a name="2.4.1-preview"/>2.4.1-pré-visualização

* TotalElapsed Fixed Na resposta do BulkDelete para medir corretamente o tempo total, incluindo quaisquer repetições.

### <a name="240-preview"></a><a name="2.4.0-preview"/>2.4.0 pré-visualização

* Alteração da dependência do SDK para >= 2,5.1

### <a name="230-preview2"></a><a name="2.3.0-preview2"/>2.3.0 pré-visualização2

* Suporte adicional para executor a granel de gráfico para aceitar ttl em vértices e bordas

### <a name="220-preview2"></a><a name="2.2.0-preview2"/>2.2.0 pré-visualização2

* Corrigiu um problema, que causou exceções durante a escala elástica do Azure Cosmos DB ao funcionar no modo Gateway. Esta correção torna-a funcionalmente equivalente a 1.4.1 de slançamento.

### <a name="210-preview2"></a><a name="2.1.0-preview2"/>2.1.0 pré-visualização2

* Suporte a granelDelete adicionado para contas SQL API para aceitar chave de partição, documentar tuples id para eliminar. Esta alteração torna-a funcionalmente equivalente a 1.4.0 de lançamento.

### <a name="200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-pré-visualização2

* Incluindo o MongoBulkExecutor para suportar .NET Standard 2.0. Esta função torna-a funcionalmente equivalente à versão 1.3.0, com a adição de suporte .NET Standard 2.0 como o quadro-alvo.

### <a name="200-preview"></a><a name="2.0.0-preview"/>2.0.0-pré-visualização

* Adicionado .NET Standard 2.0 como um dos quadros-alvo suportados para fazer com que a biblioteca executor a granel funcione com aplicações .NET Core.

### <a name="188"></a><a name="1.8.8"/>1.8.8

* Corrigiu um problema no MongoBulkExecuteor que estava a aumentar inesperadamente o tamanho do documento adicionando estofos e, em alguns casos, ultrapassando o limite máximo de tamanho do documento.

### <a name="187"></a><a name="1.8.7"/>1.8.7

* Corrigiu um problema com o BulkDeleteAsync quando a Coleção tiver os caminhos-chave da divisória.

### <a name="186"></a><a name="1.8.6"/>1.8.6

* MongoBulkExecutor agora implementa IDescartável e espera-se que seja eliminado após a sua utilizada.

### <a name="185"></a><a name="1.8.5"/>1.8.5

* Bloqueio removido na versão SDK. O pacote está agora dependente de SDK >= 2,5.1.

### <a name="184"></a><a name="1.8.4"/>1.8.4

* Manuseamento fixo dos identificadores ao chamar GranelImport com uma lista de objetos POCO com valores numéricos.

### <a name="183"></a><a name="1.8.3"/>1.8.3

* TotalElapsed Fixed Na resposta do BulkDelete para medir corretamente o tempo total, incluindo quaisquer repetições.

### <a name="182"></a><a name="1.8.2"/>1.8.2

* Consumo elevado de CPU fixo em determinados cenários.
* O rastreio agora usa traceSource. Os utilizadores podem `BulkExecutorTrace` definir os ouvintes para a fonte.
* Fixou um cenário raro que poderia causar um bloqueio ao enviar documentos perto de 2Mb de tamanho.

### <a name="160"></a><a name="1.6.0"/>1.6.0

* Atualizado o executor a granel para agora usar a versão mais recente do Azure Cosmos DB .NET SDK (2.4.0)

### <a name="150"></a><a name="1.5.0"/>1.5.0

* Suporte adicional para executor a granel de gráfico para aceitar ttl em vértices e bordas

### <a name="141"></a><a name="1.4.1"/>1.4.1

* Corrigiu um problema, que causou exceções durante a escala elástica do Azure Cosmos DB ao funcionar no modo Gateway.

### <a name="140"></a><a name="1.4.0"/>1.4.0

* Suporte a granelDelete adicionado para contas SQL API para aceitar chave de partição, documentar tuples id para eliminar.

### <a name="130"></a><a name="1.3.0"/>1.3.0

* Corrigiu um problema, que causou um problema de formatação no agente utilizador utilizado pelo executor a granel.

### <a name="120"></a><a name="1.2.0"/>1.2.0

* Melhorou a importação de executores a granel e atualizou as APIs para se adaptar transparentemente à escala elástica do contentor Cosmos quando o armazenamento excede a capacidade atual sem lançar exceções.

### <a name="112"></a><a name="1.1.2"/>1.1.2

* Aumentou a dependência do DocumentoDB .NET SDK para a versão 2.1.3.

### <a name="111"></a><a name="1.1.1"/>1.1.1

* Corrigiu um problema, que fez com que o executor a granel atirasse o erro jSRT enquanto importava para cobranças fixas.

### <a name="110"></a><a name="1.1.0"/>1.1.0

* Suporte adicional para a operação BulkDelete para contas API Azure Cosmos DB SQL.
* Apoio adicional à operação BulkImport para contas com a API da Azure Cosmos DB para o MongoDB.
* Aumentou a dependência do DocumentoDB .NET SDK para a versão 2.0.0. 

### <a name="102"></a><a name="1.0.2"/>1.0.2

* Suporte adicional para a operação BulkImport para contas API Azure Cosmos DB Gremlin.

### <a name="101"></a><a name="1.0.1"/>1.0.1

* Correção de erros menores na operação BulkImport para contas API Da Azure Cosmos DB SQL.

### <a name="100"></a><a name="1.0.0"/>1.0.0

* Suporte adicional para operações bulkImport e BulkUpdate para contas API Da Azure Cosmos DB SQL.

## <a name="next-steps"></a>Passos seguintes

Para saber sobre a biblioteca java executor a granel, consulte o seguinte artigo:

[Biblioteca SDK de executor a granel de Java e divulgação de informações](sql-api-sdk-bulk-executor-java.md)

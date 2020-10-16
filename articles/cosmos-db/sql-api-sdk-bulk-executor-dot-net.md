---
title: 'Azure Cosmos DB: Executor a granel .NET API, SDK & recursos'
description: Saiba tudo sobre o executor a granel .NET API e SDK, incluindo datas de lançamento, datas de reforma e alterações efetuadas entre cada versão do executor a granel Azure Cosmos DB .NET SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.openlocfilehash: 5203223f8468a4dfa0faf2df92e826a62f7bbfcf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91802757"
---
# <a name="net-bulk-executor-library-download-information"></a>Biblioteca de executor a granel .NET: Descarregar informações 

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
| **Descrição**| A biblioteca de executor a granel .NET permite que as aplicações do cliente realizem operações a granel nas contas DB da Azure Cosmos. Esta biblioteca fornece espaços de nome BulkImport, BulkUpdate e BulkDelete. O módulo BulkImport pode ingerir documentos em massa de forma otimizada, de modo a que a produção prevista para uma recolha seja consumida na sua máxima extensão. O módulo BulkUpdate pode atualizar em massa os dados existentes em recipientes Azure Cosmos como patches. O módulo BulkDelete pode eliminar em massa documentos de forma otimizada de modo a que a produção prevista para uma recolha seja consumida na sua máxima extensão.|
|**Download SDK**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Biblioteca de executor a granel em GitHub**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**Documentação da API**|[.NET Documentação de referência da API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet&preserve-view=true)|
|**Introdução**|[Começa com a biblioteca de executor a granel .NET SDK](bulk-executor-dot-net.md)|
| **Quadro atual suportado**| Microsoft .NET Framework 4.5.2, 4.6.1 e .NET Standard 2.0 |

> [!NOTE]
> Se estiver a utilizar o executor a granel, consulte a versão mais recente 3.x do [.NET SDK](tutorial-sql-api-dotnet-bulk-import.md), que tem o executor a granel incorporado no SDK. 

## <a name="release-notes"></a>Notas de versão

### <a name="241-preview"></a><a name="2.4.1-preview"></a>2.4.1 pré-visualização

* Fixo TotalElapsedTime na resposta da BulkDelete para medir corretamente o tempo total, incluindo quaisquer retrações.

### <a name="240-preview"></a><a name="2.4.0-preview"></a>2.4.0-pré-visualização

* Alteração da dependência da SDK para >= 2,5.1

### <a name="230-preview2"></a><a name="2.3.0-preview2"></a>2.3.0-pré-visualização2

* Suporte adicionado para executor de a granel de gráfico para aceitar ttl em vértices e bordas

### <a name="220-preview2"></a><a name="2.2.0-preview2"></a>2.2.0-pré-visualização2

* Corrigiu um problema, que causou exceções durante a escala elástica da Azure Cosmos DB quando funcionava no modo Gateway. Esta correção faz com que seja funcionalmente equivalente a uma libertação de 1.4.1.

### <a name="210-preview2"></a><a name="2.1.0-preview2"></a>2.1.0-pré-visualização2

* Apoio bulkDelete adicionado para contas API SQL para aceitar chave de partição, documento ID tuples para eliminar. Esta alteração faz com que seja funcionalmente equivalente a uma libertação de 1.4.0.

### <a name="200-preview2"></a><a name="2.0.0-preview2"></a>2.0.0-pré-visualização2

* Incluindo o MongoBulkExecutor para suportar .NET Standard 2.0. Esta funcionalidade torna-o funcionalmente equivalente ao lançamento de 1.3.0, com a adição de suporte .NET Standard 2.0 como o quadro-alvo.

### <a name="200-preview"></a><a name="2.0.0-preview"></a>2.0.0-pré-visualização

* Adicionado .NET Standard 2.0 como um dos quadros-alvo suportados para fazer a biblioteca de executor a granel funcionar com aplicações .NET Core.

### <a name="189"></a><a name="1.8.9"></a>1.8.9

* Corrigiu um problema com BulkDeleteAsync quando os valores com cotações em fuga foram passados como parâmetros de entrada.

### <a name="188"></a><a name="1.8.8"></a>1.8.8

* Corrigiu um problema no MongoBulkExecutor que estava a aumentar inesperadamente o tamanho do documento adicionando estofos e, em alguns casos, ultrapassando o limite máximo de tamanho do documento.

### <a name="187"></a><a name="1.8.7"></a>1.8.7

* Corrigiu um problema com BulkDeleteAsync quando a Coleção tem caminhos-chave de partição aninhados.

### <a name="186"></a><a name="1.8.6"></a>1.8.6

* A MongoBulkExecutor implementa agora o IDisposable e espera-se que seja eliminado após o seu utilização.

### <a name="185"></a><a name="1.8.5"></a>1.8.5

* Bloqueio removido na versão SDK. O pacote está agora dependente de >SDK = 2,5.1.

### <a name="184"></a><a name="1.8.4"></a>1.8.4

* Manuseamento fixo dos identificadores ao ligar para o BulkImport com uma lista de objetos POCO com valores numéricos.

### <a name="183"></a><a name="1.8.3"></a>1.8.3

* Fixo TotalElapsedTime na resposta da BulkDelete para medir corretamente o tempo total, incluindo quaisquer retrações.

### <a name="182"></a><a name="1.8.2"></a>1.8.2

* Fixo elevado consumo de CPU em certos cenários.
* O rastreio agora usa TraceSource. Os utilizadores podem definir os ouvintes para a `BulkExecutorTrace` origem.
* Corrigiu um cenário raro que poderia causar uma fechadura ao enviar documentos perto de 2Mb de tamanho.

### <a name="160"></a><a name="1.6.0"></a>1.6.0

* Atualizou o executor a granel para utilizar agora a versão mais recente do Azure Cosmos DB .NET SDK (2.4.0)

### <a name="150"></a><a name="1.5.0"></a>1.5.0

* Suporte adicionado para executor de a granel de gráfico para aceitar ttl em vértices e bordas

### <a name="141"></a><a name="1.4.1"></a>1.4.1

* Corrigiu um problema, que causou exceções durante a escala elástica da Azure Cosmos DB quando funcionava no modo Gateway.

### <a name="140"></a><a name="1.4.0"></a>1.4.0

* Apoio bulkDelete adicionado para contas API SQL para aceitar chave de partição, documento ID tuples para eliminar.

### <a name="130"></a><a name="1.3.0"></a>1.3.0

* Corrigiu um problema, que causou um problema de formatação no agente utilizador utilizado pelo executor a granel.

### <a name="120"></a><a name="1.2.0"></a>1.2.0

* Melhorou a importação e atualização de APIs para se adaptar de forma transparente à escala elástica do contentor cosmos quando o armazenamento exceder a capacidade atual sem lançar exceções.

### <a name="112"></a><a name="1.1.2"></a>1.1.2

* Aumentou a dependência documentdb .NET SDK para a versão 2.1.3.

### <a name="111"></a><a name="1.1.1"></a>1.1.1

* Corrigiu um problema, que fez com que o executor a granel lançasse o erro do JSRT enquanto importava para cobranças fixas.

### <a name="110"></a><a name="1.1.0"></a>1.1.0

* Suporte adicional para a operação BulkDelete para contas AZure Cosmos DB SQL API.
* Apoio adicional à operação BulkImport para contas com a API da Azure Cosmos DB para a MongoDB.
* Aumentou a dependência documentdb .NET SDK para a versão 2.0.0. 

### <a name="102"></a><a name="1.0.2"></a>1.0.2

* Apoio adicional à operação BulkImport para contas Azure Cosmos DB Gremlin API.

### <a name="101"></a><a name="1.0.1"></a>1.0.1

* Correção de erros menores para a operação BulkImport para contas Azure Cosmos DB SQL API.

### <a name="100"></a><a name="1.0.0"></a>1.0.0

* Apoio adicional às operações bulkimport e BulkUpdate para as contas AZure Cosmos DL API.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a biblioteca java do executor a granel, consulte o seguinte artigo:

[Biblioteca executora a granel de Java SDK e divulgar informações](sql-api-sdk-bulk-executor-java.md)

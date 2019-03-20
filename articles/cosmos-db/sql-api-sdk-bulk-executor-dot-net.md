---
title: 'o Azure Cosmos DB: Em massa Executor .NET API, SDK e recursos'
description: Saiba tudo sobre a API de .NET de Executor em massa e o SDK, incluindo as datas de lançamento, datas de extinção e as alterações feitas entre cada versão do SDK de .NET de Executor de em massa Azure Cosmos DB.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 11/19/2018
ms.author: ramkris
ms.openlocfilehash: 4f2d8b3246901f139695998224dfe036cccb9833
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57855789"
---
# <a name="net-bulk-executor-library-download-information"></a>Biblioteca de .NET em massa Executor: Transferir informações 

> [!div class="op_single_selector"]
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
> * [Em massa Executor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Em massa Executor - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **Descrição**| A biblioteca de Executor em massa permite que os aplicativos de cliente realizar operações em massa em contas do Azure Cosmos DB. Biblioteca de Executor em massa fornece BulkImport BulkUpdate e BulkDelete espaços de nomes. O módulo pode em massa de BulkImport ingerir documentos de forma otimizada, de modo a que o débito aprovisionado para uma coleção é consumido para o respetivo limite máximo permitido. BulkUpdate módulo pode em massa atualizar os dados existentes nos contentores do Azure Cosmos DB como patches. O módulo de BulkDelete pode em massa documentos de eliminação de forma otimizada, de modo a que o débito aprovisionado para uma coleção é consumido para o respetivo limite máximo permitido.|
|**Transferência de SDK**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Biblioteca do BulkExecutor no GitHub**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**Documentação da API**|[Documentação de referência da .NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**Introdução**|[Introdução ao .NET SDK da biblioteca do Executor em massa](bulk-executor-dot-net.md)|
| **Estrutura de suporte atual**| Microsoft .NET Framework 4.5.2, 4.6.1 e .NET Standard 2.0 |

## <a name="release-notes"></a>Notas de versão

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* Incluindo MongoBulkExecutor ao suporte .NET Standard 2.0. Esta funcionalidade faz com que seja funcionalmente equivalente ao 1.3.0 de versão, com a adição do suporte .NET Standard 2.0 como a estrutura de destino.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-Preview

* Foi adicionado .NET Standard 2.0 como uma das arquiteturas de destino suportadas para tornar a biblioteca do BulkExecutor trabalhar com aplicações de .NET Core.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

* Adicionar uma sobrecarga para a operação de BulkDelete de contas da API de SQL aceitar a chave de partição, as tuplas de id do documento a eliminar.
* Adicionar uma sobrecarga para a operação de BulkDelete de contas da API de SQL aceitar RequestOptions que contém a chave de partição especificando o valor da chave de partição, além de usá-lo como um filtro na consulta de entrada, especificando os documentos para eliminar.
* Foi corrigido um problema, o que causou um problema de formatação no agente de utilizador utilizado pelo BulkExecutor.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Feitas melhoria BulkExecutor importação e atualização APIs de forma transparente se adaptar a dimensionamento elástico de contentor do Cosmos DB quando armazenamento excede a capacidade atual sem gerar exceções.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* Diminuí a cópia de segurança a dependência de SDK .NET do DocumentDB para a versão 2.1.3.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Foi corrigido um problema, o que causou BulkExecutor lançar o erro JSRT enquanto a importar para corrigir a coleções.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Foi adicionado suporte para a operação de BulkDelete para contas de API de SQL do Azure Cosmos DB.
* Foi adicionado suporte para a operação de BulkImport para contas com a API do Azure Cosmos DB para o MongoDB.
* Diminuí a cópia de segurança a dependência de SDK .NET do DocumentDB para a versão 2.0.0. 

### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2

* Foi adicionado suporte para a operação de BulkImport para contas de API do Azure Cosmos DB Gremlin.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1

* Correção de erros menor para a operação de BulkImport para contas de API de SQL do Azure Cosmos DB.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* Foi adicionado suporte BulkImport BulkUpdate para operações de e para contas de API de SQL do Azure Cosmos DB.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre a biblioteca Java de Executor em massa, consulte o artigo seguinte:

[Biblioteca de Executor de em massa do Java SDK e a versão de informações](sql-api-sdk-bulk-executor-java.md)

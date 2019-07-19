---
title: 'o Azure Cosmos DB: API do SQL .NET Core, recursos do SDK &'
description: Saiba tudo sobre a API de núcleo de .NET de SQL e o SDK, incluindo as datas de lançamento, datas de extinção e as alterações feitas entre cada versão do Azure Cosmos DB .NET Core SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/22/2018
ms.author: sngun
ms.openlocfilehash: de712d63fa2315a85c6c0eaf392d9c17758e4999
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226182"
---
# <a name="azure-cosmos-db-net-core-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB SDK do .NET Core para a API do SQL: Notas de versão e recursos
> [!div class="op_single_selector"]
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [.NET Standard](sql-api-sdk-dotnet-standard.md)
> * [.NET](sql-api-sdk-dotnet.md)
> * [Feed de alterações de .NET](sql-api-sdk-dotnet-changefeed.md)
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
|**Transferência de SDK**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)|
|**Documentação da API**|[Documentação de referência da .NET API](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Amostras**|[Exemplos de código do .NET](sql-api-dotnet-samples.md)|
|**Introdução**|[Introdução ao Azure Cosmos DB .NET](sql-api-sdk-dotnet.md)|
|**Tutorial da aplicação Web**|[Desenvolvimento de aplicativos Web com o Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Estrutura de suporte atual**|[.NET 1.6 padrão e o .NET Standard 1.5](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>Notas de Versão

> [!NOTE]
> Se você estiver usando o .NET Core, consulte a versão mais recente 3. x do [SDK do .net](sql-api-sdk-dotnet-standard.md), que tem como destino .net Standard. 

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1

* Corrige a condição de corrida de rastreamento para consultas que causaram páginas vazias

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0

* A versão do System .net. http do SDK corresponde ao que está definido no pacote NuGet
* Tamanho de precisão decimal aumentado para consultas LINQ.
* Adicionadas novas classes CompositePath, CompositePathSortOrder, SpatialSpec, Spatialtype e PartitionKeyDefinitionVersion
* Adicionado TimeToLivePropertyPath a Documentcollection
* Adicionado CompositeIndexes e SpatialIndexes ao IndexPolicy
* Versão adicionada ao PartitionKeyDefinition
* Adicionado nenhum ao PartitionKey

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0

 * Adicionado IdleTcpConnectionTimeout, OpenTcpConnectionTimeout, MaxRequestsPerTcpConnection e MaxTcpConnectionsPerEndpoint a ConnectionPolicy.
 
### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3

* Aprimoramentos de diagnóstico

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2

* Adicionada a configuração de variável de ambiente "POCOSerializationOnly".

* O DocumentDB. Spatial. Sql. dll foi removido e agora está incluído em Microsoft. Azure. Documents. oninterop. dll

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1

* Melhoria na lógica de repetição durante o failover para executar chamadas do StoredProcedure.

* DocumentClientEventSource singleton. 

* Corrigir o tempo limite de GatewayAddressCache não respeitando ConnectionPolicy RequestTimeout.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0

* Para diagnóstico de transporte direto/TCP, foi adicionado transportexception, um tipo de exceção interna do SDK. Quando presentes em mensagens de exceção, esse tipo imprime informações adicionais para solucionar problemas de conectividade do cliente.

* Adicionada nova sobrecarga de construtor que usa um HttpMessageHandler, uma pilha de manipulador HTTP a ser usada para enviar solicitações HttpClient (por exemplo, HttpClientHandler).

* Corrija o bug em que o cabeçalho com valores nulos não estava sendo manipulado corretamente.

* Validação de cache de coleção aprimorada.

### <a name="a-name213213"></a><a name="2.1.3"/>2.1.3

* Atualizado System.Net.Security para 4.3.2.

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2

* Melhorias de rastreamento de diagnóstico.

### <a name="a-name211211"></a><a name="2.1.1"/>2.1.1

* Adicionou maior resistência a falhas transitórias de pedido de várias regiões.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0

* Suporte de escrita de adicionado várias regiões.
* Em várias melhorias de desempenho de consulta de partição com a parte superior e MaxBufferedItemCount.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0

* Suporte a cancelamento de solicitação foi adicionado.
* Foi adicionado SetCurrentLocation para ConnectionPolicy, que preenche automaticamente as localizações preferenciais com base na região.
* Foi corrigido o erro em consultas de partição cruzada com mínima/máxima e um filtro que corresponda ao não existem documentos numa partição individual.
* Agora, os métodos DocumentClient tem paridade com IDocumentClient.
* Atualizado TCP transporte pilha diretos para reduzir o número de ligações estabelecidas.
* Foi adicionado suporte para TCP de modo direto para clientes não Windows.

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* Suporte a cancelamento de solicitação foi adicionado.
* Foi adicionado SetCurrentLocation para ConnectionPolicy, que preenche automaticamente as localizações preferenciais com base na região.
* Foi corrigido o erro em consultas de partição cruzada com mínima/máxima e um filtro que corresponda ao não existem documentos numa partição individual.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-Preview

* Agora, os métodos DocumentClient tem paridade com IDocumentClient.
* Atualizado TCP transporte pilha diretos para reduzir o número de ligações estabelecidas.
* Foi adicionado suporte para TCP de modo direto para clientes não Windows.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0

* Propriedade ConsistencyLevel adicionada para FeedOptions.
* Foi adicionado JsonSerializerSettings RequestOptions e FeedOptions.
* Foi adicionado EnableReadRequestsFallback para ConnectionPolicy.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1

* Corrigido KeyNotFoundException para cruzada ordem de partição por consultas em casos específicos.
* Corrigido o bug em que o atributo Jsonproperty na cláusula SELECT para consultas LINQ não estava sendo respeitado.

### <a name="a-name182182"></a><a name="1.8.2"/>1.8.2

* Corrigido o bug que é atingido em determinadas condições de corrida, que resulta em "Microsoft. Azure. Documents. NotFoundException" intermitente: A sessão de leitura não está disponível para o token de sessão de entrada "erros ao usar o nível de consistência da sessão.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1

* Corrigida a regressão onde FeedOptions.MaxItemCount = -1 emitiu um System.ArithmeticException: tamanho da página é negativo.
* Adicionada uma nova função de ToString () para QueryMetrics.
* Estatísticas de partição expostos em coleções de leitura.
* Propriedade de PartitionKey adicionada para ChangeFeedOptions.
* Pequenas correções de erros.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
 
 * Adiciona a capacidade de especificar os índices exclusivos para os documentos, utilizando a propriedade de UniqueKeyPolicy no DocumentCollection.
 * Foi corrigido um erro em que as definições de JsonSerializer personalizadas não eram a ser respeitadas para algumas consultas e a execução do procedimento armazenado.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
 
 * Documentação, informações de metadados em assemblies e o pacote NuGet de imagem corporativa alteração do DocumentDB do Azure ao Azure Cosmos DB na referência de API.
 * Expor informações de diagnóstico e a latência da resposta de pedidos enviados com o modo de conectividade direta. Os nomes de propriedade são RequestDiagnosticsString e RequestLatency na classe ResourceResponse.
 * Esta versão do SDK requer a versão mais recente do emulador do Cosmos DB disponível para download em https://aka.ms/cosmosdb-emulator.
 
### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* Adicionar várias correções de fiabilidade e melhorias.

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1 

* As alterações internas relacionadas ao suporte para [Microsoft.Azure.Graphs](https://docs.microsoft.com/azure/cosmos-db/graph-sdk-dotnet)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0 

* Foi adicionado suporte para PartitionKeyRangeId como um FeedOption para controlar o âmbito os resultados da consulta para um valor de intervalo da chave de partição específica.
* Foi adicionado suporte para StartTime como um ChangeFeedOption para começar a procurar para que as alterações após esse tempo.

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

*   Foi corrigido um problema na classe JsonSerializable que pode fazer com que uma exceção de estouro de pilha.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

*   Foi adicionado suporte para a especificação JsonSerializerSettings personalizado ao instanciar um [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet) instância.

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2

*   Suporte 1.5 padrão do .NET como uma das estruturas de destino.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1

*   Foi corrigido um problema que afetados x64 máquinas que não suportam instruções SSE4 e lançar SEHException quando a execução de consultas do Azure Cosmos DB.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

*   Foi adicionado suporte para um novo nível de consistência chamado ConsistentPrefix.
*   Foi adicionado suporte para as métricas de consulta de partições individuais.
*   Foi adicionado suporte para limitar o tamanho do token de continuação para consultas.
*   Foi adicionado suporte para rastreamento mais detalhado para pedidos falhados.
*   Feitas algumas melhorias de desempenho no SDK.

### <a name="a-name122122"></a><a name="1.2.2"/>1.2.2

* Foi corrigido um problema que o valor de PartitionKey fornecido FeedOptions para consultas agregadas de ignoradas.
* Foi corrigido um problema na manipulação transparente de gestão de partição durante o voo médio entre partições Order By a execução da consulta.

### <a name="a-name121121"></a><a name="1.2.1"/>1.2.1

* Foi corrigido um problema que causou a deadlocks em algumas do async APIs quando utilizado dentro do contexto do ASP.NET.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Correções para manter o SDK mais resilientes a ativação pós-falha automática em determinadas condições.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* Correção de um problema que ocasionalmente causa uma WebException: Não foi possível resolver o nome remoto.
* Foi adicionado o suporte para a leitura diretamente de um documento com tipos adicionando novas sobrecargas para ReadDocumentAsync API.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Foi adicionado suporte LINQ para consultas de agregação (COUNT, MIN, MAX, soma e média).
* Correção para um problema de vazamento de memória para o objeto de ConnectionPolicy causado pelo uso do manipulador de eventos.
* Correção para um problema na qual UpsertAttachmentAsync não estava trabalhando quando utilizou-se a ETag.
* Corrigi um problema na qual partição cruzada por ordem continuação de consulta não trabalhava na classificação no campo de cadeia de caracteres.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Foi adicionado suporte para consultas de agregação (COUNT, MIN, MAX, soma e média). Ver [suporte de agregação](sql-query-aggregates.md).
* Reduzidas débito mínimo em coleções particionadas do 10,100 RU/s para 2500 RU/s.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

O SDK de núcleo de .NET do Azure Cosmos DB permite-lhe criar, rápida e, em várias plataformas [ASP.NET Core](https://www.asp.net/core) e [.NET Core](https://www.microsoft.com/net/core#windows) aplicações sejam executadas no Windows, Mac e Linux. A versão mais recente do Azure Cosmos DB .NET Core SDK é totalmente [Xamarin](https://www.xamarin.com) compatível e ser utilizado para criar aplicativos destinados a iOS, Android e Mono (Linux).  

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-Preview

O SDK de pré-visualização do Azure Cosmos DB .NET Core permite-lhe criar, rápida e, em várias plataformas [ASP.NET Core](https://www.asp.net/core) e [.NET Core](https://www.microsoft.com/net/core#windows) aplicações sejam executadas no Windows, Mac e Linux.

O SDK de pré-visualização do Azure Cosmos DB .NET Core tem paridade de funcionalidades com a versão mais recente dos [SDK de .NET do Azure Cosmos DB](sql-api-sdk-dotnet.md) e suporta o seguinte:
* Todos os [modos de conexão](performance-tips.md#networking): Modo de gateway, TCP direto e HTTPs direto.
* Todos os [níveis de consistência](consistency-levels.md): Forte, sessão, desatualização limitada e eventual.
* [Particionados coleções](partition-data.md).
* [Contas de base de dados de várias regiões e georreplicação](distribute-data-globally.md).

Se você tiver dúvidas relacionadas a esse SDK, poste no [StackOverflow](https://stackoverflow.com/questions/tagged/azure-documentdb)ou envie um problema no repositório do [GitHub](https://github.com/Azure/azure-documentdb-dotnet/issues).

## <a name="release--retirement-dates"></a>Versão & retirada datas

| Versão | Data de lançamento | Data de retirada |
| --- | --- | --- |
| [2.4.1](#2.4.1) |20 de junho de 2019 |--- |
| [2.4.0](#2.4.0) |05 de maio de 2019 |--- |
| [2.3.0](#2.3.0) |04 de abril de 2019 |--- |
| [2.2.3](#2.2.3) |11 de março de 2019 |--- |
| [2.2.2](#2.2.2) |06 de fevereiro de 2019 |--- |
| [2.2.1](#2.2.1) |24 de dezembro de 2018 |--- |
| [2.2.0](#2.2.0) |07 de dezembro de 2018 |--- |
| [2.1.3](#2.1.3) |15 de outubro de 2018 |--- |
| [2.1.2](#2.1.2) |04 de Outubro de 2018 |--- |
| [2.1.1](#2.1.1) |27 de Setembro de 2018 |--- |
| [2.1.0](#2.1.0) |21 de setembro de 2018 |--- |
| [2.0.0](#2.0.0) |07 de Setembro de 2018 |--- |
| [1.9.1](#1.9.1) |09 de Março de 2018 |--- |
| [1.8.2](#1.8.2) |21 de fevereiro de 2018 |--- |
| [1.8.1](#1.8.1) |05 de Fevereiro de 2018 |--- |
| [1.7.1](#1.7.1) |16 de novembro de 2017 |--- |
| [1.7.0](#1.7.0) |10 de Novembro de 2017 |--- |
| [1.6.0](#1.6.0) |17 de Outubro de 2017 |--- |
| [1.5.1](#1.5.1) |02 de Outubro de 2017 |--- |
| [1.5.0](#1.5.0) |10 de Agosto de 2017 |--- | 
| [1.4.1](#1.4.1) |07 de Agosto de 2017 |--- |
| [1.4.0](#1.4.0) |02 de Agosto de 2017 |--- |
| [1.3.2](#1.3.2) |12 de junho de 2017 |--- |
| [1.3.1](#1.3.1) |23 de Maio de 2017 |--- |
| [1.3.0](#1.3.0) |10 de maio de 2017 |--- |
| [1.2.2](#1.2.2) |19 de Abril de 2017 |--- |
| [1.2.1](#1.2.1) |29 de Março de 2017 |--- |
| [1.2.0](#1.2.0) |25 de março de 2017 |--- |
| [1.1.2](#1.1.2) |20 de março de 2017 |--- |
| [1.1.1](#1.1.1) |14 de março de 2017 |--- |
| [1.1.0](#1.1.0) |16 de Fevereiro de 2017 |--- |
| [1.0.0](#1.0.0) |21 de Dezembro de 2016 |--- |
| [0.1.0-preview](#0.1.0-preview) |15 de Novembro de 2016 |31 de Dezembro de 2016 |

## <a name="see-also"></a>Consultar Também
Para saber mais sobre o Cosmos DB, veja [do Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) página do serviço.


---
title: 'o Azure Cosmos DB: SQL .NET API, SDK e recursos'
description: Saiba tudo sobre a API .NET de SQL e o SDK, incluindo as datas de lançamento, datas de extinção e as alterações feitas entre cada versão do SDK de .NET do Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/09/2018
ms.author: sngun
ms.openlocfilehash: 83a866b20d2802b7d49363b7c6451356e938eac1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57838923"
---
# <a name="azure-cosmos-db-net-sdk-for-sql-api-download-and-release-notes"></a>Azure Cosmos DB .NET SDK para a API de SQL: Transferir e notas de versão
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
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Transferência de SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)|
|**Documentação da API**|[Documentação de referência da .NET API](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Amostras**|[Exemplos de código do .NET](sql-api-dotnet-samples.md)|
|**Introdução**|[Introdução ao SDK de .NET do Azure Cosmos DB](sql-api-get-started.md)|
|**Tutorial da aplicação Web**|[Desenvolvimento de aplicativos Web com o Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Estrutura de suporte atual**|[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)|

## <a name="release-notes"></a>Notas de versão

### <a name="a-name3001-preview3001-preview"></a><a name="3.0.0.1-preview"/>3.0.0.1-Preview
* Pré-visualização 1 de [versão 3.0.0](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) do SDK do .NET para pré-visualização pública.
* Destino .NET Standard, que suporta 4.6.1+ do .NET framework e .NET Core 2.0 +
* Novo modelo de objeto, com métodos e de nível superior CosmosClient dividir por classes CosmosDatabases, CosmosContainers e CosmosItems relevantes. 
* Suporte para fluxos. 
* Atualizado CosmosResponseMessage de servidor para devolver o código de estado e apenas lançar a exceção quando nenhuma resposta é devolvida. 

### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3

* Melhorias de diagnóstico

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2

* Definição de variável de ambiente adicionado "POCOSerializationOnly".

* Removido DocumentDB.Spatial.Sql.dll e agora incluída no Microsoft.Azure.Documents.ServiceInterop.dll

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1

* Melhoria na lógica de repetição durante a ativação pós-falha para o StoredProcedure executar chamadas.

* Feitas DocumentClientEventSource singleton. 

* Corrija o tempo limite de GatewayAddressCache não respeitar ConnectionPolicy RequestTimeout.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0

* Para obter um diagnóstico transporte direct/TCP, adicionado TransportException, um tipo de exceção do SDK. Quando presente nas mensagens de exceção, este tipo imprime informações adicionais de resolução de problemas de conectividade do cliente.

* Foram adicionados novos sobrecarga do construtor que aceita um HttpMessageHandler, uma pilha de manipulador HTTP para utilizar para enviar pedidos do HttpClient (por exemplo, HttpClientHandler).

* Corrigi erro em que o cabeçalho com valores nulos não foram a ser processado devidamente.

* Melhorada a validação de cache de coleção.

### <a name="a-name213213"></a><a name="2.1.3"/>2.1.3

* Atualizado System.Net.Security para 4.3.2.

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2

* Melhorias de rastreamento de diagnóstico

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

### <a name="a-name12201220"></a><a name="1.22.0"/>1.22.0

* Propriedade ConsistencyLevel adicionada para FeedOptions.
* Foi adicionado JsonSerializerSettings RequestOptions e FeedOptions.
* Foi adicionado EnableReadRequestsFallback para ConnectionPolicy.

### <a name="a-name12111211"></a><a name="1.21.1"/>1.21.1

* Corrigido KeyNotFoundException para cruzada ordem de partição por consultas em casos específicos.
* Foi corrigido o erro em que não estava sendo premiado JsonProperty atributo na cláusula select para consultas LINQ.

### <a name="a-name12021202"></a><a name="1.20.2"/>1.20.2

* Foi corrigido o erro que for atingido sob determinadas condições de corrida, que resulta num intermitente "Microsoft.Azure.Documents.NotFoundException: A sessão de leitura não está disponível para o token de sessão de entrada"erros ao utilizar o nível de consistência da sessão.

### <a name="a-name12011201"></a><a name="1.20.1"/>1.20.1

* Corrigida a regressão onde FeedOptions.MaxItemCount = -1 emitiu um System.ArithmeticException: tamanho da página é negativo.
* Adicionada uma nova função de ToString () para QueryMetrics.
* Estatísticas de partição expostos em coleções de leitura.
* Propriedade de PartitionKey adicionada para ChangeFeedOptions.
* Pequenas correções de erros.

### <a name="a-name11911191"></a><a name="1.19.1"/>1.19.1

* Adiciona a capacidade de especificar os índices exclusivos para os documentos, utilizando a propriedade de UniqueKeyPolicy no DocumentCollection.
* Foi corrigido um erro em que as definições de JsonSerializer personalizadas não eram a ser respeitadas para algumas consultas e a execução do procedimento armazenado.

### <a name="a-name11901190"></a><a name="1.19.0"/>1.19.0

* Documentação, informações de metadados em assemblies e o pacote NuGet de imagem corporativa alteração do DocumentDB do Azure ao Azure Cosmos DB na referência de API. 
* Expor informações de diagnóstico e a latência da resposta de pedidos enviados com o modo de conectividade direta. Os nomes de propriedade são RequestDiagnosticsString e RequestLatency na classe ResourceResponse.
* Esta versão do SDK requer a versão mais recente do emulador do Cosmos DB disponível para download em https://aka.ms/cosmosdb-emulator. 

### <a name="a-name11811181"></a><a name="1.18.1"/>1.18.1 

* Alterações internas para assemblies de amigos da Microsoft.

### <a name="a-name11801180"></a><a name="1.18.0"/>1.18.0 

* Adicionar várias correções de fiabilidade e melhorias.

### <a name="a-name11701170"></a><a name="1.17.0"/>1.17.0 

* Foi adicionado suporte para PartitionKeyRangeId como um FeedOption para controlar o âmbito os resultados da consulta para um valor de intervalo da chave de partição específica. 
* Foi adicionado suporte para StartTime como um ChangeFeedOption para começar a procurar para que as alterações após esse tempo.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Foi corrigido um problema na classe JsonSerializable que pode fazer com que uma exceção de estouro de pilha.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
*   Foi corrigido um problema que exigia a recompilação do aplicativo devido à introdução de JsonSerializerSettings como um parâmetro opcional no construtor DocumentClient.
* Marcado do construtor DocumentClient obsoleto esse JsonSerializerSettings necessária como o último parâmetro para permitir valores padrão de ConnectionPolicy e os parâmetros de ConsistencyLevel ao passar no parâmetro JsonSerializerSettings.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
*   Foi adicionado suporte para a especificação JsonSerializerSettings personalizado ao instanciar [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet).

### <a name="a-name11411141"></a><a name="1.14.1"/>1.14.1
*   Foi corrigido um problema que afetados x64 máquinas que não suportam instruções SSE4 e lançar uma SEHException ao executar consultas SQL do Azure Cosmos DB.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
*   Foi adicionado suporte para um novo nível de consistência chamado ConsistentPrefix.
*   Foi adicionado suporte para as métricas de consulta de partições individuais.
*   Foi adicionado suporte para limitar o tamanho do token de continuação para consultas.
*   Foi adicionado suporte para rastreamento mais detalhado para pedidos falhados.
*   Feitas algumas melhorias de desempenho no SDK.

### <a name="a-name11341134"></a><a name="1.13.4"/>1.13.4
* Funcionalmente igual 1.13.3. Fez algumas alterações internas.

### <a name="a-name11331133"></a><a name="1.13.3"/>1.13.3
* Funcionalmente igual 1.13.2. Fez algumas alterações internas.

### <a name="a-name11321132"></a><a name="1.13.2"/>1.13.2
* Foi corrigido um problema que o valor de PartitionKey fornecido FeedOptions para consultas agregadas de ignoradas.
* Foi corrigido um problema na manipulação transparente de gestão de partição durante o voo médio entre partições Order By a execução da consulta.

### <a name="a-name11311131"></a><a name="1.13.1"/>1.13.1
* Foi corrigido um problema que causou a deadlocks em algumas do async APIs quando utilizado dentro do contexto do ASP.NET.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Correções para manter o SDK mais resilientes a ativação pós-falha automática em determinadas condições.

### <a name="a-name11221122"></a><a name="1.12.2"/>1.12.2
* Correção para um problema que eventualmente cause uma exceção WebException: Não foi possível resolver o nome remoto.
* Foi adicionado o suporte para a leitura diretamente de um documento com tipos adicionando novas sobrecargas para ReadDocumentAsync API.

### <a name="a-name11211121"></a><a name="1.12.1"/>1.12.1
* Foi adicionado suporte LINQ para consultas de agregação (COUNT, MIN, MAX, soma e média).
* Correção para um problema de vazamento de memória para o objeto de ConnectionPolicy causado pelo uso do manipulador de eventos.
* Correção para um problema na qual UpsertAttachmentAsync não estava trabalhando quando utilizou-se a ETag.
* Corrigi um problema na qual partição cruzada por ordem continuação de consulta não trabalhava na classificação no campo de cadeia de caracteres.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Foi adicionado suporte para consultas de agregação (COUNT, MIN, MAX, soma e média). Ver [suporte de agregação](how-to-sql-query.md#Aggregates).
* Reduzidas débito mínimo em coleções particionadas do 10,100 RU/s para 2500 RU/s.

### <a name="a-name11141114"></a><a name="1.11.4"/>1.11.4
* Correção para um problema em que algumas das consultas entre partições, ocorreram falhas no processo do host de 32 bits.
* Correção para um problema em que o contentor de sessão não estava a ser atualizado com o token para pedidos falhados no modo de Gateway.
* Corrigi um problema em que uma consulta com chamadas UDF no projeção estava falhando em alguns casos.
* Desempenho do lado cliente correções para aumentar o débito de leitura e escrita dos pedidos.

### <a name="a-name11131113"></a><a name="1.11.3"/>1.11.3
* Correção para um problema em que o contentor de sessão não estava a ser atualizado com o token para pedidos falhados.
* Foi adicionado suporte para o SDK trabalhar num processo de host de 32 bits. Tenha em atenção que, se utilizar consultas de partição cruzada, o processamento do host de 64 bits é recomendado para um melhor desempenho.
* Desempenho aprimorado para cenários que envolvem consultas com um grande número de valores de chave de partição numa expressão de IN.
* Preenchido várias estatísticas de quota de recursos no ResourceResponse para pedidos de leitura quando a opção de pedido de PopulateQuotaInfo está definida de coleção de documentos.

### <a name="a-name11111111"></a><a name="1.11.1"/>1.11.1
* Correção de desempenho secundárias para a API de CreateDocumentCollectionIfNotExistsAsync introduzida no 1.11.0.
* Desempenho corrigir no SDK para cenários que envolvem o alto grau de pedidos em simultâneo.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Suporte para novas classes e métodos para processar a [feed de alterações](change-feed.md) de documentos dentro de uma coleção.
* Suporte para consultas entre partições continuação e algumas melhorias de desempenho para consultas entre partições.
* Adição de métodos CreateDatabaseIfNotExistsAsync e CreateDocumentCollectionIfNotExistsAsync.
* Suporte a LINQ para funções de sistema: IsDefined, IsNull e IsPrimitive.
* Correção para binplacing automática de assemblies Microsoft.Azure.Documents.ServiceInterop.dll e DocumentDB.Spatial.Sql.dll para a pasta bin do aplicativo ao utilizar o pacote Nuget com projetos que tenham as ferramentas de Project.
* Suporte para emitir rastreios ETW de lado do cliente que podem ser útil em cenários de depuração.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Conectividade direta foi adicionado suporte para coleções particionadas.
* Desempenho aprimorado para o nível de consistência de estagnação limitada.
* Foi adicionado e LineString tipos de dados ao especificar a coleção de política para consultas espaciais do perímetro geográfico de indexação de polígonos.
* Foi adicionado suporte LINQ para StringEnumConverter, IsoDateTimeConverter e UnixDateTimeConverter ao traduzir a predicados.
* Várias correções de erros SDK.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Foi corrigido um problema que causou o NotFoundException seguinte: A sessão de leitura não está disponível para o token de sessão de entrada. Essa exceção ocorreu em alguns casos, ao consultar para a região de leitura de uma conta distribuída geograficamente.
* Expostos a propriedade ResponseStream na classe ResourceResponse, que permite o acesso direto para o fluxo subjacente a partir de uma resposta.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Modificar as classes ResourceResponse, FeedResponse, StoredProcedureResponse e MediaResponse para implementar a interface pública correspondente, de modo a que pode ser simuladas para teste controlado por implementação (TDD).
* Foi corrigido um problema que causou um cabeçalho de chave de partição tem um formato incorreto ao usar um objeto de JsonSerializerSettings personalizado para serializar dados.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Foi corrigido um problema que causou a falha das consultas longa com o erro: Neste momento, o token de autorização não é válido.
* Foi corrigido um problema que removeu o SqlParameterCollection original de cruzada partição parte superior/ordem-por consultas.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Foi adicionado suporte para consultas paralelas para coleções particionadas.
* Foi adicionado suporte para cruzada consultas de ORDER BY e a parte superior de partição para coleções particionadas.
* Corrigido as referências em falta para DocumentDB.Spatial.Sql.dll e Microsoft.Azure.Documents.ServiceInterop.dll que são necessárias ao referenciar um projeto do Azure Cosmos DB com uma referência ao pacote Nuget do Azure Cosmos DB.
* Corrigida a capacidade de usar parâmetros de diferentes tipos, ao utilizar as funções definidas pelo utilizador no LINQ. 
* Foi corrigido um erro para contas replicadas globalmente em que as chamadas de Upsert foram sejam direcionadas para localizações em vez de localizações de escrita de leitura.
* Foi adicionados métodos para a interface de IDocumentClient que estavam em falta: 
  * Método de UpsertAttachmentAsync que usa mediaStream e opções como parâmetros
  * Método de CreateAttachmentAsync que usa opções como um parâmetro
  * Método de CreateOfferQuery que usa querySpec como um parâmetro.
* Classes públicas não selados que são expostas na IDocumentClient interface.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Foi adicionado o suporte para contas de base de dados de várias regiões.
* Foi adicionado suporte para repetição em pedidos limitados.  Utilizador pode personalizar o número de repetições e o tempo de espera máximo ao configurar a propriedade Retryoptions.
* Adicionar uma nova interface de IDocumentClient que define as assinaturas de todos os métodos e propriedades de DocumentClient.  Como parte dessa alteração, alterar também os métodos de extensão que criar IQueryable e IOrderedQueryable para métodos na classe DocumentClient em si.
* Opção de configuração foi adicionado para definir o ServicePoint.ConnectionLimit para um ponto de final do Azure Cosmos DB determinado Uri.  Utilize ConnectionPolicy.MaxConnectionLimit para alterar o valor predefinido, que está definido como 50.
* IPartitionResolver preterido e sua implementação.  Suporte para IPartitionResolver está obsoleto. Recomenda-se que utilize coleções Particionadas para armazenamento mais elevado e débito.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Adicionado uma sobrecarga ao Uri com base em método de ExecuteStoredProcedureAsync que usa RequestOptions como um parâmetro.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Tempo extra para o suporte do live (TTL) para documentos.

### <a name="a-name163163"></a><a name="1.6.3"/>1.6.3
* Foi corrigido um erro no empacotamento do Nuget do SDK do .NET para criação de pacotes como parte de uma solução de serviço Cloud do Azure.

### <a name="a-name162162"></a><a name="1.6.2"/>1.6.2
* Implementado [particionados coleções](partition-data.md) e [níveis de desempenho definido pelo utilizador](performance-levels.md). 

### <a name="a-name153153"></a><a name="1.5.3"/>1.5.3
* **[Fixo]**  Lança de ponto final de consultar o Azure Cosmos DB: 'System.Net.Http.HttpRequestException: Ocorreu um erro ao copiar o conteúdo para um fluxo ".

### <a name="a-name152152"></a><a name="1.5.2"/>1.5.2
* LINQ expandido suportam incluindo novos operadores para expressões de paginação, condicionais e comparação de intervalo.
  * Tire o operador para ativar o comportamento da parte superior SELECIONE no LINQ
  * Operador de CompareTo para permitir comparações de intervalo de cadeia de caracteres
  * Condicional (?) e reúna operadores (?)
* **[Fixo]**  ArgumentOutOfRangeException ao combinar a projeção de modelo com Where-In numa consulta LINQ. [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* **[Fixo]**  Se Select não é a última expressão o provedor de LINQ pressupõe-se sem projeção e produziu o SELECT * incorretamente.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Upsert implementada, métodos de UpsertXXXAsync adicionado
* Melhorias de desempenho para todos os pedidos
* Suporte de fornecedor de LINQ para condicional, coalesce e métodos CompareTo para cadeias de caracteres
* **[Fixo]**  Provedor de LINQ--> Implementar contém o método na lista para gerar o mesmo SQL como em IEnumerable e matriz
* **[Fixo]**  BackoffRetryUtility utiliza o mesmo HttpRequestMessage novamente em vez de criar um novo, tente novamente
* **[Obsoleto]**  UriFactory.CreateCollection--> agora devem usar UriFactory.CreateDocumentCollection

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1
* **[Fixo]**  Localização emite quando utilizando as informações de cultura en não, como nl-NL, etc. 

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Adicionado o encaminhamento baseado no ID
  * Para ajudar na criação de ligações de recursos com base no ID de programa auxiliar de UriFactory novo
  * Novas sobrecargas em DocumentClient para tirar do URI
* Foi adicionado IsValid() e IsValidDetailed() no LINQ para geoespacial
* Suporte a provedores de LINQ avançado:
  * **Matemática** -Abs, funções Acos, Asin, Atan, invisível, Cos, Exp, andar, Log, Log10, Pow, Round, início de sessão, Sin, Sqrt, Tan, truncar
  * **String** - Concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, SubString, ToUpper
  * **Matriz** -Concat, contém, contagem
  * **IN** operador

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Foi adicionado suporte para modificar as políticas de indexação.
  * Novo método de ReplaceDocumentCollectionAsync DocumentClient
  * Nova propriedade IndexTransformationProgress no ResourceResponse<T> para controlar o progresso por cento das alterações de política de índice
  * Agora é mutável DocumentCollection.IndexingPolicy
* Foi adicionado suporte para indexação espacial e a consulta.
  * Como o novo espaço de nomes de Microsoft.Azure.Documents.Spatial para serializar/desserializar tipos geográficos ponto e polígono
  * Nova classe de SpatialIndex para indexar dados GeoJSON armazenados no Cosmos DB
* **[Fixo]**  Consulta incorreta SQL gerada a partir de uma expressão LINQ [#38](https://github.com/Azure/azure-documentdb-net/issues/38).

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Adicionar uma dependência no v5.0.7 newtonsoft.
* Foram feitas alterações para suportar Order By:
  
  * Suporte de fornecedor LINQ para OrderBy() ou OrderByDescending()
  * IndexingPolicy para suportar Order By 
    
    **Alteração de última hora possível** 
    
    Se tiver código existente em coleções que Aprovisiona com uma política de indexação personalizada, seu código existente tem de ser atualizado para suportar a nova classe de IndexingPolicy. Se tiver uma política de indexação personalizada, em seguida, esta alteração não afeta.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Foi adicionado suporte para criação de partições de dados com as novas classes HashPartitionResolver e RangePartitionResolver e o IPartitionResolver.
* Serialização de DataContract foi adicionada.
* GUID foi adicionado suporte no provedor de LINQ.
* UDF foi adicionado suporte no LINQ.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* SDK DE DISPONIBILIDADE GERAL

## <a name="release--retirement-dates"></a>Datas de lançamento & retirada
A Microsoft fornece notificação, pelo menos, **12 meses** antecedência extinguir um SDK para facilitar a transição para uma versão mais recente/suportadas.

Novos recursos e funcionalidade e otimizações, apenas são adicionadas ao SDK atual, como tal, é recomendável que sempre atualiza para a versão mais recente SDK mais cedo possível. 

Todos os pedidos para o Azure Cosmos DB com um SDK extinto são rejeitados pelo serviço.

<br/>

| Versão | Data de lançamento | Data de retirada |
| --- | --- | --- |
| [2.2.3](#2.2.3) |11 de Fevereiro de 2019 |--- |
| [2.2.2](#2.2.2) |06 de Fevereiro de 2019 |--- |
| [2.2.1](#2.2.1) |24 de Dezembro de 2018 |--- |
| [2.2.0](#2.2.0) |07 de Dezembro de 2018 |--- |
| [2.1.3](#2.1.3) |15 de outubro de 2018 |--- |
| [2.1.2](#2.1.2) |04 de Outubro de 2018 |--- |
| [2.1.1](#2.1.1) |27 de Setembro de 2018 |--- |
| [2.1.0](#2.1.0) |21 de setembro de 2018 |--- |
| [2.0.0](#2.0.0) |07 de Setembro de 2018 |--- |
| [1.22.0](#1.22.0) |19 de abril de 2018 |--- |
| [1.21.1](#1.20.1) |09 de Março de 2018 |--- |
| [1.20.2](#1.20.1) |21 de fevereiro de 2018 |--- |
| [1.20.1](#1.20.1) |05 de Fevereiro de 2018 |--- |
| [1.19.1](#1.19.1) |16 de novembro de 2017 |--- |
| [1.19.0](#1.19.0) |10 de Novembro de 2017 |--- |
| [1.18.1](#1.18.1) |07 de Novembro de 2017 |--- |
| [1.18.0](#1.18.0) |17 de Outubro de 2017 |--- |
| [1.17.0](#1.17.0) |10 de Agosto de 2017 |--- |
| [1.16.1](#1.16.1) |07 de Agosto de 2017 |--- |
| [1.16.0](#1.16.0) |02 de Agosto de 2017 |--- |
| [1.15.0](#1.15.0) |30 de Junho de 2017 |--- |
| [1.14.1](#1.14.1) |23 de Maio de 2017 |--- |
| [1.14.0](#1.14.0) |10 de maio de 2017 |--- |
| [1.13.4](#1.13.4) |09 de Maio de 2017 |--- |
| [1.13.3](#1.13.3) |06 de Maio de 2017 |--- |
| [1.13.2](#1.13.2) |19 de Abril de 2017 |--- |
| [1.13.1](#1.13.1) |29 de Março de 2017 |--- |
| [1.13.0](#1.13.0) |24 de Março de 2017 |--- |
| [1.12.2](#1.12.2) |20 de março de 2017 |--- |
| [1.12.1](#1.12.1) |14 de março de 2017 |--- |
| [1.12.0](#1.12.0) |15 de Fevereiro de 2017 |--- |
| [1.11.4](#1.11.4) |06 de Fevereiro de 2017 |--- |
| [1.11.3](#1.11.3) |26 de Janeiro de 2017 |--- |
| [1.11.1](#1.11.1) |21 de Dezembro de 2016 |--- |
| [1.11.0](#1.11.0) |08 de Dezembro de 2016 |--- |
| [1.10.0](#1.10.0) |27 de Setembro de 2016 |--- |
| [1.9.5](#1.9.5) |01 de Setembro de 2016 |--- |
| [1.9.4](#1.9.4) |24 de Agosto de 2016 |--- |
| [1.9.3](#1.9.3) |15 de Agosto de 2016 |--- |
| [1.9.2](#1.9.2) |23 de Julho de 2016 |--- |
| [1.8.0](#1.8.0) |14 de Junho de 2016 |--- |
| [1.7.1](#1.7.1) |06 de Maio de 2016 |--- |
| [1.7.0](#1.7.0) |26 de Abril de 2016 |--- |
| [1.6.3](#1.6.3) |08 de Abril de 2016 |--- |
| [1.6.2](#1.6.2) |29 de Março de 2016 |--- |
| [1.5.3](#1.5.3) |19 de Fevereiro de 2016 |--- |
| [1.5.2](#1.5.2) |14 de Dezembro de 2015 |--- |
| [1.5.1](#1.5.1) |23 de Novembro de 2015 |--- |
| [1.5.0](#1.5.0) |05 de Outubro de 2015 |--- |
| [1.4.1](#1.4.1) |25 de Agosto de 2015 |--- |
| [1.4.0](#1.4.0) |13 de Agosto de 2015 |--- |
| [1.3.0](#1.3.0) |05 de Agosto de 2015 |--- |
| [1.2.0](#1.2.0) |06 de Julho de 2015 |--- |
| [1.1.0](#1.1.0) |30 de Abril de 2015 |--- |
| [1.0.0](#1.0.0) |08 de Abril de 2015 |--- |


## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também
Para saber mais sobre o Cosmos DB, veja [do Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) página do serviço. 


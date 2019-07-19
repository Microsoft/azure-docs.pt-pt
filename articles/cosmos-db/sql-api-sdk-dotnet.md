---
title: 'o Azure Cosmos DB: API do SQL .NET, recursos do SDK &'
description: Saiba tudo sobre a API e o SDK do SQL .NET, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão do SDK do .NET Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/09/2018
ms.author: sngun
ms.openlocfilehash: d68e957ece5c634ed1ce069218df717a4fe0952f
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68261274"
---
# <a name="azure-cosmos-db-net-sdk-for-sql-api-download-and-release-notes"></a>Azure Cosmos DB SDK do .NET para a API do SQL: Download e notas de versão
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET](sql-api-sdk-dotnet-standard.md)
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
|**Amostras**|[Exemplos de código do .NET](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples)|
|**Introdução**|[Introdução ao SDK do .NET Azure Cosmos DB](sql-api-get-started.md)|
|**Tutorial da aplicação Web**|[Desenvolvimento de aplicativos Web com o Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Estrutura de suporte atual**|[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)|

## <a name="release-notes"></a>Notas de versão

> [!NOTE]
> Se você estiver usando .NET Framework, consulte a versão mais recente 3. x do [SDK do .net](sql-api-sdk-dotnet-standard.md), que tem como alvo .net Standard. 

### <a name="a-name251251"></a><a name="2.5.1"/>2.5.1

* A versão do System .net. http do SDK agora corresponde ao que está definido no pacote NuGet.
* Permitir que solicitações de gravação comentem para uma região diferente se o original falhar.
* Adicionar política de repetição de sessão para solicitação de gravação.

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1

* Corrige a condição de corrida de rastreamento para consultas que causaram páginas vazias

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0

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

* Aprimoramentos de rastreamento de diagnóstico

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
* Corrigido o bug em que o atributo Jsonproperty na cláusula SELECT para consultas LINQ não estava sendo respeitado.

### <a name="a-name12021202"></a><a name="1.20.2"/>1.20.2

* Corrigido o bug que é atingido em determinadas condições de corrida, que resulta em "Microsoft. Azure. Documents. NotFoundException" intermitente: A sessão de leitura não está disponível para o token de sessão de entrada "erros ao usar o nível de consistência da sessão.

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
*   Corrigido um problema que exigia a recompilação do aplicativo devido à introdução do JsonSerializerSettings como um parâmetro opcional no Construtor DocumentClient.
* Foi marcado o Construtor DocumentClient obsoleto que exigia JsonSerializerSettings como o último parâmetro para permitir valores padrão de parâmetros ConnectionPolicy e ConsistencyLevel ao passar no parâmetro JsonSerializerSettings.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
*   Adicionado suporte para especificar JsonSerializerSettings personalizados ao instanciar [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet).

### <a name="a-name11411141"></a><a name="1.14.1"/>1.14.1
*   Corrigido um problema que afetou computadores x64 que não dão suporte à instrução SSE4 e lançou um SEHException ao executar Azure Cosmos DB consultas SQL.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
*   Foi adicionado suporte para um novo nível de consistência chamado ConsistentPrefix.
*   Foi adicionado suporte para as métricas de consulta de partições individuais.
*   Foi adicionado suporte para limitar o tamanho do token de continuação para consultas.
*   Foi adicionado suporte para rastreamento mais detalhado para pedidos falhados.
*   Feitas algumas melhorias de desempenho no SDK.

### <a name="a-name11341134"></a><a name="1.13.4"/>1.13.4
* Funcionalmente o mesmo que 1.13.3. Algumas alterações internas foram feitas.

### <a name="a-name11331133"></a><a name="1.13.3"/>1.13.3
* Funcionalmente o mesmo que 1.13.2. Algumas alterações internas foram feitas.

### <a name="a-name11321132"></a><a name="1.13.2"/>1.13.2
* Foi corrigido um problema que o valor de PartitionKey fornecido FeedOptions para consultas agregadas de ignoradas.
* Foi corrigido um problema na manipulação transparente de gestão de partição durante o voo médio entre partições Order By a execução da consulta.

### <a name="a-name11311131"></a><a name="1.13.1"/>1.13.1
* Foi corrigido um problema que causou a deadlocks em algumas do async APIs quando utilizado dentro do contexto do ASP.NET.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Correções para manter o SDK mais resilientes a ativação pós-falha automática em determinadas condições.

### <a name="a-name11221122"></a><a name="1.12.2"/>1.12.2
* Correção de um problema que ocasionalmente causa uma WebException: Não foi possível resolver o nome remoto.
* Foi adicionado o suporte para a leitura diretamente de um documento com tipos adicionando novas sobrecargas para ReadDocumentAsync API.

### <a name="a-name11211121"></a><a name="1.12.1"/>1.12.1
* Foi adicionado suporte LINQ para consultas de agregação (COUNT, MIN, MAX, soma e média).
* Correção para um problema de vazamento de memória para o objeto de ConnectionPolicy causado pelo uso do manipulador de eventos.
* Correção para um problema na qual UpsertAttachmentAsync não estava trabalhando quando utilizou-se a ETag.
* Corrigi um problema na qual partição cruzada por ordem continuação de consulta não trabalhava na classificação no campo de cadeia de caracteres.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Foi adicionado suporte para consultas de agregação (COUNT, MIN, MAX, soma e média). Ver [suporte de agregação](sql-query-aggregates.md).
* Reduzidas débito mínimo em coleções particionadas do 10,100 RU/s para 2500 RU/s.

### <a name="a-name11141114"></a><a name="1.11.4"/>1.11.4
* Correção de um problema em que algumas das consultas entre partições estavam falhando no processo de host de 32 bits.
* Correção de um problema no qual o contêiner de sessão não estava sendo atualizado com o token para solicitações com falha no modo de gateway.
* Correção de um problema em que uma consulta com chamadas UDF na projeção estava falhando em alguns casos.
* Correções de desempenho no lado do cliente para aumentar a taxa de transferência de leitura e gravação das solicitações.

### <a name="a-name11131113"></a><a name="1.11.3"/>1.11.3
* Correção de um problema no qual o contêiner de sessão não estava sendo atualizado com o token para solicitações com falha.
* Suporte adicionado para o SDK funcionar em um processo de host de 32 bits. Observe que, se você usar consultas entre partições, o processamento de host de 64 bits será recomendado para melhorar o desempenho.
* Melhor desempenho para cenários que envolvem consultas com um grande número de valores de chave de partição em uma expressão IN.
* Foram populadas várias estatísticas de cota de recurso no ResourceResponse para solicitações de leitura de coleção de documentos quando a opção de solicitação PopulateQuotaInfo está definida.

### <a name="a-name11111111"></a><a name="1.11.1"/>1.11.1
* Pequena correção de desempenho para a API CreateDocumentCollectionIfNotExistsAsync introduzida no 1.11.0.
* Correção de desempenho no SDK para cenários que envolvem alto grau de solicitações simultâneas.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Suporte para novas classes e métodos para processar o [feed de alterações](change-feed.md) de documentos dentro de uma coleção.
* Suporte para continuação de consulta entre partições e alguns aprimoramentos de desempenho para consultas entre partições.
* Adição de métodos CreateDatabaseIfNotExistsAsync e CreateDocumentCollectionIfNotExistsAsync.
* Suporte do LINQ para funções do sistema: IsDefined, IsNull e IsPrimitive.
* Correção para binplacing automática de assemblies Microsoft. Azure. Documents. perinterop. dll e DocumentDB. Spatial. Sql. dll na pasta bin do aplicativo ao usar o pacote NuGet com projetos que têm ferramentas Project. JSON.
* Suporte para emissão de rastreamentos ETW do lado do cliente que podem ser úteis em cenários de depuração.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Adicionado suporte de conectividade direta para coleções particionadas.
* Desempenho aprimorado para o nível de consistência de desatualização limitada.
* Foi adicionado e LineString tipos de dados ao especificar a coleção de política para consultas espaciais do perímetro geográfico de indexação de polígonos.
* Adicionado suporte a LINQ para StringEnumConverter, IsoDateTimeConverter e UnixDateTimeConverter durante a conversão de predicados.
* Várias correções de bugs de SDK.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Corrigido um problema que causou o seguinte NotFoundException: A sessão de leitura não está disponível para o token de sessão de entrada. Essa exceção ocorreu em alguns casos ao consultar a região de leitura de uma conta distribuída geograficamente.
* Expôs a propriedade ResponseStream na classe ResourceResponse, que permite o acesso direto ao fluxo subjacente de uma resposta.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Modificadas as classes ResourceResponse, FeedResponse, StoredProcedureResponse e MediaResponse para implementar a interface pública correspondente para que elas possam ser simuladas para TDD (implantação controlada por teste).
* Corrigido um problema que causou um cabeçalho de chave de partição malformado ao usar um objeto JsonSerializerSettings personalizado para serializar dados.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Correção de um problema que causou a falha de consultas de execução longa com erro: O token de autorização não é válido no momento atual.
* Corrigido um problema que removeu o SqlParameterCollection original das consultas de ordem cruzada/ordenados por partição.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Adicionado suporte para consultas paralelas para coleções particionadas.
* Adicionado suporte para as consultas ORDENAdas por partição cruzada por e TOP para coleções particionadas.
* Foram corrigidas as referências ausentes para DocumentDB. Spatial. Sql. dll e Microsoft. Azure. Documents. oninterop. dll que são necessários ao fazer referência a um projeto de Azure Cosmos DB com uma referência ao pacote NuGet Azure Cosmos DB.
* Correção da capacidade de usar parâmetros de tipos diferentes ao usar funções definidas pelo usuário no LINQ. 
* Corrigido um bug para contas replicadas globalmente em que chamadas Upsert estavam sendo direcionadas para locais de leitura em vez de locais de gravação.
* Métodos adicionados à interface IDocumentClient que estavam ausentes: 
  * Método UpsertAttachmentAsync que usa mediaStream e opções como parâmetros
  * Método CreateAttachmentAsync que usa opções como um parâmetro
  * Método CreateOfferQuery que usa querySpec como parâmetro.
* Classes públicas sem lacre que são expostas na interface IDocumentClient.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Foi adicionado o suporte para contas de base de dados de várias regiões.
* Adicionado suporte para repetição em solicitações limitadas.  O usuário pode personalizar o número de repetições e o tempo de espera máximo Configurando a propriedade ConnectionPolicy. Retryoptions.
* Adicionada uma nova interface IDocumentClient que define as assinaturas de todas as propriedades e métodos do DocumentClient.  Como parte dessa alteração, também foram alteradas os métodos de extensão que criam IQueryable e IOrderedQueryable para métodos na própria classe DocumentClient.
* Opção de configuração adicionada para definir o ConnectionLimit para um determinado URI de ponto de extremidade de Azure Cosmos DB.  Use ConnectionPolicy. MaxConnectionLimit para alterar o valor padrão, que é definido como 50.
* IPartitionResolver preterido e sua implementação.  O suporte para IPartitionResolver agora é obsoleto. É recomendável que você use coleções particionadas para obter maior armazenamento e taxa de transferência.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Adicionada uma sobrecarga ao método ExecuteStoredProcedureAsync baseado em URI que usa requestoptions como um parâmetro.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Tempo extra para o suporte do live (TTL) para documentos.

### <a name="a-name163163"></a><a name="1.6.3"/>1.6.3
* Corrigido um bug no empacotamento NuGet do SDK do .NET para compactá-lo como parte de uma solução de serviço de nuvem do Azure.

### <a name="a-name162162"></a><a name="1.6.2"/>1.6.2
* Implementado [particionados coleções](partition-data.md) e [níveis de desempenho definido pelo utilizador](performance-levels.md). 

### <a name="a-name153153"></a><a name="1.5.3"/>1.5.3
* **[Corrigido]** Consultando Azure Cosmos DB ponto de extremidade gera: 'System.Net.Http.HttpRequestException: Erro ao copiar o conteúdo para um fluxo '.

### <a name="a-name152152"></a><a name="1.5.2"/>1.5.2
* Suporte expandido para LINQ, incluindo novos operadores para paginação, expressões condicionais e comparação de intervalo.
  * Operador Take para habilitar o comportamento selecionar TOP no LINQ
  * Operador CompareTo para habilitar comparações de intervalo de cadeia de caracteres
  * Operadores condicionais (?) e de União (??)
* **[Corrigido]** ArgumentOutOfRangeException ao combinar projeção de modelo com o Where-in em uma consulta LINQ. [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* **[Corrigido]** Se SELECT não for a última expressão, o provedor LINQ presumiu que não há projeção e produziu SELECT * incorretamente.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Implementado Upsert, métodos UpsertXXXAsync adicionados
* Melhorias de desempenho para todas as solicitações
* Suporte ao provedor LINQ para métodos condicionais, de União e CompareTo para cadeias de caracteres
* **[Corrigido]** Provedor LINQ--> implementa o método Contains na lista para gerar o mesmo SQL que no IEnumerable e na matriz
* **[Corrigido]** BackoffRetryUtility usa o mesmo HttpRequestMessage novamente em vez de criar um novo na nova tentativa
* **[Obsoleto]** UriFactory. Createcollection--> agora deve usar UriFactory. createdocumentcollection

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1
* **[Corrigido]** Problemas de localização ao usar informações de cultura não en, como NL-NL, etc. 

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Roteamento baseado em ID adicionado
  * Novo auxiliar UriFactory para auxiliar na construção de links de recursos baseados em ID
  * Novas sobrecargas em DocumentClient a serem executadas no URI
* Adicionado IsValid () e IsValidDetailed () em LINQ para geoespacial
* Suporte do provedor LINQ avançado:
  * **Math** -ABS, Acos, Asen, Atan, teto, cos, exp, piso, log, log10, pow, Round, Sign, Sin, sqrt, Tan, TRUNCATE
  * **String** -Concat, Contains, EndsWith, IndexOf, contagem, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, SUBSTRING, ToUpper
  * **Matriz** -Concat, Contains, contagem
  * Operador **in**

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Adicionado suporte para modificar políticas de indexação.
  * Novo método ReplaceDocumentCollectionAsync no DocumentClient
  * Nova Propriedade IndexTransformationProgress em ResourceResponse\<T > para acompanhar a porcentagem de progresso das alterações de política de índice
  * Documentcollection. IndexingPolicy agora é mutável
* Adicionado suporte para indexação espacial e consulta.
  * Novo namespace Microsoft. Azure. Documents. Spatial para serializar/desserializar tipos espaciais como ponto e polígono
  * Nova classe SpatialIndex para indexação de dados geojson armazenados em Cosmos DB
* **[Corrigido]** Consulta SQL incorreta gerada a partir de uma expressão LINQ [#38](https://github.com/Azure/azure-documentdb-net/issues/38).

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Adicionada uma dependência em Newtonsoft. JSON v 5.0.7.
* Foram feitas alterações para oferecer suporte a order by:
  
  * Suporte ao provedor LINQ para OrderBy () ou OrderByDescending ()
  * IndexingPolicy para dar suporte à ordenação por 
    
    **Possível alteração significativa** 
    
    Se você tiver um código existente que provisione coleções com uma política de indexação personalizada, o código existente precisará ser atualizado para dar suporte à nova classe IndexingPolicy. Se você não tiver nenhuma política de indexação personalizada, essa alteração não afetará você.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Adição de suporte para particionamento de dados usando as novas classes HashPartitionResolver e RangePartitionResolver e IPartitionResolver.
* Serialização DataContract adicionada.
* Suporte de GUID adicionado no provedor LINQ.
* Adicionado suporte a UDF no LINQ.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* SDK DE DISPONIBILIDADE GERAL

## <a name="release--retirement-dates"></a>Datas de lançamento & desativação
A Microsoft fornece notificações com pelo menos **12 meses** de antecedência para desativar um SDK a fim de suavizar a transição para uma versão mais recente/com suporte.

Novos recursos e funcionalidades e otimizações são adicionados somente ao SDK atual, por isso é recomendável que você sempre atualize para a versão mais recente do SDK o mais cedo possível. 

Todas as solicitações para Azure Cosmos DB usando um SDK desativado são rejeitadas pelo serviço.

<br/>

| Version | Data de lançamento | Data de retirada |
| --- | --- | --- |
| [2.5.1](#2.5.1) |02 de julho de 2019 |--- |
| [2.4.1](#2.4.1) |20 de junho de 2019 |--- |
| [2.4.0](#2.4.0) |05 de maio de 2019 |--- |
| [2.3.0](#2.3.0) |04 de abril de 2019 |--- |
| [2.2.3](#2.2.3) |11 de fevereiro de 2019 |--- |
| [2.2.2](#2.2.2) |06 de fevereiro de 2019 |--- |
| [2.2.1](#2.2.1) |24 de dezembro de 2018 |--- |
| [2.2.0](#2.2.0) |07 de dezembro de 2018 |--- |
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
| [1.18.1](#1.18.1) |7 de novembro de 2017 |--- |
| [1.18.0](#1.18.0) |17 de Outubro de 2017 |--- |
| [1.17.0](#1.17.0) |10 de Agosto de 2017 |--- |
| [1.16.1](#1.16.1) |07 de Agosto de 2017 |--- |
| [1.16.0](#1.16.0) |02 de Agosto de 2017 |--- |
| [1.15.0](#1.15.0) |30 de junho de 2017 |--- |
| [1.14.1](#1.14.1) |23 de Maio de 2017 |--- |
| [1.14.0](#1.14.0) |10 de maio de 2017 |--- |
| [1.13.4](#1.13.4) |09 de maio de 2017 |--- |
| [1.13.3](#1.13.3) |06 de maio de 2017 |--- |
| [1.13.2](#1.13.2) |19 de Abril de 2017 |--- |
| [1.13.1](#1.13.1) |29 de Março de 2017 |--- |
| [1.13.0](#1.13.0) |24 de março de 2017 |--- |
| [1.12.2](#1.12.2) |20 de março de 2017 |--- |
| [1.12.1](#1.12.1) |14 de março de 2017 |--- |
| [1.12.0](#1.12.0) |15 de fevereiro de 2017 |--- |
| [1.11.4](#1.11.4) |06 de fevereiro de 2017 |--- |
| [1.11.3](#1.11.3) |26 de janeiro de 2017 |--- |
| [1.11.1](#1.11.1) |21 de Dezembro de 2016 |--- |
| [1.11.0](#1.11.0) |8 de dezembro de 2016 |--- |
| [1.10.0](#1.10.0) |27 de setembro de 2016 |--- |
| [1.9.5](#1.9.5) |01 de setembro de 2016 |--- |
| [1.9.4](#1.9.4) |24 de agosto de 2016 |--- |
| [1.9.3](#1.9.3) |15 de agosto de 2016 |--- |
| [1.9.2](#1.9.2) |23 de julho de 2016 |--- |
| [1.8.0](#1.8.0) |14 de Junho de 2016 |--- |
| [1.7.1](#1.7.1) |06 de maio de 2016 |--- |
| [1.7.0](#1.7.0) |26 de abril de 2016 |--- |
| [1.6.3](#1.6.3) |08 de abril de 2016 |--- |
| [1.6.2](#1.6.2) |29 de Março de 2016 |--- |
| [1.5.3](#1.5.3) |19 de fevereiro de 2016 |--- |
| [1.5.2](#1.5.2) |14 de dezembro de 2015 |--- |
| [1.5.1](#1.5.1) |23 de novembro de 2015 |--- |
| [1.5.0](#1.5.0) |05 de Outubro de 2015 |--- |
| [1.4.1](#1.4.1) |25 de agosto de 2015 |--- |
| [1.4.0](#1.4.0) |13 de agosto de 2015 |--- |
| [1.3.0](#1.3.0) |05 de Agosto de 2015 |--- |
| [1.2.0](#1.2.0) |06 de julho de 2015 |--- |
| [1.1.0](#1.1.0) |30 de abril de 2015 |--- |
| [1.0.0](#1.0.0) |08 de abril de 2015 |--- |


## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também
Para saber mais sobre o Cosmos DB, veja [do Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) página do serviço. 


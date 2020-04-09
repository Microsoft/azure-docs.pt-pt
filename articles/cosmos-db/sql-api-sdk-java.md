---
title: 'Azure Cosmos DB: SQL Java API, SDK & recursos'
description: Saiba tudo sobre o SQL Java API e SDK, incluindo datas de lançamento, datas de reforma e alterações feitas entre cada versão do Azure Cosmos DB SQL Java SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 02/21/2020
ms.author: sngun
ms.openlocfilehash: e57029e53365fbf99054e2d926296ccca3360663
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983607"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK para SQL API: Notas de lançamento e recursos
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET Change Feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java assíncrono](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Fornecedor de Recursos REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor a granel - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor a granel - Java](sql-api-sdk-bulk-executor-java.md)

O SQL API Java SDK suporta operações sincronizadas. Para suporte assíncrono, utilize o [SQL ASYNC Java SDK](sql-api-sdk-async-java.md). 

| |  |
|---|---|
|**SDK Download**|[Rio Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**Documentação da API**|[Documentação de referência da API java](/java/api/com.microsoft.azure.documentdb)|
|**Contribuir para o SDK**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Introdução**|[Começar com o Java SDK](sql-api-java-get-started.md)|
|**Tutorial de aplicativo web**|[Desenvolvimento de aplicações web com Azure Cosmos DB](sql-api-java-application.md)|
|**Tempo mínimo de execução suportado**|[Kit de Desenvolvimento Java (JDK) 7+](/java/azure/jdk/?view=azure-java-stable)|

## <a name="release-notes"></a>Notas de versão

### <a name="247"></a><a name="2.4.7"/>2.4.7
* Corrige problema de tempo de ligação do pool.
* Correções auth token refresh em repetições internas.

### <a name="246"></a><a name="2.4.6"/>2.4.6
* Etiqueta de política de réplica do lado do cliente atualizada na base de dadosAccount e configuração de account de base de dados lê a partir de cache.

### <a name="245"></a><a name="2.4.5"/>2.4.5
* Evitando a repetição do erro de alcance da chave de divisórias inválido, se o utilizador fornecer pkRangeId.

### <a name="244"></a><a name="2.4.4"/>2.4.4
* Refrescações de cache de divisória otimizada.
* Corrige o cenário em que o SDK não entretém a sugestão de divisão dividida do servidor e resulta em caches de encaminhamento do lado do cliente incorretos.

### <a name="242"></a><a name="2.4.2"/>2.4.2
* Refrescações de cache de coleção otimizadas.

### <a name="241"></a><a name="2.4.1"/>2.4.1
* Suporte adicional para recuperar mensagem de exceção interior a partir de uma cadeia de diagnóstico de pedido.

### <a name="240"></a><a name="2.4.0"/>2.4.0
* Versão api da versão introduzida na PartitionKeyDefinition.

### <a name="230"></a><a name="2.3.0"/>2.3.0
* Adicione suporte de tempo separado para o modo direto.

### <a name="223"></a><a name="2.2.3"/>2.2.3
* Consumir mensagem de erro nulo do serviço e produzir exceção ao cliente de documentos.

### <a name="222"></a><a name="2.2.2"/>2.2.2
* Melhoria da ligação da tomada, adicionando o padrão SoKeepAlive verdadeiro.

### <a name="220"></a><a name="2.2.0"/>2.2.0
* Suporte de cadeia de diagnóstico de pedidos adicionados.

### <a name="213"></a><a name="2.1.3"/>2.1.3
* Bug fixo em PartitionKey para Hash V2.

### <a name="212"></a><a name="2.1.2"/>2.1.2
* Suporte adicional para índices compostos.
* Bug fixo no gestor de pontofinal global para forçar a atualização.
* Bug fixo para upserts com pré-condições em modo direto.

### <a name="211"></a><a name="2.1.1"/>2.1.1
* Bug fixo na cache do endereço gateway.

### <a name="210"></a><a name="2.1.0"/>2.1.0
* Suporte de escrita multi-região adicionado para o modo direto.
* Suporte adicional para manuseamento de IOExcepções lançadas como ServiceIn exceções, a partir de um proxy.
* Fixou um bug na política de retry de descoberta de ponto final.
* Fixou um bug para garantir que as exceções de ponteironulo não são lançadas no BaseDatabaseAccountConfigurationProvider.
* Fixou um bug para garantir que o QueryIterator não devolve os nulos.
* Fixou um bug para garantir que é permitida uma grande chave de partição

### <a name="200"></a><a name="2.0.0"/>2.0.0
* Suporte de escrita multi-região adicionado para o modo gateway.

### <a name="1164"></a><a name="1.16.4"/>1.16.4
* Fixe um bug em Ler intervalos de teclas para uma consulta.

### <a name="1163"></a><a name="1.16.3"/>1.16.3
* Fixou um bug na definição do tamanho do cabeçalho de token no modo DirectHttps.

### <a name="1162"></a><a name="1.16.2"/>1.16.2
* O streaming adicionado falha no suporte.
* Suporte adicional para metadados personalizados.
* Lógica de manipulação de sessão melhorada.
* Fixou um bug na cache de gama de teclas de divisória.
* Fixou um bug NPE no modo direto.

### <a name="1161"></a><a name="1.16.1"/>1.16.1
* Suporte adicional para Índice Único.
* Suporte adicional para limitar o tamanho do token de continuação nas opções de alimentação.
* Fixou um bug em Json Serialization (carimbo de tempo).
* Fixou um bug em Json Serialization (enum).
* Dependência em com.fasterxml.jackson.core:jackson-databind atualizado para 2.9.5.

### <a name="1160"></a><a name="1.16.0"/>1.16.0
* Melhor Ligação Pooling para modo direto.
* Melhoria melhorada da pré-fetch para consulta de divisória cruzada não-ordenada.
* Melhor geração uUID.
* Lógica de consistência melhorada da sessão.
* Suporte adicional para multipoligon.
* Suporte adicional para estatísticas de gama de divisórias para recolha.
* Fixou um bug no suporte multi-região.

### <a name="1150"></a><a name="1.15.0"/>1.15.0
* Melhor desempenho de Série json.
* Esta versão SDK requer a versão mais recente do [Emulador Db do Azure Cosmos.](https://aka.ms/cosmosdb-emulator)

### <a name="1140"></a><a name="1.14.0"/>1.14.0
* Alterações internas para bibliotecas de amigos da Microsoft.

### <a name="1130"></a><a name="1.13.0"/>1.13.0
* Corrigiu um problema na leitura de gamas-chave de divisóriaúnica únicas.
* Corrigiu um problema na análise de ResourceID que afeta a base de dados com nomes curtos.
* Corrigiu uma causa de problema por codificação da chave de divisória.

### <a name="1120"></a><a name="1.12.0"/>1.12.0
* Correções críticas de bugs para solicitar processamento durante divisórias.
* Corrigiu um problema com os níveis de consistência forte e limitado.

### <a name="1110"></a><a name="1.11.0"/>1.11.0
* Suporte adicional para um novo nível de consistência chamado ConsistentPrefix.
* Fixou um bug na coleção de leitura no modo de sessão.

### <a name="1100"></a><a name="1.10.0"/>1.10.0
* Suporte habilitado para recolha dividida com até 2.500 RU/seg e escala em incrementos de 100 RU/seg.
* Fixou um inseto na montagem nativa que pode causar exceção ao NullRef em algumas consultas.

### <a name="196"></a><a name="1.9.6"/>1.9.6
* Fixou um bug na configuração do motor de consulta que pode causar exceções para consultas no modo Gateway.
* Fixou alguns bugs no recipiente da sessão que podem causar uma exceção "Recurso proprietário não encontrado" para pedidos imediatamente após a criação da recolha.

### <a name="195"></a><a name="1.9.5"/>1.9.5
* Suporte adicional para consultas de agregação (COUNT, MIN, MAX, SUM e AVG). Consulte o suporte de [agregação](sql-query-aggregates.md).
* Suporte adicional para a mudança de alimentos.
* Suporte adicional para informação de quotas de recolha através de RequestOptions.setPopulateCotInfo.
* Suporte adicional para registo de scriptde procedimento armazenado através de RequestOptions.setScriptLoggingEnabled.
* Fixou um bug onde a consulta no modo DirectHttps pode deixar de responder quando se depara com falhas no acelerador.
* Fixou um bug no modo de consistência da sessão.
* Fixou um bug que pode causar a exceção de Referência Snuna em HttpContext quando a taxa de pedido é elevada.
* Melhor desempenho do modo DirectHttps.

### <a name="194"></a><a name="1.9.4"/>1.9.4
* Adicionou um suporte simples de procuração baseado em instâncias de cliente com API ConnectionPolicy.setProxy() API.
* Acrescentou documentClient.close() API para encerrar corretamente a instância do Cliente do Documento.
* Melhor desempenho de consulta no modo de conectividade direta, derivando o plano de consulta da montagem nativa em vez do Gateway.
* Defina FAIL_ON_UNKNOWN_PROPERTIES = falso para que os utilizadores não precisem de definir jsonIgnoreProperties no seu POJO.
* Registo refactored para usar SLF4J.
* Fixou alguns outros bugs no leitor de consistência.

### <a name="193"></a><a name="1.9.3"/>1.9.3
* Fixou um bug na gestão da ligação para evitar fugas de ligação no modo de conectividade direta.
* Fixou um bug na consulta TOP onde pode lançar a exceção De Referência Nu.
* Melhor desempenho reduzindo o número de chamadas de rede para os caches internos.
* Código de estado adicionado, ActivityID e Request URI no DocumentClientException para uma melhor resolução de problemas.

### <a name="192"></a><a name="1.9.2"/>1.9.2
* Corrigiu um problema na gestão de ligação para a estabilidade.

### <a name="191"></a><a name="1.9.1"/>1.9.1
* Suporte adicional para o nível de consistência de Limites.
* Suporte adicional para conectividade direta para operações de CRUD para coleções divididas.
* Fixou um bug na consulta de uma base de dados com a SQL.
* Fixou um inseto na cache da sessão onde o token da sessão pode ser incorretamente definido.

### <a name="190"></a><a name="1.9.0"/>1.9.0
* Suporte adicional para consultas paralelas de divisórias cruzadas.
* Suporte adicional para consultas TOP/ORDER BY para coleções divididas.
* Apoio acrescido para uma forte consistência.
* Suporte adicional para pedidos baseados em nome ao utilizar conectividade direta.
* Fixado para tornar o ActivityId consistente em todos os pedidos de retry.
* Fixou um bug relacionado com a cache da sessão ao recriar uma coleção com o mesmo nome.
* Adicionou DataTypes de Polygon e LineString, especificando a política de indexação de recolha para consultas espaciais de geo-esgrima.
* Problemas fixos com Java Doc para Java 1.8.

### <a name="181"></a><a name="1.8.1"/>1.8.1
* Fixou um bug no PartitionKeyDefinitionMap para cache coleções de divisórias individuais e não fazer pedidos de chave de partição extra fetch.
* Fixou um bug para não voltar a tentar quando for fornecido um valor de chave de partição incorreto.

### <a name="180"></a><a name="1.8.0"/>1.8.0
* Acrescentou o apoio às contas de base de dados multi-regiões.
* Suporte adicional para retry automático em pedidos acelerados com opções para personalizar as tentativas de retry máximo e tempo de espera máximo.  Consulte RetryOptions e ConnectionPolicy.getRetryOptions().
* Código de partição personalizado baseado em IPartitionResolver. Por favor, utilize coleções divididas para maior armazenamento e entrada.

### <a name="171"></a><a name="1.7.1"/>1.7.1
* Acrescentou o apoio político de novo para a limitação das taxas.  

### <a name="170"></a><a name="1.7.0"/>1.7.0
* Tempo adicional para o apoio ao vivo (TTL) para documentos.

### <a name="160"></a><a name="1.6.0"/>1.6.0
* Coleções [divididas implementadas](partition-data.md) e [níveis de desempenho definidos pelo utilizador.](performance-levels.md)

### <a name="151"></a><a name="1.5.1"/>1.5.1
* Fixou um bug em HashPartitionResolver para gerar valores de hash em pouco endiano para ser consistente com outros SDKs.

### <a name="150"></a><a name="1.5.0"/>1.5.0
* Adicione hash & intervalo de divisórias para ajudar com aplicações sharding em várias divisórias.

### <a name="140"></a><a name="1.4.0"/>1.4.0
* Implementar Upsert. Novos métodos upsertXXX adicionados para apoiar a funcionalidade Upsert.
* Implemente o encaminhamento baseado em ID. Sem alterações públicas da API, tudo muda internamente.

### <a name="130"></a><a name="1.3.0"/>1.3.0
* Lançamento ignorado para trazer número de versão em alinhamento com outros SDKs

### <a name="120"></a><a name="1.2.0"/>1.2.0
* Suporta o Índice GeoEspacial
* Valida a propriedade de ID para todos os recursos. Ids for resources can contain ?, /, #, \, characters or end with a space.
* Adiciona novo cabeçalho "progresso de transformação de índice" à Resposta de Recursos.

### <a name="110"></a><a name="1.1.0"/>1.1.0
* Implementa a política de indexação V2

### <a name="100"></a><a name="1.0.0"/>1.0.0
* GA SDK

## <a name="release-and-retirement-dates"></a>Datas de lançamento e de reforma
A Microsoft irá notificar com pelo menos **12 meses** de antecedência a retirada de um SDK de forma a suavizar a transição para uma versão mais recente/suportada.

Novas funcionalidades e funcionalidades e otimizações só são adicionadas ao SDK atual, como tal é recomendado que você sempre atualize para a versão Mais recente SDK o mais cedo possível.

Qualquer pedido à Cosmos DB utilizando um SDK reformado será rejeitado pelo serviço.

> [!WARNING]
> Todas as versões **1.x** do SQL SDK para Java serão retiradas a 30 de maio de **2020**.
> 
>

> [!WARNING]
> Todas as versões do SQL SDK para Java antes da versão **1.0.0** foram retiradas em 29 de fevereiro de **2016**.
> 
> 

<br/>

| Versão | Data de Lançamento | Data de Aposentadoria |
| --- | --- | --- |
| [2.4.7](#2.4.7) |Fev 20, 2020 |--- |
| [2.4.6](#2.4.6) |24 de janeiro de 2020 |--- |
| [2.4.5](#2.4.5) |Nov 10, 2019 |--- |
| [2.4.4](#2.4.4) |24 out, 2019 |--- |
| [2.4.2](#2.4.2) |26 de setembro de 2019 |--- |
| [2.4.1](#2.4.1) |Jul 18, 2019 |--- |
| [2.4.0](#2.4.0) |04 de maio de 2019 |--- |
| [2.3.0](#2.3.0) |Abr 24, 2019 |--- |
| [2.2.3](#2.2.3) |Abr 16, 2019 |--- |
| [2.2.2](#2.2.2) |Abr 05, 2019 |--- |
| [2.2.0](#2.2.0) |Mar 27, 2019 |--- |
| [2.1.3](#2.1.3) |Mar 13, 2019 |--- |
| [2.1.2](#2.1.2) |Mar 09, 2019 |--- |
| [2.1.1](#2.1.1) |Dez 13, 2018 |--- |
| [2.1.0](#2.1.0) |Nov 20, 2018 |--- |
| [2.0.0](#2.0.0) |21 set, 2018 |--- |
| [1.16.4](#1.16.4) |10 de setembro de 2018 |30 de maio de 2020 |
| [1.16.3](#1.16.3) |09 de setembro de 2018 |30 de maio de 2020 |
| [1.16.2](#1.16.2) |29 de junho de 2018 |30 de maio de 2020 |
| [1.16.1](#1.16.1) |16 de maio de 2018 |30 de maio de 2020 |
| [1.16.0](#1.16.0) |Março 15, 2018 |30 de maio de 2020 |
| [1.15.0](#1.15.0) |Nov 14, 2017 |30 de maio de 2020 |
| [1.14.0](#1.14.0) |28 out, 2017 |30 de maio de 2020 |
| [1.13.0](#1.13.0) |25 de agosto de 2017 |30 de maio de 2020 |
| [1.12.0](#1.12.0) |11 de julho de 2017 |30 de maio de 2020 |
| [1.11.0](#1.11.0) |10 de maio de 2017 |30 de maio de 2020 |
| [1.10.0](#1.10.0) |11 de março de 2017 |30 de maio de 2020 |
| [1.9.6](#1.9.6) |21 de fevereiro de 2017 |30 de maio de 2020 |
| [1.9.5](#1.9.5) |31 de janeiro de 2017 |30 de maio de 2020 |
| [1.9.4](#1.9.4) |24 de novembro de 2016 |30 de maio de 2020 |
| [1.9.3](#1.9.3) |30 de outubro de 2016 |30 de maio de 2020 |
| [1.9.2](#1.9.2) |28 de outubro de 2016 |30 de maio de 2020 |
| [1.9.1](#1.9.1) |26 de outubro de 2016 |30 de maio de 2020 |
| [1.9.0](#1.9.0) |03 de outubro de 2016 |30 de maio de 2020 |
| [1.8.1](#1.8.1) |30 de junho de 2016 |30 de maio de 2020 |
| [1.8.0](#1.8.0) |14 de junho de 2016 |30 de maio de 2020 |
| [1.7.1](#1.7.1) |30 de abril de 2016 |30 de maio de 2020 |
| [1.7.0](#1.7.0) |27 de abril de 2016 |30 de maio de 2020 |
| [1.6.0](#1.6.0) |29 de março de 2016 |30 de maio de 2020 |
| [1.5.1](#1.5.1) |31 de dezembro de 2015 |30 de maio de 2020 |
| [1.5.0](#1.5.0) |04 de dezembro de 2015 |30 de maio de 2020 |
| [1.4.0](#1.4.0) |05 de outubro de 2015 |30 de maio de 2020 |
| [1.3.0](#1.3.0) |05 de outubro de 2015 |30 de maio de 2020 |
| [1.2.0](#1.2.0) |05 de agosto de 2015 |30 de maio de 2020 |
| [1.1.0](#1.1.0) |09 de julho de 2015 |30 de maio de 2020 |
| 1.0.1 |12 de maio de 2015 |30 de maio de 2020 |
| [1.0.0](#1.0.0) |07 de abril de 2015 |30 de maio de 2020 |
| 0.9.5-prelease |Mar 09, 2015 |29 de fevereiro de 2016 |
| 0.9.4-prelease |17 de fevereiro de 2015 |29 de fevereiro de 2016 |
| 0.9.3-prelease |13 de janeiro de 2015 |29 de fevereiro de 2016 |
| 0.9.2-prelease |19 de dezembro de 2014 |29 de fevereiro de 2016 |
| 0.9.1-prelease |19 de dezembro de 2014 |29 de fevereiro de 2016 |
| 0.9.0-prelease |10 de dezembro de 2014 |29 de fevereiro de 2016 |

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também
Para saber mais sobre cosmos DB, consulte a página de serviço [do Microsoft Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/)


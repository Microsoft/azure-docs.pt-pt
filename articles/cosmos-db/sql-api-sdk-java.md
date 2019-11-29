---
title: 'Azure Cosmos DB: API Java do SQL, recursos de & do SDK'
description: Saiba tudo sobre o SDK e a API Java do SQL, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão do SDK do Java do Azure Cosmos DB SQL.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 03/13/2019
ms.author: sngun
ms.openlocfilehash: 42dcc0e9bd07f357c17f28dd754e4ae3404e01ff
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561879"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>SDK do Java Azure Cosmos DB para a API do SQL: notas de versão e recursos
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Feed de alterações do .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Fornecedor de Recursos REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor em massa-.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor em massa – Java](sql-api-sdk-bulk-executor-java.md)

O SDK Java da API do SQL dá suporte a operações síncronas. Para obter suporte assíncrono, use o [SDK Java assíncrono de API do SQL](sql-api-sdk-async-java.md). 

| |  |
|---|---|
|**Download do SDK**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**Documentação da API**|[Documentação de referência da API do Java](/java/api/com.microsoft.azure.documentdb)|
|**Contribuir para o SDK**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Get started**|[Introdução ao SDK do Java](sql-api-java-get-started.md)|
|**Tutorial do aplicativo Web**|[Desenvolvimento de aplicativos Web com Azure Cosmos DB](sql-api-java-application.md)|
|**Tempo de execução mínimo com suporte**|[Java Development Kit (JDK) 7 +](https://aka.ms/azure-jdks)|

## <a name="release-notes"></a>Notas de versão

### <a name="a-name245245"></a><a name="2.4.5"/>2.4.5
* Evitando a repetição em um erro de intervalo de chaves de partição inválido, se o usuário fornecer pkRangeId.

### <a name="a-name244244"></a><a name="2.4.4"/>2.4.4
* Atualizações otimizadas do cache do intervalo de chaves de partição.

### <a name="a-name242242"></a><a name="2.4.2"/>2.4.2
* Atualizações otimizadas do cache de coleta.

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1
* Adicionado suporte para recuperar a mensagem de exceção interna da cadeia de caracteres de diagnóstico de solicitação.

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0
* Introduziu a API da versão em PartitionKeyDefinition.

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Adicionado suporte de tempo limite separado para o modo direto.

### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3
* Consumindo mensagem de erro nula do serviço e produzindo exceção do cliente de documento.

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* Melhoria da conexão de soquete, adicionando SoKeepAlive padrão true.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Suporte à cadeia de caracteres de diagnóstico de solicitação adicionado.

### <a name="a-name213213"></a><a name="2.1.3"/>2.1.3
* Corrigido o bug em PartitionKey para hash v2.

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2
* Suporte adicionado para índices compostos.
* Corrigido o bug no global Endpoint Manager para forçar a atualização.
* Corrigido o bug para upserts com pré-condições no modo direto.

### <a name="a-name211211"></a><a name="2.1.1"/>2.1.1
* Corrigido o bug no cache de endereços do gateway.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Suporte para gravação de várias regiões adicionado ao modo direto.
* Adição de suporte para tratamento de IOExceptions geradas como exceções ServiceUnavailable, de um proxy.
* Correção de um bug na política de repetição de descoberta de ponto de extremidade.
* Correção de um bug para garantir que exceções de ponteiro nulo não sejam lançadas em BaseDatabaseAccountConfigurationProvider.
* Corrigido um bug para garantir que QueryIterator não retorne nulos.
* Correção de um bug para garantir que PartitionKey grande seja permitido

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Suporte para gravação de várias regiões adicionado ao modo de gateway.

### <a name="a-name11641164"></a><a name="1.16.4"/>1.16.4
* Corrigido um bug em intervalos de chave de partição de leitura para uma consulta.

### <a name="a-name11631163"></a><a name="1.16.3"/>1.16.3
* Corrigido um bug na definição do tamanho do cabeçalho do token de continuação no modo DirectHttps.

### <a name="a-name11621162"></a><a name="1.16.2"/>1.16.2
* Suporte ao failover de streaming adicionado.
* Suporte adicionado para metadados personalizados.
* Lógica de manipulação de sessão aprimorada.
* Correção de um bug no cache de intervalo de chaves de partição.
* Foi corrigido um bug de NPE no modo direto.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Adicionado suporte para índice exclusivo.
* Adicionado suporte para limitar o tamanho do token de continuação nas opções de feed.
* Correção de um bug na serialização JSON (carimbo de data/hora).
* Correção de um bug na serialização JSON (enum).
* Dependência em com. fasterxml. Jackson. Core: Jackson-DataBind atualizado para 2.9.5.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
* Pool de conexões aprimorado para o modo direto.
* Aperfeiçoamento da pré-busca aprimorado para consulta de partição cruzada não OrderBy.
* Geração de UUID aprimorada.
* Lógica de consistência de sessão aprimorada.
* Adicionado suporte para MultiPolygon.
* Adicionado suporte para estatísticas de intervalo de chaves de partição para coleta.
* Correção de um bug no suporte a várias regiões.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
* Melhor desempenho de serialização JSON.
* Esta versão do SDK requer a versão mais recente do emulador do Azure Cosmos DB disponível para download no https://aka.ms/cosmosdb-emulator.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
* Alterações internas para bibliotecas de amigos da Microsoft.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Corrigido um problema na leitura de intervalos de chaves de partição única.
* Corrigido um problema na análise ResourceId que afeta o banco de dados com nomes curtos.
* Correção de um problema causado pela codificação de chave de partição.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Correções críticas de bugs para solicitar processamento durante divisões de partição.
* Corrigido um problema com os níveis de consistência forte e BoundedStaleness.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Adicionado suporte para um novo nível de consistência chamado ConsistentPrefix.
* Corrigido um bug na leitura da coleção no modo de sessão.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Habilitado o suporte para coleção particionada com menos de 2.500 RU/s e escala em incrementos de 100 RU/seg.
* Foi corrigido um bug no assembly nativo que pode causar exceção NullRef em algumas consultas.

### <a name="a-name196196"></a><a name="1.9.6"/>1.9.6
* Correção de um bug na configuração do mecanismo de consulta que pode causar exceções para consultas no modo de gateway.
* Correção de alguns bugs no contêiner de sessão que podem causar uma exceção de "recurso do proprietário não encontrado" para solicitações imediatamente após a criação da coleção.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Adição de suporte para consultas de agregação (contagem, mín., máx., soma e Méd). Consulte [suporte à agregação](sql-query-aggregates.md).
* Adicionado suporte para o feed de alterações.
* Adicionado suporte para informações de cota de coleção por meio de requestoptions. setPopulateQuotaInfo.
* Adicionado suporte para log de script de procedimento armazenado por meio de requestoptions. setScriptLoggingEnabled.
* Corrigido um bug em que a consulta no modo DirectHttps pode parar de responder ao encontrar falhas de restrição.
* Corrigido um bug no modo de consistência da sessão.
* Correção de um bug que pode causar NullReferenceException no HttpContext quando a taxa de solicitação é alta.
* Desempenho aprimorado do modo DirectHttps.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Adicionado suporte de proxy simples baseado em instância de cliente com a API ConnectionPolicy. SetProxy ().
* A API DocumentClient. Close () foi adicionada para desligar corretamente a instância de DocumentClient.
* Melhor desempenho de consulta no modo de conectividade direta derivando o plano de consulta do assembly nativo em vez do gateway.
* Defina FAIL_ON_UNKNOWN_PROPERTIES = false para que os usuários não precisem definir JsonIgnoreProperties em seu POJO.
* Log refatorado para usar o SLF4J.
* Corrigidos alguns outros bugs no leitor de consistência.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Correção de um bug no gerenciamento de conexões para evitar vazamentos de conexão no modo de conectividade direta.
* Foi corrigido um bug na consulta TOP, em que ele pode gerar exceção NullReference.
* Melhor desempenho, reduzindo o número de chamadas de rede para os caches internos.
* Código de status adicionado, ActivityId e URI de solicitação em DocumentClientException para melhor solução de problemas.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Correção de um problema no gerenciamento de conexão para estabilidade.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1
* Adicionado suporte para o nível de consistência BoundedStaleness.
* Suporte adicionado para conectividade direta para operações CRUD para coleções particionadas.
* Correção de um bug na consulta de um banco de dados com SQL.
* Correção de um bug no cache de sessão em que o token de sessão pode ser definido incorretamente.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Adicionado suporte para consultas paralelas entre partições.
* Adição de suporte para consultas TOP/ORDER BY para coleções particionadas.
* Suporte adicionado para consistência forte.
* Suporte adicionado para solicitações baseadas em nome ao usar a conectividade direta.
* Corrigido para tornar ActivityId ficar consistente entre todas as novas tentativas de solicitação.
* Corrigido um bug relacionado ao cache da sessão ao recriar uma coleção com o mesmo nome.
* Adicionados tipos de polígono e LineString ao especificar a política de indexação de coleção para consultas espaciais de isolamento geográfico.
* Problemas corrigidos com o documento Java para Java 1,8.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1
* Corrigido um bug em PartitionKeyDefinitionMap para armazenar em cache as coleções de partição única e não fazer solicitações de chave de partição de busca extra.
* Foi corrigido um bug para não tentar novamente quando um valor de chave de partição incorreto é fornecido.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Adicionado o suporte para contas de banco de dados de várias regiões.
* Suporte adicionado para repetição automática em solicitações limitadas com opções para personalizar as tentativas de repetição máxima e o tempo de espera máximo de repetição.  Consulte Retryoptions e ConnectionPolicy. getrepetioptions ().
* Código de particionamento personalizado baseado em IPartitionResolver preterido. Use coleções particionadas para obter maior armazenamento e taxa de transferência.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Adicionado suporte à política de repetição para limitação de taxa.  

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Adicionado suporte a TTL (vida útil) para documentos.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Implementadas [coleções particionadas](partition-data.md) e [níveis de desempenho definidos pelo usuário](performance-levels.md).

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* Correção de um bug em HashPartitionResolver para gerar valores de hash em little-endian para ser consistente com outros SDKs.

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Adicione os resolvedores de partição de intervalo de hash & para auxiliar na fragmentação de aplicativos em várias partições.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Implemente Upsert. Novos métodos upsertXXX adicionados para dar suporte ao recurso Upsert.
* Implementar o roteamento baseado em ID. Nenhuma alteração de API pública, todas as alterações internas.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Versão ignorada para colocar o número de versão em alinhamento com outros SDKs

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Dá suporte a índice geoespacial
* Valida a propriedade ID para todos os recursos. As IDs dos recursos não podem conter?,/, #, \, caracteres ou terminar com um espaço.
* Adiciona o novo cabeçalho "progresso da transformação do índice" a ResourceResponse.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Implementa a política de indexação v2

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* SDK DE GA

## <a name="release-and-retirement-dates"></a>Datas de lançamento e desativação
A Microsoft fornecerá uma notificação com pelo menos **12 meses** de antecedência para desativar um SDK a fim de suavizar a transição para uma versão mais recente/com suporte.

Novos recursos e funcionalidades e otimizações são adicionados somente ao SDK atual, por isso é recomendável que você sempre atualize para a versão mais recente do SDK o mais cedo possível.

Qualquer solicitação para Cosmos DB usando um SDK desativado será rejeitada pelo serviço.

> [!WARNING]
> Todas as versões **1. x** do SDK do SQL para Java serão desativadas em **30 de maio de 2020**.
> 
>

> [!WARNING]
> Todas as versões do SDK do SQL para Java anteriores à versão **1.0.0** foram desativadas em **29 de fevereiro de 2016**.
> 
> 

<br/>

| Versão | Data de lançamento | Data de aposentadoria |
| --- | --- | --- |
| [2.1.3](#2.1.3) |13 de março de 2018 |--- |
| [2.1.2](#2.1.2) |09 de março de 2018 |--- |
| [2.1.1](#2.1.1) |13 de dezembro de 2018 |--- |
| [2.1.0](#2.1.0) |20 de novembro de 2018 |--- |
| [2.0.0](#2.0.0) |21 de setembro de 2018 |--- |
| [1.16.4](#1.16.4) |10 de setembro de 2018 |30 de maio de 2020 |
| [1.16.3](#1.16.3) |09 de setembro de 2018 |30 de maio de 2020 |
| [1.16.2](#1.16.2) |29 de junho de 2018 |30 de maio de 2020 |
| [1.16.1](#1.16.1) |16 de maio de 2018 |30 de maio de 2020 |
| [1.16.0](#1.16.0) |15 de março de 2018 |30 de maio de 2020 |
| [1.15.0](#1.15.0) |14 de novembro de 2017 |30 de maio de 2020 |
| [1.14.0](#1.14.0) |28 de outubro de 2017 |30 de maio de 2020 |
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
| [1.4.0](#1.4.0) |5 de outubro de 2015 |30 de maio de 2020 |
| [1.3.0](#1.3.0) |5 de outubro de 2015 |30 de maio de 2020 |
| [1.2.0](#1.2.0) |5 de agosto de 2015 |30 de maio de 2020 |
| [1.1.0](#1.1.0) |09 de julho de 2015 |30 de maio de 2020 |
| 1.0.1 |12 de maio de 2015 |30 de maio de 2020 |
| [1.0.0](#1.0.0) |07 de abril de 2015 |30 de maio de 2020 |
| 0.9.5-prelocação |09 de março de 2015 |29 de fevereiro de 2016 |
| 0.9.4-prelocação |17 de fevereiro de 2015 |29 de fevereiro de 2016 |
| 0.9.3-prelocação |13 de janeiro de 2015 |29 de fevereiro de 2016 |
| 0.9.2-prelocação |19 de dezembro de 2014 |29 de fevereiro de 2016 |
| 0.9.1-prelocação |19 de dezembro de 2014 |29 de fevereiro de 2016 |
| 0.9.0-prelocação |10 de dezembro de 2014 |29 de fevereiro de 2016 |

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Ver também
Para saber mais sobre Cosmos DB, consulte [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) página de serviço.


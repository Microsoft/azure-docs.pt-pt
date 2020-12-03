---
title: 'Azure Cosmos DB: SQL Java API, SDK & recursos'
description: Saiba tudo sobre o SQL Java API e ODK incluindo datas de lançamento, datas de aposentadoria e alterações feitas entre cada versão do Azure Cosmos DB SQL Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 0b48b8d954b132caab96e5978c90687899ea04c2
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96549230"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK para API SQL: Lançar notas e recursos
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
> [!div class="op_single_selector"]
> * [SDK .NET v3](sql-api-sdk-dotnet-standard.md)
> * [SDK .NET v2](sql-api-sdk-dotnet.md)
> * [SDK v2 de .NET Core](sql-api-sdk-dotnet-core.md)
> * [SDK Feed de Alterações .NET v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [SDK v4 de Java](sql-api-sdk-java-v4.md)
> * [SDK v2 Java assíncrono](sql-api-sdk-async-java.md)
> * [SDK v2 Java síncrono](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark Connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Fornecedor de Recursos REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Executor em massa - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor em massa - Java](sql-api-sdk-bulk-executor-java.md)

Este é o Azure Cosmos DB Sync Java SDK v2 para a SQL API que suporta operações sincronizadas.

> [!IMPORTANT]  
> Este *não* é o mais recente Java SDK para Azure Cosmos DB! Considere usar [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) para o seu projeto. Para atualizar, siga as instruções no [guia Migrador para Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) e o guia [Reator vs RxJava.](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) 
>

| |  |
|---|---|
|**SDK Download**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**Documentação da API**|[Documentação de referência da API de Java](/java/api/com.microsoft.azure.documentdb)|
|**Contribuir para a SDK**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Introdução**|[Começa com o Java SDK](./create-sql-api-java.md)|
|**Tutorial de aplicativo web**|[Desenvolvimento de aplicações web com Azure Cosmos DB](sql-api-java-application.md)|
|**Tempo mínimo suportado**|[Kit de Desenvolvimento de Java (JDK) 7+](/java/azure/jdk/?view=azure-java-stable&preserve-view=true)|

## <a name="release-notes"></a>Notas de versão

### <a name="251"></a><a name="2.5.1"></a>2.5.1
* Corrige a emissão de cache de partição primária na consulta documentCollection.

### <a name="250"></a><a name="2.5.0"></a>2.5.0
* Suporte adicionado para 449 retíria configuração personalizada.

### <a name="247"></a><a name="2.4.7"></a>2.4.7
* Corrige o problema do tempo limite de tempo da piscina de ligação.
* Correções auth token refresh em retréis internos.

### <a name="246"></a><a name="2.4.6"></a>2.4.6
* Atualização correta da etiqueta de política de réplica do lado do cliente na base de dadosA contagem e fez a configuração do número de dados As leituras da cache.

### <a name="245"></a><a name="2.4.5"></a>2.4.5
* Evitando voltar a tentar o erro de intervalo da chave de partição inválida, se o utilizador fornecer pkRangeId.

### <a name="244"></a><a name="2.4.4"></a>2.4.4
* Otimização da cache da chave de partição.
* Corrige o cenário em que o SDK não entretém a sugestão de divisão de partição do servidor e resulta em atualização incorreta de caches de encaminhamento do cliente.

### <a name="242"></a><a name="2.4.2"></a>2.4.2
* Atualizaçãos de cache de recolha otimizada.

### <a name="241"></a><a name="2.4.1"></a>2.4.1
* Suporte adicionado para recuperar a mensagem de exceção interior do pedido de cadeia de diagnóstico.

### <a name="240"></a><a name="2.4.0"></a>2.4.0
* Introduziu a versão api na PartitionKeyDefinition.

### <a name="230"></a><a name="2.3.0"></a>2.3.0
* Adicionou suporte de tempo separado para o modo direto.

### <a name="223"></a><a name="2.2.3"></a>2.2.3
* Consumir mensagem de erro nulo do serviço e produzir a exceção do cliente documental.

### <a name="222"></a><a name="2.2.2"></a>2.2.2
* Melhoria da ligação da tomada, adicionando o padrão SoKeepAlive verdadeiro.

### <a name="220"></a><a name="2.2.0"></a>2.2.0
* Apoio de cadeia de diagnóstico de pedido adicionado.

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* Bug fixo na PartitionKey para Hash V2.

### <a name="212"></a><a name="2.1.2"></a>2.1.2
* Suporte adicionado para índices compostos.
* Bug fixo no gestor global de pontos finais para forçar a atualização.
* Bug fixo para ações com pré-condições em modo direto.

### <a name="211"></a><a name="2.1.1"></a>2.1.1
* Bug fixo na cache do endereço do gateway.

### <a name="210"></a><a name="2.1.0"></a>2.1.0
* Suporte de escrita multi-região adicionado para o modo direto.
* Suporte adicional para lidar com iOExceptions lançado como ServiceUn exceções disponíveis, de um representante.
* Corrigi uma falha na política de relemissão da descoberta do ponto final.
* Fixo um bug para garantir que as exceções ao ponteiro nulo não são lançadas no BaseDataBaseAccountConfigurationProvider.
* Corrigiu um erro para garantir que o QueryIterator não devolve os nulos.
* Fixo um bug para garantir que a Grande PartitionKey é permitida

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* Suporte de escrita multi-região adicionado para o modo gateway.

### <a name="1164"></a><a name="1.16.4"></a>1.16.4
* Fixo um bug em Gamas de chaves de partição de leitura para uma consulta.

### <a name="1163"></a><a name="1.16.3"></a>1.16.3
* Corrigiu um erro na definição do tamanho do cabeçalho do símbolo de continuação no modo DirectHttps.

### <a name="1162"></a><a name="1.16.2"></a>1.16.2
* Falha de streaming adicionada sobre suporte.
* Suporte adicional para metadados personalizados.
* Lógica de tratamento de sessão melhorada.
* Fixou um inseto na cache da chave de partição.
* Fixou um erro NPE em modo direto.

### <a name="1161"></a><a name="1.16.1"></a>1.16.1
* Apoio adicional para Índice Único.
* Suporte adicional para limitar o tamanho do token de continuação nas opções de alimentação.
* Fixo um erro na Serialização Json (timetamp).
* Fixou um bug na Serialização Json (enum).
* Dependência em com.fasterxml.jackson.core:jackson-databind atualizado para 2.9.5.

### <a name="1160"></a><a name="1.16.0"></a>1.16.0
* Agrupamento de ligação melhorado para modo direto.
* Melhoria melhorada da Prefetch para consulta de partição cruzada não ordenada.
* Melhor geração UUID.
* Lógica de consistência melhorada da sessão.
* Apoio adicional ao multiplígono.
* Apoio adicional às estatísticas da gama de chaves de partição para a recolha.
* Corrigiu um bug no suporte multi-região.

### <a name="1150"></a><a name="1.15.0"></a>1.15.0
* Melhor desempenho da Json Serialization.
* Esta versão SDK requer a versão mais recente do [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator).

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* Alterações internas para bibliotecas de amigos da Microsoft.

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* Corrigiu um problema na leitura de intervalos de chaves de partição únicas.
* Corrigiu um problema na análise do ResourceID que afeta a base de dados com nomes curtos.
* Corrigiu uma causa de problema por codificação da chave de partição.

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* Correções críticas de erros para solicitar o processamento durante as divisórias.
* Corrigiu um problema com os níveis de consistência Strong e BoundedStaleness.

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* Apoio adicional para um novo nível de consistência chamado ConsistentPrefix.
* Corrigiu um erro na recolha de leitura no modo de sessão.

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* Suporte habilitado para a recolha dividida com até 2.500 RU/seg e escala em incrementos de 100 RU/seg.
* Corrigiu um erro no conjunto nativo que pode causar exceção nullRef em algumas consultas.

### <a name="196"></a><a name="1.9.6"></a>1.9.6
* Corrigiu um erro na configuração do motor de consulta que pode causar exceções para consultas no modo Gateway.
* Corrigiu alguns bugs no recipiente de sessão que podem causar uma exceção "Recurso proprietário não encontrado" para pedidos imediatamente após a criação da recolha.

### <a name="195"></a><a name="1.9.5"></a>1.9.5
* Suporte adicional para consultas de agregação (COUNT, MIN, MAX, SUM e AVG). Consulte [o suporte de agregação](sql-query-aggregate-functions.md).
* Apoio adicional para o feed de mudança.
* Apoio adicional para informações de quotas de recolha através de RequestOptions.setPopulateQuotaInfo.
* Suporte adicionado para script de procedimento armazenado registando através de RequestOptions.setScriptLoggingEnabled.
* Corrigiu um erro onde a consulta no modo DirectHttps pode parar de responder quando se depara com falhas no acelerador.
* Fixo um bug no modo de consistência da sessão.
* Corrigiu um bug que pode causar NullReferenceException em HttpContext quando a taxa de pedido é elevada.
* Melhor desempenho do modo DirectHttps.

### <a name="194"></a><a name="1.9.4"></a>1.9.4
* Adicionou um simples suporte de procuração baseado em instâncias de cliente com ConnectionPolicy.setProxy() API.
* Documento Adicionado.close() API para encerrar adequadamente a instância documentclient.
* Melhor desempenho de consulta no modo de conectividade direta, derivando o plano de consulta da montagem nativa em vez do Gateway.
* Defina FAIL_ON_UNKNOWN_PROPERTIES = falso para que os utilizadores não precisem de definir JsonIgnoreProperties no seu POJO.
* Registo de registo refactorizado para utilizar o SLF4J.
* Corrigimos alguns outros bugs no leitor de consistência.

### <a name="193"></a><a name="1.9.3"></a>1.9.3
* Corrigiu um erro na gestão da ligação para evitar fugas de ligação no modo de conectividade direta.
* Corrigiu um erro na consulta TOP onde pode lançar a exceção NullReference.
* Melhor desempenho reduzindo o número de chamadas de rede para as caches internas.
* Código de estado adicionado, ActivityID e Request URI em DocumentClientException para uma melhor resolução de problemas.

### <a name="192"></a><a name="1.9.2"></a>1.9.2
* Corrigiu um problema na gestão da ligação para a estabilidade.

### <a name="191"></a><a name="1.9.1"></a>1.9.1
* Suporte adicional para o nível de consistência DaVelada.
* Apoio adicional à conectividade direta das operações CRUD para coleções divididas.
* Corrigi um erro na consulta de uma base de dados com o SQL.
* Fixar um erro na cache da sessão onde o token da sessão pode ser configurado incorretamente.

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* Apoio adicional para consultas paralelas de partição cruzada.
* Suporte adicional para consultas TOP/ORDER BY para coleções divididas.
* Apoio acrescido para uma forte consistência.
* Suporte adicionado para pedidos baseados em nomes ao utilizar conectividade direta.
* Fixado para tornar a ActivityId consistente em todas as retretes de pedido.
* Corrigiu um bug relacionado com a cache da sessão ao recriar uma coleção com o mesmo nome.
* Adicionou Polygon e LineString DataTypes ao mesmo tempo que especifica a política de indexação da recolha para consultas espaciais de geo-esgrima.
* Problemas fixos com Java Doc para Java 1.8.

### <a name="181"></a><a name="1.8.1"></a>1.8.1
* Fixo um bug no PartitionKeyDefinitionMap para cache de coleções de divisórias individuais e não fazer pedidos extra de partição.
* Corrigiu um erro para não voltar a tentar quando for fornecido um valor de chave de partição incorreto.

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* Adicionou o apoio a contas de base de dados multi-regiões.
* Suporte adicionado para rejulgação automática em pedidos de aceleração com opções para personalizar as tentativas de rejulgação máxima e o tempo de espera máximo de rejulgação.  Ver RetryOptions e ConnectionPolicy.getRetryOptions().
* Código de partição personalizado codificado IPartitionResolver. Por favor, utilize coleções divididas para um armazenamento e produção mais elevados.

### <a name="171"></a><a name="1.7.1"></a>1.7.1
* Acrescentou o apoio político à limitação das taxas.  

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* Tempo adicional para o apoio ao vivo (TTL) para documentos.

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* Coleções [divididas implementadas](partitioning-overview.md) e [níveis de desempenho definidos pelo utilizador.](performance-levels.md)

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* Corrigiu um bug em HashPartitionResolver para gerar valores de haxixe em pouco endian para ser consistente com outros SDKs.

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* Adicione hash & range division resolver para ajudar com aplicações de fragmentos em várias divisórias.

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* Implementar Upsert. Novos métodos upsertXXX adicionados para suportar a funcionalidade Upsert.
* Implementar encaminhamento baseado em ID. Sem alterações públicas da API, tudo muda internamente.

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* Lançamento ignorado para alinhar o número da versão com outros SDKs

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* Suporta Índice GeoSpatial
* Valida a propriedade de ID para todos os recursos. Ids for resources can contain ?, /, #, \, characters or end with a space.
* Adiciona novo cabeçalho "progresso de transformação de índice" à ResourceResponse.

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* Implementa a política de indexação V2

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* SDK em Disponibilidade Geral

## <a name="release-and-retirement-dates"></a>Datas de libertação e aposentadoria

A Microsoft enviará uma notificação com uma antecedência de pelo menos **12 meses** antes da descontinuação de um SDK, para tornar a transição para uma versão mais recente/suportada mais suave. Novas funcionalidades e funcionalidades e otimizações são adicionadas apenas ao SDK atual, como tal é recomendado que você sempre atual atualização para a versão SDK mais recente o mais cedo possível.

> [!WARNING]
> Depois de 30 de maio de 2020, a Azure Cosmos DB deixará de fazer correções de bugs, adicionar novas funcionalidades e fornecer suporte às versões 1.x do Azure Cosmos DB Java SDK para a SQL API. Se preferir não atualizar, o serviço Azure Cosmos DB continua a servir os pedidos enviados das versões 1.x do SDK.
>
> Depois de 29 de fevereiro de 2016, a Azure Cosmos DB deixará de fazer correções de bugs, adicionar novas funcionalidades e fornecer suporte às versões 0.x do Azure Cosmos DB Java SDK para a SQL API. Se preferir não fazer upgrade, os pedidos enviados da versão 0.x do SDK continuarão a ser servidos pelo serviço DB Azure Cosmos.


| Versão | Data da versão: | Data de Extinção |
| --- | --- | --- |
| [2.5.1](#2.5.1) |03 de junho de 2020 |--- |
| [2.5.0](#2.5.0) |12 de maio de 2020 |--- |
| [2.4.7](#2.4.7) |20 de fevereiro de 2020 |--- |
| [2.4.6](#2.4.6) |24 de janeiro de 2020 |--- |
| [2.4.5](#2.4.5) |Nov 10, 2019 |--- |
| [2.4.4](#2.4.4) |24 out, 2019 |--- |
| [2.4.2](#2.4.2) |26 set 2019 |--- |
| [2.4.1](#2.4.1) |18 de julho de 2019 |--- |
| [2.4.0](#2.4.0) |04 de maio de 2019 |--- |
| [2.3.0](#2.3.0) |Abr 24, 2019 |--- |
| [2.2.3](#2.2.3) |Abr 16, 2019 |--- |
| [2.2.2](#2.2.2) |Abr 05, 2019 |--- |
| [2.2.0](#2.2.0) |Mar 27, 2019 |--- |
| [2.1.3](#2.1.3) |Mar 13, 2019 |--- |
| [2.1.2](#2.1.2) |Mar 09, 2019 |--- |
| [2.1.1](#2.1.1) |Dez 13, 2018 |--- |
| [2.1.0](#2.1.0) |Nov 20, 2018 |--- |
| [2.0.0](#2.0.0) |21 de setembro de 2018 |--- |
| [1.16.4](#1.16.4) |10 de setembro de 2018 |30 de maio de 2020 |
| [1.16.3](#1.16.3) |09 de setembro de 2018 |30 de maio de 2020 |
| [1.16.2](#1.16.2) |29 de junho de 2018 |30 de maio de 2020 |
| [1.16.1](#1.16.1) |16 de maio de 2018 |30 de maio de 2020 |
| [1.16.0](#1.16.0) |15 de março de 2018 |30 de maio de 2020 |
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
| 0.9.5 pré-lease |Mar 09, 2015 |29 de fevereiro de 2016 |
| 0.9.4 pré-lease |17 de fevereiro de 2015 |29 de fevereiro de 2016 |
| 0.9.3 pré-lease |13 de janeiro de 2015 |29 de fevereiro de 2016 |
| 0.9.2 pré-lease |19 de dezembro de 2014 |29 de fevereiro de 2016 |
| 0.9.1 pré-lease |19 de dezembro de 2014 |29 de fevereiro de 2016 |
| 0.9.0 pré-lease |10 de dezembro de 2014 |29 de fevereiro de 2016 |

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também
Para saber mais sobre o Azure Cosmos DB, veja a página do serviço [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
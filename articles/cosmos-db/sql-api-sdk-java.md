---
title: 'Do Azure Cosmos DB: SQL Java API, SDK e recursos'
description: Saiba tudo sobre o SQL Java API e o SDK, incluindo as datas de lançamento, datas de extinção e as alterações feitas entre cada versão do SDK de Java do Azure Cosmos DB SQL.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 03/13/2019
ms.author: sngun
ms.openlocfilehash: 3a7eaabedd6736c107625204f982a530f2bab73d
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718178"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK para a API de SQL: notas de versão e recursos
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
> * [Executor em massa-.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor em massa – Java](sql-api-sdk-bulk-executor-java.md)

O SDK de Java API do SQL oferece suporte a operações síncronas. Para obter suporte assíncrono, utilize o [SQL API Async Java SDK](sql-api-sdk-async-java.md). 

| |  |
|---|---|
|**Transferência de SDK**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**Documentação da API**|[Documentação de referência da API de Java](/java/api/com.microsoft.azure.documentdb)|
|**Contribuir para o SDK**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Introdução**|[Introdução ao SDK do Java](sql-api-java-get-started.md)|
|**Tutorial da aplicação Web**|[Desenvolvimento de aplicativos Web com o Azure Cosmos DB](sql-api-java-application.md)|
|**Mínimo de runtime suportada**|[Kit de desenvolvimento Java (JDK) 7 +](https://aka.ms/azure-jdks)|

## <a name="release-notes"></a>Notas de versão

### <a name="a-name246246"></a><a name="2.4.6"/>2.4.6
* Etiqueta de política de réplica do lado do cliente atualizada na base de dadosAccount e configuração de account de base de dados lê a partir de cache.

### <a name="a-name245245"></a><a name="2.4.5"/>2.4.5
* Evitando a repetição em um erro de intervalo de chaves de partição inválido, se o usuário fornecer pkRangeId.

### <a name="a-name244244"></a><a name="2.4.4"/>2.4.4
* Atualizações otimizadas do cache do intervalo de chaves de partição.
* Corrige o cenário em que o SDK não tem a relação de divisão da partição do servidor e resulta na atualização incorreta de caches de roteamento do lado do cliente.

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
* Foi adicionado para o modo direto de um suporte de escrita de várias regiões.
* Foi adicionado suporte para lidar com IOExceptions lançada como ServiceUnavailable exceções, a partir de um proxy.
* Foi corrigido um erro na política de repetição de deteção de ponto final.
* Foi corrigido um erro para garantir que exceções não são geradas no BaseDatabaseAccountConfigurationProvider de ponteiro nulo.
* Corrigido um bug para garantir que QueryIterator não retorne nulos.
* Foi corrigido um erro para garantir que é permitido PartitionKey grandes

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Foi adicionado para o modo de gateway de um suporte de escrita de várias regiões.

### <a name="a-name11641164"></a><a name="1.16.4"/>1.16.4
* Foi corrigido um erro na chave de partição de leitura intervalos para uma consulta.

### <a name="a-name11631163"></a><a name="1.16.3"/>1.16.3
* Foi corrigido um erro na configuração de tamanho do cabeçalho de token de continuação no modo de DirectHttps.

### <a name="a-name11621162"></a><a name="1.16.2"/>1.16.2
* Falha de transmissão em fluxo foi adicionada ao longo de suporte.
* Foi adicionado suporte para metadados personalizados.
* Melhorada a sessão de lógica de processamento.
* Foi corrigido um erro na cache de intervalo da chave de partição.
* Foi corrigido um erro NPE no modo direto.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Foi adicionado suporte para índice exclusivo.
* Foi adicionado suporte para limitar o tamanho de token de continuação nas opções de feed.
* Foi corrigido um erro na serialização Json (timestamp).
* Foi corrigido um erro na serialização Json (enumeração).
* Dependência de atualizado para 2.9.5 com.fasterxml.jackson.core:jackson-databind.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
* Pool para o modo direto de conexão melhorada.
* Melhorada a melhoria de obtenção prévia para não orderby cruzada consulta da partição.
* Geração de UUID melhorada.
* Lógica de consistência de sessão melhorada.
* Foi adicionado suporte para multipolygon.
* Foi adicionado suporte para estatísticas de intervalo de chave de partição para a coleção.
* Foi corrigido um erro no suporte de várias regiões.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
* Desempenho melhorado de serialização Json.
* Esta versão do SDK requer a versão mais recente do emulador do Cosmos DB disponível para download em https://aka.ms/cosmosdb-emulator.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
* Alterações internas para bibliotecas de amigos da Microsoft.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Foi corrigido um problema na intervalos de chaves de partição única leitura.
* Foi corrigido um problema no ResourceID análise que afeta a base de dados com nomes abreviados.
* Foi corrigido uma causa do problema por codificação de chave de partição.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Correções de erros críticas para pedir o processamento durante divisões de partição.
* Foi corrigido um problema com os níveis de consistência forte e BoundedStaleness.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Foi adicionado suporte para um novo nível de consistência chamado ConsistentPrefix.
* Foi corrigido um erro na coleção no modo de sessão de leitura.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Suporte ativado para uma coleção particionada com como baixa 2.500 RU/seg e dimensionar em incrementos de 100 RU/seg.
* Foi corrigido um erro no assembly nativo que pode fazer com que a exceção de NullRef em algumas consultas.

### <a name="a-name196196"></a><a name="1.9.6"/>1.9.6
* Foi corrigido um erro na configuração do motor de consulta que pode causar exceções para consultas no modo de Gateway.
* Corrigir alguns bugs no contentor de sessão que pode fazer com que uma exceção de "Recurso de proprietário não encontrado" para pedidos imediatamente após a criação de coleção.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Foi adicionado suporte para consultas de agregação (COUNT, MIN, MAX, soma e média). Ver [suporte de agregação](sql-query-aggregates.md).
* Foi adicionado suporte para o feed de alterações.
* Foi adicionado suporte para obter informações de quota de coleção por meio de RequestOptions.setPopulateQuotaInfo.
* Foi adicionado suporte para o registo de script do procedimento armazenado por meio de RequestOptions.setScriptLoggingEnabled.
* Corrigido um bug em que a consulta no modo DirectHttps pode parar de responder ao encontrar falhas de restrição.
* Foi corrigido um erro no modo de consistência da sessão.
* Foi corrigido um erro que pode causar NullReferenceException no HttpContext, quando a taxa de pedidos é elevada.
* Desempenho aprimorado do modo de DirectHttps.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Suporte de proxy com base na instância de cliente simples foi adicionado com a API de ConnectionPolicy.setProxy().
* API de DocumentClient.close() foi adicionado para a instância do DocumentClient encerramento corretamente.
* Desempenho melhorado das consultas no modo de conectividade direta, derivando o plano de consulta do assembly nativo em vez do Gateway.
* Definir FAIL_ON_UNKNOWN_PROPERTIES = false, para que os usuários não precisam definir JsonIgnoreProperties em seus POJO.
* Registo refatorizá-los para utilizar o SLF4J.
* Corrigido alguns outros bugs no leitor de consistência.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Foi corrigido um erro na gestão de ligações para impedir fugas de ligação no modo de conectividade direta.
* Foi corrigido um bug na consulta TOP, em que ele pode gerar exceção NullReference.
* Desempenho melhorado, reduzindo o número de chamada de rede para os caches internos.
* Código de estado adicionado, ActivityID e URI de pedido de mensagens em fila no DocumentClientException para melhor de resolução de problemas.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Foi corrigido um problema na gestão de ligações para estabilidade.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1
* Foi adicionado suporte para o nível de consistência BoundedStaleness.
* Foi adicionado suporte para ligação direta para operações de CRUD para coleções particionadas.
* Foi corrigido um erro na consulta um banco de dados SQL.
* Foi corrigido um erro na cache da sessão em que o token de sessão pode ser definida incorretamente.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Foi adicionado suporte para cruzada consultas paralelas de partição.
* Foi adicionado suporte para a parte superior/ORDER BY consultas para coleções particionadas.
* Foi adicionado suporte para consistência forte.
* Foi adicionado suporte para pedidos de nomes com base em quando utilizar a conectividade direta.
* Corrigido tornar ActivityId manter a consistência em todas as tentativas de pedido.
* Foi corrigido um erro relacionado com a cache da sessão quando recriar uma coleção com o mesmo nome.
* Foi adicionado e LineString tipos de dados ao especificar a coleção de política para consultas espaciais do perímetro geográfico de indexação de polígonos.
* Foi corrigidos problemas com o documento de Java para Java 1.8.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1
* Foi corrigido um erro no PartitionKeyDefinitionMap colocar em cache coleções de partição única e não faça fetch extra pedidos de chave de partição.
* Foi corrigido um erro não repetir quando é fornecido um valor de chave de partição incorreto.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Foi adicionado o suporte para contas de base de dados de várias regiões.
* Foi adicionado suporte para repetição automática em pedidos limitados com opções para personalizar o máximo de tentativas e o tempo de espera de tentativa máximo.  Ver RetryOptions e ConnectionPolicy.getRetryOptions().
* IPartitionResolver preterido com a base de código personalizado de criação de partições. Utilize coleções particionadas para armazenamento e o débito mais elevado.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Suporte de política de repetição foi adicionado à limitação de taxas.  

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Tempo extra para o suporte do live (TTL) para documentos.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Implementado [particionados coleções](partition-data.md) e [níveis de desempenho definido pelo utilizador](performance-levels.md).

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* Foi corrigido um erro no HashPartitionResolver para gerar valores de hash no little-endian para ser consistente com outros SDKs.

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Adicionar Inte & rvalo de Hash de partição resoluções para ajudá-lo em aplicativos de fragmentação em várias partições.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Implemente Upsert. Novos métodos de upsertXXX adicionados para suportar a funcionalidade de Upsert.
* Implemente o encaminhamento baseado na identificação. Sem alterações de API públicas, todas as alterações internas.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Versão ignorada para introduzir o número de versão em alinhamento com outros SDKs

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Oferece suporte Geoespacial índice
* Valida a propriedade ID para todos os recursos. IDs de recursos não podem conter?, /, #, \, carateres ou terminar com um espaço.
* Adiciona o novo cabeçalho "índice transformação progress" ao ResourceResponse.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Implementa a política de indexação V2

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* SDK DE DISPONIBILIDADE GERAL

## <a name="release-and-retirement-dates"></a>Datas de lançamento e de extinção
A Microsoft irá fornecer, pelo menos, notificação **12 meses** antecedência extinguir um SDK para facilitar a transição para uma versão mais recente/suportadas.

Novos recursos e funcionalidade e otimizações, apenas são adicionadas ao SDK atual, como tal, é recomendável que sempre atualiza para a versão mais recente SDK mais cedo possível.

Qualquer pedido ao Cosmos DB com um SDK extinto será rejeitado pelo serviço.

> [!WARNING]
> Todas as versões **1. x** do SDK do SQL para Java serão desativadas em **30 de maio de 2020**.
> 
>

> [!WARNING]
> Todas as versões do SDK para Java SQL anterior à versão **1.0.0** foram extinguidas **29 de Fevereiro de 2016**.
> 
> 

<br/>

| Versão | Data de lançamento | Data de retirada |
| --- | --- | --- |
| [2.1.3](#2.1.3) |13 de março de 2018 |--- |
| [2.1.2](#2.1.2) |09 de março de 2018 |--- |
| [2.1.1](#2.1.1) |13 de dezembro de 2018 |--- |
| [2.1.0](#2.1.0) |20 de novembro de 2018 |--- |
| [2.0.0](#2.0.0) |21 de Setembro de 2018 |--- |
| [1.16.4](#1.16.4) |10 de Setembro de 2018 |30 de maio de 2020 |
| [1.16.3](#1.16.3) |09 de Setembro de 2018 |30 de maio de 2020 |
| [1.16.2](#1.16.2) |29 de Junho de 2018 |30 de maio de 2020 |
| [1.16.1](#1.16.1) |16 de maio de 2018 |30 de maio de 2020 |
| [1.16.0](#1.16.0) |15 de Março de 2018 |30 de maio de 2020 |
| [1.15.0](#1.15.0) |14 de Novembro de 2017 |30 de maio de 2020 |
| [1.14.0](#1.14.0) |28 de Outubro de 2017 |30 de maio de 2020 |
| [1.13.0](#1.13.0) |25 de Agosto de 2017 |30 de maio de 2020 |
| [1.12.0](#1.12.0) |11 de Julho de 2017 |30 de maio de 2020 |
| [1.11.0](#1.11.0) |10 de maio de 2017 |30 de maio de 2020 |
| [1.10.0](#1.10.0) |11 de Março de 2017 |30 de maio de 2020 |
| [1.9.6](#1.9.6) |21 de Fevereiro de 2017 |30 de maio de 2020 |
| [1.9.5](#1.9.5) |31 de Janeiro de 2017 |30 de maio de 2020 |
| [1.9.4](#1.9.4) |24 de Novembro de 2016 |30 de maio de 2020 |
| [1.9.3](#1.9.3) |30 de Outubro de 2016 |30 de maio de 2020 |
| [1.9.2](#1.9.2) |28 de Outubro de 2016 |30 de maio de 2020 |
| [1.9.1](#1.9.1) |26 de Outubro de 2016 |30 de maio de 2020 |
| [1.9.0](#1.9.0) |03 de Outubro de 2016 |30 de maio de 2020 |
| [1.8.1](#1.8.1) |30 de Junho de 2016 |30 de maio de 2020 |
| [1.8.0](#1.8.0) |14 de Junho de 2016 |30 de maio de 2020 |
| [1.7.1](#1.7.1) |30 de Abril de 2016 |30 de maio de 2020 |
| [1.7.0](#1.7.0) |27 de Abril de 2016 |30 de maio de 2020 |
| [1.6.0](#1.6.0) |29 de Março de 2016 |30 de maio de 2020 |
| [1.5.1](#1.5.1) |31 de Dezembro de 2015 |30 de maio de 2020 |
| [1.5.0](#1.5.0) |04 de Dezembro de 2015 |30 de maio de 2020 |
| [1.4.0](#1.4.0) |05 de Outubro de 2015 |30 de maio de 2020 |
| [1.3.0](#1.3.0) |05 de Outubro de 2015 |30 de maio de 2020 |
| [1.2.0](#1.2.0) |05 de Agosto de 2015 |30 de maio de 2020 |
| [1.1.0](#1.1.0) |09 de Julho de 2015 |30 de maio de 2020 |
| 1.0.1 |12 de Maio de 2015 |30 de maio de 2020 |
| [1.0.0](#1.0.0) |07 de Abril de 2015 |30 de maio de 2020 |
| 0.9.5-prelease |09 de Março de 2015 |29 de Fevereiro de 2016 |
| 0.9.4-prelease |17 de Fevereiro de 2015 |29 de Fevereiro de 2016 |
| 0.9.3-prelease |13 de Janeiro de 2015 |29 de Fevereiro de 2016 |
| 0.9.2-prelease |19 de Dezembro de 2014 |29 de Fevereiro de 2016 |
| 0.9.1-prelease |19 de Dezembro de 2014 |29 de Fevereiro de 2016 |
| 0.9.0-prelease |10 de Dezembro de 2014 |29 de Fevereiro de 2016 |

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Ver também
Para saber mais sobre o Cosmos DB, veja [do Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) página do serviço.


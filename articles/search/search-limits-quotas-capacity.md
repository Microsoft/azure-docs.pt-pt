---
title: Limites de serviço para níveis e skus
titleSuffix: Azure Cognitive Search
description: Limites de serviço utilizados para o planeamento de capacidade e limites máximos para pedidos e respostas para pesquisa cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 6ee339cb709a5d825b39b4accf294761c99ee41a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79282981"
---
# <a name="service-limits-in-azure-cognitive-search"></a>Limites de serviço na Pesquisa Cognitiva Azure

Os limites máximos de armazenamento, cargas de trabalho e quantidades de índices e outros objetos dependem se [você fornecer camadas](search-create-service-portal.md) de preços otimizados Azure em **Free**, **Basic**, **Standard**ou **Storage Optimized** pricing.

+ **Free** é um serviço partilhado multi-inquilino que vem com a sua assinatura Azure. Os pedidos de indexação e consulta executam em réplicas e divisórias que são usadas por outros inquilinos.

+ **A Basic** fornece recursos de computação dedicados para cargas horárias de produção em menor escala, mas partilha algumas infraestruturas de networking com outros inquilinos.

+ **A Standard** funciona em máquinas dedicadas com mais capacidade de armazenamento e processamento a todos os níveis. O standard vem em quatro níveis: S1, S2, S3 e S3 HD.

+ **Armazenamento Otimizado** funciona em máquinas dedicadas com armazenamento mais total, largura de banda de armazenamento e memória do que **standard**. Armazenamento Otimizado vem em dois níveis: L1 e L2

> [!NOTE]
> A partir de 1 de julho, todos os níveis estão geralmente disponíveis, incluindo o nível Otimizado de Armazenamento. Todos os preços podem ser encontrados na página de Detalhes de [Preços.](https://azure.microsoft.com/pricing/details/search/)

  S3 High Density (S3 HD) é projetado para cargas de trabalho específicas: [multi-arrendamento](search-modeling-multitenant-saas-applications.md) e grandes quantidades de pequenos índices (um milhão de documentos por índice, três mil índices por serviço). Este nível não fornece a [função indexante](search-indexer-overview.md). No S3 HD, a ingestão de dados deve alavancar a abordagem push, utilizando chamadas API para empurrar dados de origem para índice. 

> [!NOTE]
> Um serviço é prestado a um nível específico. Os níveis de salto para ganhar capacidade envolvem o fornecimento de um novo serviço (não existe uma atualização no local). Para mais informações, consulte [Escolha um SKU ou um nível](search-sku-tier.md). Para saber mais sobre o ajuste da capacidade dentro de um serviço que já disponibilizou, consulte os níveis de [recursos de escala para consultas e cargas](search-capacity-planning.md)de trabalho indexantes .
>

## <a name="subscription-limits"></a>Limites da subscrição
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Limites de armazenamento
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Limites de índice

| Recurso | Gratuito | &nbsp;<sup>básico 1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | ERROS DE L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Índices máximos |3 |5 ou 15 |50 |200 |200 |1000 por partição ou 3000 por serviço |10 |10 |
| Máximo de campos simples por índice |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Campos de recolha complexos máximos por índice |40 |40 |40 |40 |40 |40 |40 |40 |
| Elementos máximos em todas as coleções complexas por documento&nbsp;<sup>2</sup> |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Profundidade máxima dos campos complexos |10 |10 |10 |10 |10 |10 |10 |10 |
| Sugestões [máximas](https://docs.microsoft.com/rest/api/searchservice/suggesters) por índice |1 |1 |1 |1 |1 |1 |1 |1 |
| Perfis [máximos de pontuação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) por índice |100 |100 |100 |100 |100 |100 |100 |100 |
| Funções máximas por perfil |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> Os serviços básicos criados antes de dezembro de 2017 têm limites mais baixos (5 em vez de 15) nos índices. O nível básico é o único SKU com um limite inferior de 100 campos por índice.

<sup>2</sup> Ter um número muito grande de elementos em coleções complexas por documento causa atualmente uma elevada utilização de armazenamento. Este é um problema conhecido. Entretanto, um limite de 3000 é um limite superior seguro para todos os níveis de serviço. Este limite só é aplicado para operações de indexação que utilizam a versão API mais antiga e geralmente disponível (GA) que suporta campos de tipo complexos (`2019-05-06`) em diante. Para não quebrar clientes que possam estar a usar versões API de pré-visualização anteriores (que suportam campos de tipo complexos), não vamos impor este limite para operações de indexação que utilizam estas versões API de pré-visualização. Note que as versões API de pré-visualização não são para ser usadas para cenários de produção e recomendamos vivamente que os clientes se mudem para a versão mais recente da API.

<a name="document-limits"></a>

## <a name="document-limits"></a>Limites de documentos 

A partir de outubro de 2018, já não existem limites de documentos para qualquer novo serviço criado em qualquer nível de faturação (Básico, S1, S2, S3, S3 HD) em qualquer região. Embora a maioria das regiões tenha contagens de documentos ilimitadas desde novembro/dezembro de 2017, houve algumas regiões que continuaram a impor limites de documentos após essa data. Dependendo de quando e onde criou um serviço de pesquisa, poderá estar a executar um serviço que ainda está sujeito a limites de documentos.

Para determinar se o seu serviço tem limites de documentos, utilize a API de Estatísticas do [Serviço GET REST API](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics). Os limites dos documentos refletem-se na resposta, com `null` a indicar em nenhum limite.

> [!NOTE]
> Apesar de não existirem limites de documentos específicos sku, cada índice ainda está sujeito a um limite máximo de segurança para garantir a estabilidade do serviço. Este limite vem de Lucene. Todos os documentos de Pesquisa Cognitiva Azure são indexados internamente como um ou mais documentos Lucene. O número de documentos Lucene por documento de pesquisa depende do número total de elementos em campos de recolha complexos. Cada elemento é indexado como um documento lucene separado. Por exemplo, um documento com 3 elementos num campo de recolha complexo, será indexado como 4 documentos Lucene - 1 para o próprio documento e 3 para os elementos. O número máximo de documentos lucene é de aproximadamente 25 mil milhões por índice.

### <a name="regions-previously-having-document-limits"></a>Regiões anteriormente com limites de documentos

Se o portal indicar um limite de documento, o seu serviço foi criado antes do final de 2017, ou foi criado num centro de dados utilizando clusters de menor capacidade para hospedar serviços de Pesquisa Cognitiva Azure:

+ Leste da Austrália
+ Ásia Leste
+ Índia Central
+ Oeste do Japão
+ E.U.A. Centro-Oeste

Para os serviços sujeitos a limites de documentos, aplicam-se os seguintes limites máximos:

|  Gratuito | Básica | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10,000 |1&nbsp;milhões |15 milhões por partição ou 180 milhões por serviço |60 milhões por partição ou 720 milhões por serviço |120 milhões por partição ou 1,4 biliões por serviço |1 milhão por índice ou 200 milhões por partição |

Se o seu serviço tiver limites que o estão a bloquear, crie um novo serviço e, em seguida, republique todos os conteúdos para esse serviço. Não existe nenhum mecanismo para refornecer o seu serviço para um novo hardware nos bastidores.

> [!Note] 
> Para os serviços de alta densidade S3 criados após o final de 2017, o documento de 200 milhões por partição foi removido, mas o documento de 1 milhão por índice mantém-se.


### <a name="document-size-limits-per-api-call"></a>Limites de tamanho de documento por chamada API

O tamanho máximo do documento ao chamar uma API indexada é de aproximadamente 16 megabytes.

O tamanho do documento é, na verdade, um limite para o tamanho do organismo de pedido de API indexado. Uma vez que pode passar um lote de vários documentos para a API de índice de uma só vez, o limite de tamanho depende realisticamente de quantos documentos estão no lote. Para um lote com um único documento, o tamanho máximo do documento é de 16 MB de JSON.

Para manter o tamanho do documento baixo, lembre-se de excluir dados não consultados do pedido. As imagens e outros dados binários não são diretamente consultados e não devem ser armazenados no índice. Para integrar dados não consultados em resultados de pesquisa, defina um campo não pesquisável que armazene uma referência URL ao recurso.

## <a name="indexer-limits"></a>Limites indexantes

Existem tempos máximos de funcionamento para proporcionar equilíbrio e estabilidade ao serviço como um todo, mas conjuntos de dados maiores podem precisar de mais tempo de indexação do que o máximo permite. Se um trabalho de indexação não puder ser concluído dentro do tempo máximo permitido, tente executá-lo num horário. O programador mantém o registo do estado de indexação. Se um trabalho de indexação programado for interrompido por qualquer motivo, o indexante pode retomar onde ficou parado pela última vez na próxima execução programada.


| Recurso | &nbsp;livre <sup>1</sup> | &nbsp;<sup>básico 2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |ERROS DE L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Indexadores máximos |3 |5 ou 15|50 |200 |200 |N/D |10 |10 |
| Origens de dados máximas |3 |5 ou 15 |50 |200 |200 |N/D |10 |10 |
| Habilidades máximas <sup>4</sup> |3 |5 ou 15 |50 |200 |200 |N/D |10 |10 |
| Carga máxima de indexação por invocação |10.000 documentos |Limitado apenas por documentos máximos |Limitado apenas por documentos máximos |Limitado apenas por documentos máximos |Limitado apenas por documentos máximos |N/D |Sem limite |Sem limite |
| Horário mínimo | 5 minutos |5 minutos |5 minutos |5 minutos |5 minutos |5 minutos |5 minutos | 5 minutos |
| Tempo máximo de funcionamento <sup>5</sup> | 1-3 minutos |24 horas |24 horas |24 horas |24 horas |N/D  |24 horas |24 horas |
| Tempo máximo de funcionamento para habilidades de pesquisa cognitiva ou indexação de blob com análise de imagem <sup>5</sup> | 3-10 minutos |2 horas |2 horas |2 horas |2 horas |N/D  |2 horas |2 horas |
| Indexante blob: tamanho máximo da bolha, MB |16 |16 |128 |256 |256 |N/D  |256 |256 |
| Indexador blob: caracteres máximos de conteúdo extraído de uma bolha |32,000 |64,000 |4&nbsp;milhões |8&nbsp;milhões |16 milhões&nbsp; |N/D |4&nbsp;milhões |4&nbsp;milhões |

<sup>1</sup> Os serviços gratuitos têm o tempo máximo de execução do indexante de 3 minutos para fontes blob e 1 minuto para todas as outras fontes de dados. Para a indexação da IA que entra nos Serviços Cognitivos, os serviços gratuitos estão limitados a 20 transações gratuitas por dia, onde uma transação é definida como um documento que passa com sucesso através do pipeline de enriquecimento.

<sup>2</sup> Os serviços básicos criados antes de dezembro de 2017 têm limites mais baixos (5 em vez de 15) em indexadores, fontes de dados e competências.

<sup>3</sup> Os serviços S3 HD não incluem suporte indexador.

<sup>4</sup> Máximo de 30 habilidades por habilidade.

<sup>5</sup> As cargas de trabalho cognitivas de pesquisa e a análise de imagem na indexação do blob Azure têm tempos de funcionamento mais curtos do que a indexação regular de texto. A análise da imagem e o processamento de linguagem natural são computacionalmente intensivos e consomem quantidades desproporcionadas de poder de processamento disponível. O tempo de funcionamento foi reduzido para dar a outros empregos na fila uma oportunidade de correr.  

> [!NOTE]
> Tal como indicado nos limites do [Índice,](#index-limits)os indexadores também aplicarão o limite superior de 3000 elementos em todas as coleções complexas por documento, a começar pela mais recente versão ga API que suporta tipos complexos (`2019-05-06`) em diante. Isto significa que se criou o seu indexante com uma versão API anterior, não estará sujeito a este limite. Para preservar a máxima compatibilidade, um indexante que foi criado com uma versão API anterior e depois atualizado com uma versão API `2019-05-06` ou posterior, continuará a ser **excluído** dos limites. Os clientes devem estar cientes do impacto adverso de ter coleções complexas muito grandes (como indicado anteriormente) e recomendamos vivamente a criação de quaisquer novos indexers com a mais recente versão ga API.

## <a name="synonym-limits"></a>Limites do sinónimo

O número máximo de mapas sinónimo permitidos varia em linha com o nível de preços. Cada regra pode ter até 20 expansões, onde uma expansão é um termo equivalente. Por exemplo, dado "gato", associação com "gatinho", "felino" e "felis" (o género dos gatos) contaria como 3 expansões.

| Recurso | Gratuito | Básica | S1 | S2 | S3 | S3-HD |L1 | ERROS DE L2 |
| -------- | -----|------ |----|----|----|-------|---|----|
| Mapas de sinónimo máximo |3 |3|5 |10 |20 |20 | 10 | 10 |
| Número máximo de regras por mapa |5000 |20000|20000 |20000 |20000 |20000 | 20000 | 20000  |

## <a name="queries-per-second-qps"></a>Consultas por segundo (QPS)

As estimativas do QPS devem ser desenvolvidas de forma independente por todos os clientes. Tamanho e complexidade do índice, tamanho e complexidade da consulta, e a quantidade de tráfego são determinantes primários do QPS. Não há forma de oferecer estimativas significativas quando tais fatores são desconhecidos.

As estimativas são mais previsíveis quando calculadas em serviços em funcionamento em recursos dedicados (níveis básicos e standard). Pode estimar o QPS mais de perto porque tem controlo sobre mais dos parâmetros. Para obter orientações sobre como abordar a estimativa, consulte o desempenho e otimização da [Pesquisa Cognitiva azure.](search-performance-optimization.md)

Para os níveis Otimizados de Armazenamento, deve esperar uma entrada de consulta mais baixa e uma latência mais elevada do que os níveis Standard.  A metodologia para estimar o desempenho da consulta que vai experimentar é a mesma que os níveis Standard.

## <a name="data-limits-ai-enrichment"></a>Limites de dados (enriquecimento de IA)

Um pipeline de enriquecimento de [IA](cognitive-search-concept-intro.md) que faz chamadas para um recurso De SMS Analytics para reconhecimento de [entidades,](cognitive-search-skill-entity-recognition.md)extração de [frases-chave,](cognitive-search-skill-keyphrases.md) [análise de sentimentos,](cognitive-search-skill-sentiment.md) [deteção de linguagem,](cognitive-search-skill-language-detection.md)e [deteção de PII](cognitive-search-skill-pii-detection.md) está sujeito a limites de dados. O tamanho máximo de um recorde deve ser de 50.000 caracteres medido [si ](https://docs.microsoft.com/dotnet/api/system.string.length)`String.Length`. Se precisar de separar os seus dados antes de enviá-los para o analisador de sentimentos, utilize a [habilidade Text Split](cognitive-search-skill-textsplit.md).

## <a name="throttling-limits"></a>Limites de estrangulamento

As consultas de pesquisa e os pedidos de indexação são acelerados à medida que o sistema se aproxima da capacidade máxima. O estrangulamento comporta-se de forma diferente para diferentes APIs. Apis de consulta (Search/Suggest/Autocomplete) e indexando o acelerador de APIs dinamicamente com base na carga no serviço. As APIs do índice têm limites estáticos de taxa de pedido. 

Limites de pedido de taxa estática para operações relacionadas com um índice:

+ Índices de Lista (GET/índices): 5 por segundo por unidade de pesquisa
+ Obter Índice (GET/índices/myindex): 10 por segundo por unidade de pesquisa
+ Criar Índice (POST/índices): 12 por minuto por unidade de pesquisa
+ Criar ou Atualizar Índice (PUT/índices/myindex): 6 por segundo por unidade de pesquisa
+ Eliminar Índice (DELETE /indexs/myindex): 12 por minuto por unidade de pesquisa 

## <a name="api-request-limits"></a>Limites de pedido da API
* Máximo de 16 MB por pedido <sup>1</sup>
* Comprimento máximo de 8 KB URL
* Máximo de 1000 documentos por lote de uploads, fusões ou exclusões de índices
* Máximo de 32 campos na cláusula $orderby
* O tamanho máximo do período de pesquisa é de 32.766 bytes (32 KB menos 2 bytes) de texto codificado UTF-8

<sup>1</sup> Na Pesquisa Cognitiva Azure, o corpo de um pedido está sujeito a um limite superior de 16 MB, impondo um limite prático ao conteúdo de campos ou coleções individuais que de outra forma não estejam limitados por limites teóricos (ver tipos de [dados suportados](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) para obter mais informações sobre composição e restrições de campo).

## <a name="api-response-limits"></a>Limites de resposta da API
* Máximo de 1000 documentos devolvidos por página dos resultados da pesquisa
* Máximo de 100 sugestões devolvidas por pedido de API sugestão

## <a name="api-key-limits"></a>Limites de chave DaPI
As teclas API são utilizadas para autenticação de serviço. Existem dois tipos. As chaves de administrador são especificadas no cabeçalho do pedido e concedem acesso completo de leitura ao serviço. As chaves de consulta são apenas de leitura, especificadas no URL, e normalmente distribuídas para aplicações de clientes.

* Máximo de 2 chaves de administração por serviço
* Máximo de 50 chaves de consulta por serviço
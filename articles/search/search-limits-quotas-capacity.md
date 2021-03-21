---
title: Limites de serviço para níveis e skus
titleSuffix: Azure Cognitive Search
description: Limites de serviço utilizados para o planeamento da capacidade e limites máximos em pedidos e respostas para a Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 994ed74750d159dfdb83259e9fe921f870ec2241
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99509372"
---
# <a name="service-limits-in-azure-cognitive-search"></a>Limites de serviço no Azure Cognitive Search

Os limites máximos de armazenamento, cargas de trabalho e quantidades de índices e outros objetos dependem de se fornece níveis de preços otimizados [Azure Cognitive Search](search-create-service-portal.md) at **Free,** **Basic,** **Standard** ou **Storage Optimized** pricing.

+ **Free** é um serviço compartilhado multi-inquilino que vem com a sua assinatura Azure. 

+ **A Basic** fornece recursos computamento dedicados para cargas de trabalho de produção em menor escala, mas partilha algumas infraestruturas de networking com outros inquilinos.

+ **A norma** funciona em máquinas dedicadas com mais capacidade de armazenamento e processamento a todos os níveis. O padrão vem em quatro níveis: S1, S2, S3 e S3 HD. S3 High Density (S3 HD) é projetado para [multi-arrendamento](search-modeling-multitenant-saas-applications.md) e grandes quantidades de pequenos índices (três mil índices por serviço). O S3 HD não fornece a funcionalidade do [indexante](search-indexer-overview.md) e a ingestão de dados deve alavancar as APIs que empurram os dados da fonte para o índice. 

+ **Armazenamento Otimizado** funciona em máquinas dedicadas com mais armazenamento total, largura de banda de armazenamento e memória do que **o Standard**. Este nível tem como alvo índices grandes e em mudança lenta. Armazenamento Otimizado vem em dois níveis: L1 e L2.

## <a name="subscription-limits"></a>Limites da subscrição
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Limites de armazenamento
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Limites de índice

| Recurso | Gratuito | Básico &nbsp; <sup>1</sup>  | S1 | S2 | S3 | S3 &nbsp; HD | L1 | L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Índices máximos |3 |5 ou 15 |50 |200 |200 |1000 por partição ou 3000 por serviço |10 |10 |
| Máximo de campos simples por índice |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Campos de recolha complexos máximos por índice |40 |40 |40 |40 |40 |40 |40 |40 |
| Elementos máximos em todas as coleções complexas por documento &nbsp; <sup>2</sup> |3.000 |3.000 |3.000 |3.000 |3.000 |3.000 |3.000 |3.000 |
| Profundidade máxima de campos complexos |10 |10 |10 |10 |10 |10 |10 |10 |
| Indicadores [máximos](/rest/api/searchservice/suggesters) por índice |1 |1 |1 |1 |1 |1 |1 |1 |
| Perfis [de pontuação](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) máximo por índice |100 |100 |100 |100 |100 |100 |100 |100 |
| Funções máximas por perfil |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> Os serviços básicos criados antes de dezembro de 2017 têm limites mais baixos (5 em vez de 15) nos índices. O nível básico é o único SKU com um limite inferior de 100 campos por índice.

<sup>2</sup> Existe um limite superior para os elementos porque ter um grande número deles aumenta significativamente o armazenamento necessário para o seu índice. Um elemento de uma coleção complexa é definido como um membro dessa coleção. Por exemplo, assuma um [documento de hotel com uma coleção complexa de quartos,](search-howto-complex-data-types.md#indexing-complex-types)cada quarto na coleção de quartos é considerado um elemento. Durante a indexação, o motor de indexação pode processar com segurança um máximo de 3000 elementos em todo o documento como um todo. [Este limite](search-api-migration.md#upgrade-to-2019-05-06) foi introduzido `api-version=2019-05-06` e aplica-se apenas a coleções complexas, e não a coleções de cordas ou a campos complexos.

<a name="document-limits"></a>

## <a name="document-limits"></a>Limites de documentos 

A partir de outubro de 2018, já não existem limites de contagem de documentos para qualquer novo serviço criado em qualquer nível faturativo (Básico, S1, S2, S3, S3) em qualquer região. Os serviços mais antigos criados antes de outubro de 2018 podem ainda estar sujeitos a limites de contagem de documentos.

Para determinar se o seu serviço tem limites de documentos, utilize a [API de Estatísticas de Serviço GET](/rest/api/searchservice/get-service-statistics). Os limites do documento refletem-se na resposta, `null` não indicando limites.

> [!NOTE]
> Embora não existam limites de documentos impostos pelo serviço, existe um limite de fragmentos de aproximadamente 24 mil milhões de documentos por índice nos serviços de pesquisa Basic, S1, S2 e S3. Para o S3 HD, o limite de fragmentos é de 2 mil milhões de documentos por índice. Cada elemento de uma coleção complexa conta como um documento separado em termos de limites de fragmentos.

### <a name="document-size-limits-per-api-call"></a>Limites de tamanho do documento por chamada da API

O tamanho máximo do documento quando se chama API de índice é de aproximadamente 16 megabytes.

O tamanho do documento é, na verdade, um limite para o tamanho do corpo de pedido de API indexar. Uma vez que pode passar um lote de vários documentos para a API do índice de uma só vez, o limite de tamanho depende realisticamente de quantos documentos estão no lote. Para um lote com um único documento, o tamanho máximo do documento é de 16 MB de JSON.

Ao estimar o tamanho do documento, lembre-se de considerar apenas os campos que podem ser consumidos por um serviço de pesquisa. Quaisquer dados binários ou de imagem nos documentos de origem devem ser omitidos dos seus cálculos.  

## <a name="indexer-limits"></a>Limites de indexante

Os tempos máximos de funcionamento existem para proporcionar equilíbrio e estabilidade ao serviço como um todo, mas conjuntos de dados maiores podem precisar de mais tempo de indexação do que o máximo permite. Se um trabalho de indexação não puder ser concluído dentro do tempo máximo permitido, tente executá-lo em um horário. O programador acompanha o estado de indexação. Se um trabalho de indexação programado for interrompido por qualquer motivo, o indexante pode retomar onde ficou pela última vez na próxima corrida programada.


| Recurso | Grátis &nbsp; <sup>1</sup> | Básico &nbsp; <sup>2</sup>| S1 | S2 | S3 | S3 &nbsp; HD &nbsp; <sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Indexadores máximos |3 |5 ou 15|50 |200 |200 |N/D |10 |10 |
| Origens de dados máximas |3 |5 ou 15 |50 |200 |200 |N/D |10 |10 |
| Competências máximas <sup>4</sup> |3 |5 ou 15 |50 |200 |200 |N/D |10 |10 |
| Carga máxima de indexação por invocação |10.000 documentos |Limitado apenas por documentos máximos |Limitado apenas por documentos máximos |Limitado apenas por documentos máximos |Limitado apenas por documentos máximos |N/D |Sem limite |Sem limite |
| Horário mínimo | 5 minutos |5 minutos |5 minutos |5 minutos |5 minutos |5 minutos |5 minutos | 5 minutos |
| Tempo máximo de funcionamento| 1-3 minutos |24 horas |24 horas |24 horas |24 horas |N/D  |24 horas |24 horas |
| Tempo máximo de execução para indexadores com um skillset <sup>5</sup> | 3-10 minutos |Duas horas |Duas horas |Duas horas |Duas horas |N/D  |Duas horas |Duas horas |
| Indexador blob: tamanho máximo de bolha, MB |16 |16 |128 |256 |256 |N/D  |256 |256 |
| Indexador blob: caracteres máximos de conteúdo extraído de uma bolha |32,000 |64,000 |4 &nbsp; milhões |8 &nbsp; milhões |16 &nbsp; milhões |N/D |4 &nbsp; milhões |4 &nbsp; milhões |

<sup>1</sup> Os serviços gratuitos têm um tempo máximo de execução indexante de 3 minutos para fontes blob e 1 minuto para todas as outras fontes de dados. Para a indexação da IA que liga para os Serviços Cognitivos, os serviços gratuitos estão limitados a 20 transações gratuitas por dia, onde uma transação é definida como um documento que passa com sucesso através do pipeline de enriquecimento.

<sup>2</sup> Os serviços básicos criados antes de dezembro de 2017 têm limites mais baixos (5 em vez de 15) em indexadores, fontes de dados e skillsets.

<sup>3</sup> Os serviços S3 HD não incluem suporte indexante.

<sup>4</sup> Máximo de 30 competências por skillset.

<sup>5</sup> O enriquecimento e a análise de imagem da IA são computacionalmente intensivos e consomem quantidades desproporcionadas de poder de processamento disponível. O tempo de funcionamento destas cargas de trabalho foi encurtado para dar a outros empregos na fila mais oportunidades de correr.

> [!NOTE]
> Tal como indicado nos [limites](#index-limits)do Índice, os indexantes também aplicarão o limite superior de 3000 elementos em todas as coleções complexas por documento, começando com a versão mais recente da API ga que suporta tipos complexos ( `2019-05-06` ) em diante. Isto significa que se criou o seu indexante com uma versão API anterior, não estará sujeito a este limite. Para preservar a compatibilidade máxima, um indexante que foi criado com uma versão API anterior e depois atualizado com uma versão API `2019-05-06` ou posterior, continuará **a** ser excluído dos limites. Os clientes devem estar cientes do impacto adverso de ter coleções complexas muito grandes (como indicado anteriormente) e recomendamos vivamente a criação de novos indexadores com a versão mais recente da GA API.

## <a name="shared-private-link-resource-limits"></a>Limites de recursos de ligação privada compartilhados

Os indexantes podem aceder a outros recursos Azure [através de pontos finais privados geridos](search-indexer-howto-access-private.md) através do [recurso de ligação privada partilhada API](/rest/api/searchmanagement/sharedprivatelinkresources). Esta secção descreve os limites associados a esta capacidade.

| Recurso | Gratuito | Básico | S1 | S2 | S3 | S3 HD | L1 | L2
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Suporte do indexador de pontos finais privados | No | Yes | Yes | Yes | Yes | No | Yes | Yes |
| Suporte de ponto final privado para indexadores com um skillset<sup>1</sup> | No | No | No | Yes | Yes | No | Yes | Yes |
| Pontos finais máximos privados | N/D | 10 ou 30 | 100 | 400 | 400 | N/D | 20 | 20 |
| Máximo distintos tipos de recursos<sup>2</sup> | N/D | 4 | 7 | 15 | 15 | N/D | 4 | 4 |

<sup>1</sup> O enriquecimento e análise de imagem da IA são computacionalmente intensivos e consomem quantidades desproporcionadas de poder de processamento disponível. Por esta razão, as ligações privadas são desativadas em níveis inferiores para evitar um impacto adverso no desempenho e estabilidade do próprio serviço de pesquisa.

<sup>2</sup> O número de tipos de recursos distintos é calculado como o número de valores únicos `groupId` utilizados em todos os recursos de ligação privada partilhada para um determinado serviço de pesquisa, independentemente do estado do recurso.

## <a name="synonym-limits"></a>Limites de sinônio

O número máximo de mapas de sinónimo varia por nível. Cada regra pode ter até 20 expansões, onde uma expansão é um termo equivalente. Por exemplo, dado "gato", associação com "gatinho", "felino" e "felis" (o género para gatos) contaria como 3 expansões.

| Recurso | Gratuito | Básico | S1 | S2 | S3 | S3-HD |L1 | L2 |
| -------- | -----|------ |----|----|----|-------|---|----|
| Mapas máximos de sinónimo |3 |3|5 |10 |20 |20 | 10 | 10 |
| Número máximo de regras por mapa |5000 |20 000|20 000 |20 000 |20 000 |20 000 | 20 000 | 20 000  |

## <a name="data-limits-ai-enrichment"></a>Limites de dados (enriquecimento de IA)

Um [oleoduto de enriquecimento de IA](cognitive-search-concept-intro.md) que faz chamadas para um recurso Text Analytics para reconhecimento de [entidades](cognitive-search-skill-entity-recognition.md), [extração de frases-chave,](cognitive-search-skill-keyphrases.md)análise de [sentimentos,](cognitive-search-skill-sentiment.md) [deteção](cognitive-search-skill-language-detection.md)de linguagem e [deteção de informação pessoal](cognitive-search-skill-pii-detection.md) está sujeito a limites de dados. O tamanho máximo de um disco deve ser de 50.000 caracteres medido por [`String.Length`](/dotnet/api/system.string.length) . Se precisar de separar os seus dados antes de os enviar para o analisador de sentimentos, utilize a [habilidade Text Split](cognitive-search-skill-textsplit.md).

## <a name="throttling-limits"></a>Limites de estrangulamento

Os pedidos da API são acelerados à medida que o sistema se aproxima da capacidade máxima. O estrangulamento comporta-se de forma diferente para diferentes APIs. APIs de consulta (Search/Suggest/Autocomplete) e indexar o acelerador APIs dinamicamente com base na carga do serviço. ApIs de índice e operações de serviço API têm limites estáticos de taxa de pedido. 

Limites de pedido de taxa estática para operações relacionadas com um índice:

+ Índices de Lista (GET/indexes): 3 por segundo por unidade de pesquisa
+ Obtenha Índice (GET/indexes/myindex): 10 por segundo por unidade de pesquisa
+ Criar Índice (POST/índices): 12 por minuto por unidade de pesquisa
+ Criar ou atualizar índice (PUT/indexes/myindex): 6 por segundo por unidade de pesquisa
+ Eliminar Índice (DELETE/indexes/myindex): 12 por minuto por unidade de pesquisa 

Limites de pedido de taxa estática para operações relacionadas com um serviço:

+ Estatísticas de Serviço (GET/servicestats): 4 por segundo por unidade de pesquisa

## <a name="api-request-limits"></a>Limites de pedido da API
* Máximo de 16 MB por pedido <sup>1</sup>
* Comprimento máximo de URL de 8 KB
* Máximo de 1000 documentos por lote de uploads de índices, fusões ou eliminações
* Máximo 32 campos na cláusula $orderby
* O tamanho máximo do período de pesquisa é de 32.766 bytes (32 KB menos 2 bytes) de texto codificado UTF-8

<sup>1</sup> Em Azure Cognitive Search, o corpo de um pedido está sujeito a um limite superior de 16 MB, impondo um limite prático ao conteúdo de campos ou coleções individuais que não são de outra forma limitados por limites teóricos (ver tipos de [dados suportados](/rest/api/searchservice/supported-data-types) para mais informações sobre composição e restrições de campo).

## <a name="api-response-limits"></a>Limites de resposta da API
* Máximo de 1000 documentos devolvidos por página de resultados de pesquisa
* Máximo de 100 sugestões devolvidas por pedido de API

## <a name="api-key-limits"></a>Limites-chave da API
As teclas API são utilizadas para a autenticação do serviço. Existem dois tipos. As teclas de administração estão especificadas no cabeçalho do pedido e concedem acesso completo à leitura-escrita ao serviço. As chaves de consulta são apenas de leitura, especificadas no URL, e normalmente distribuídas para aplicações do cliente.

* Máximo de 2 teclas de administração por serviço
* Máximo de 50 chaves de consulta por serviço

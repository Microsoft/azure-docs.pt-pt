---
title: Limites de serviço para camadas e SKUs
titleSuffix: Azure Cognitive Search
description: Limites de serviço usados para planejamento de capacidade e limites máximos de solicitações e respostas para Pesquisa Cognitiva do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 6ee339cb709a5d825b39b4accf294761c99ee41a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846280"
---
# <a name="service-limits-in-azure-cognitive-search"></a>Limites de serviço no Azure Pesquisa Cognitiva

Os limites máximos de armazenamento, cargas de trabalho e quantidades de índices e outros objetos dependem de você [provisionar pesquisa cognitiva do Azure](search-create-service-portal.md) em tipos de preço **gratuitos**, **básicos**, **padrão**ou com **otimização de armazenamento** .

+ **Gratuito** é um serviço compartilhado multilocatário que vem com sua assinatura do Azure. As solicitações de indexação e consulta são executadas em réplicas e partições que são usadas por outros locatários.

+ O **básico** fornece recursos de computação dedicados para cargas de trabalho de produção em uma escala menor, mas compartilha alguma infraestrutura de rede com outros locatários.

+ O **Standard** é executado em computadores dedicados com mais capacidade de armazenamento e processamento em todos os níveis. O Standard é fornecido em quatro níveis: S1, S2, S3 e S3 HD.

+ O **armazenamento otimizado** é executado em computadores dedicados com mais armazenamento total, largura de banda de armazenamento e memória do que o **padrão**. O armazenamento otimizado vem em dois níveis: L1 e L2

> [!NOTE]
> A partir de 1º de julho, todas as camadas estão geralmente disponíveis, incluindo a camada de armazenamento otimizado. Todos os preços podem ser encontrados na página de [detalhes de preços](https://azure.microsoft.com/pricing/details/search/) .

  S3 High densidade (S3 HD) é projetado para cargas de trabalho específicas: [multilocação](search-modeling-multitenant-saas-applications.md) e grandes quantidades de índices pequenos (1 milhão documentos por índice, 3000 índices por serviço). Essa camada não fornece o [recurso de indexador](search-indexer-overview.md). No S3 HD, a ingestão de dados deve aproveitar a abordagem de push, usando chamadas à API para enviar dados de origem para o índice. 

> [!NOTE]
> Um serviço é provisionado em uma camada específica. As camadas de salto para obter capacidade envolvem o provisionamento de um novo serviço (não há nenhuma atualização in-loco). Para obter mais informações, consulte [escolher uma SKU ou camada](search-sku-tier.md). Para saber mais sobre como ajustar a capacidade em um serviço que você já provisionou, consulte [dimensionar níveis de recursos para cargas de trabalho de consulta e indexação](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Limites da subscrição
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Limites de armazenamento
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Limites de índice

| Recurso | Gratuito | &nbsp;básica <sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | ERROS DE L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Índices máximos |3 |5 ou 15 |50 |200 |200 |1000 por partição ou 3000 por serviço |10 |10 |
| Máximo de campos simples por índice |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Máximo de campos de coleta complexos por índice |40 |40 |40 |40 |40 |40 |40 |40 |
| Máximo de elementos em todas as coleções complexas por documento&nbsp;<sup>2</sup> |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Profundidade máxima de campos complexos |10 |10 |10 |10 |10 |10 |10 |10 |
| [Sugestores](https://docs.microsoft.com/rest/api/searchservice/suggesters) máximos por índice |1 |1 |1 |1 |1 |1 |1 |1 |
| Máximo de [perfis de Pontuação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) por índice |100 |100 |100 |100 |100 |100 |100 |100 |
| Máximo de funções por perfil |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> os serviços básicos criados antes de dezembro de 2017 têm limites mais baixos (5 em vez de 15) em índices. A camada básica é a única SKU com um limite inferior de 100 campos por índice.

<sup>2</sup> ter um número muito grande de elementos em coleções complexas por documento atualmente causa alta utilização do armazenamento. Este é um problema conhecido. Enquanto isso, um limite de 3000 é uma associação superior segura para todas as camadas de serviço. Esse limite é imposto somente para operações de indexação que utilizam a versão da API GA (geralmente disponível) que dá suporte a campos de tipo complexo (`2019-05-06`) em diante. Para não interromper os clientes que podem estar usando versões anteriores da API de visualização (que dão suporte a campos de tipo complexo), não iremos impor esse limite para operações de indexação que usam essas versões da API de visualização. Observe que as versões da API de visualização não devem ser usadas para cenários de produção e é altamente recomendável que os clientes migrem para a versão mais recente da API GA.

<a name="document-limits"></a>

## <a name="document-limits"></a>Limites de documentos 

A partir de outubro de 2018, não há mais nenhum limite de documento para nenhum novo serviço criado em qualquer camada faturável (básico, S1, S2, S3, S3 HD) em qualquer região. Embora a maioria das regiões tenha tido contagens de documentos ilimitadas desde novembro/dezembro de 2017, havia algumas regiões que continuaram a impor os limites de documentos após essa data. Dependendo de quando e onde você criou um serviço de pesquisa, você pode estar executando um serviço que ainda está sujeito a limites de documentos.

Para determinar se o serviço tem limites de documento, use a [API REST obter estatísticas de serviço](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics). Os limites de documento são refletidos na resposta, com `null` indicando que não há limites.

> [!NOTE]
> Embora não haja nenhum limite de documento específico de SKU, cada índice ainda está sujeito a um limite de segurança máximo para garantir a estabilidade do serviço. Esse limite vem do Lucene. Cada documento do Azure Pesquisa Cognitiva é indexado internamente como um ou mais documentos do Lucene. O número de documentos Lucene por documento de pesquisa depende do número total de elementos em campos de coleção complexos. Cada elemento é indexado como um documento Lucene separado. Por exemplo, um documento com 3 elementos em um campo de coleção complexo será indexado como quatro documentos Lucene-1 para o documento em si e 3 para os elementos. O número máximo de documentos Lucene é de aproximadamente 25.000.000.000 por índice.

### <a name="regions-previously-having-document-limits"></a>Regiões com limites de documentos anteriormente

Se o portal indicar um limite de documento, o serviço foi criado antes de 2017 ou foi criado em um data center usando clusters de menor capacidade para hospedar os serviços de Pesquisa Cognitiva do Azure:

+ Leste da Austrália
+ Ásia Leste
+ Índia Central
+ Oeste do Japão
+ E.U.A. Centro-Oeste

Para serviços sujeitos a limites de documentos, os limites máximos a seguir se aplicam:

|  Gratuito | Basic | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10,000 |1&nbsp;milhão |15 milhões por partição ou 180 milhões por serviço |60 milhões por partição ou 720 milhões por serviço |120 milhões por partição ou 1,4 biliões por serviço |1 milhão por índice ou 200 milhões por partição |

Se o serviço tiver limites que estão bloqueando você, crie um novo serviço e Republique todo o conteúdo para esse serviço. Não há nenhum mecanismo para reprovisionar o serviço diretamente em um novo hardware nos bastidores.

> [!Note] 
> Para os serviços de alta densidade S3 criados após o final de 2017, o documento 200 milhões por partição foi removido, mas o documento 1 milhão por limite de índice permanece.


### <a name="document-size-limits-per-api-call"></a>Limites de tamanho de documento por chamada à API

O tamanho máximo do documento ao chamar uma API de índice é de aproximadamente 16 megabytes.

O tamanho do documento é, na verdade, um limite no tamanho do corpo da solicitação da API do índice. Como você pode passar um lote de vários documentos para a API de índice de uma só vez, o limite de tamanho depende Realistamente de quantos documentos estão no lote. Para um lote com um único documento, o tamanho máximo do documento é 16 MB de JSON.

Para manter o tamanho do documento inativo, lembre-se de excluir dados não consultáveis da solicitação. Imagens e outros dados binários não são diretamente consultáveis e não devem ser armazenados no índice. Para integrar dados não consultáveis nos resultados da pesquisa, defina um campo não pesquisável que armazena uma referência de URL para o recurso.

## <a name="indexer-limits"></a>Limites do indexador

Existem tempos de execução máximos para fornecer balanceamento e estabilidade ao serviço como um todo, mas conjuntos de dados maiores podem precisar de mais tempo de indexação do que o máximo permitido. Se um trabalho de indexação não puder ser concluído dentro do tempo máximo permitido, tente executá-lo em um agendamento. O Agendador mantém o controle do status de indexação. Se um trabalho de indexação agendado for interrompido por algum motivo, o indexador poderá escolher onde ele parou na próxima execução agendada.


| Recurso | Gratuito&nbsp;<sup>1</sup> | &nbsp;básica <sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |ERROS DE L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Indexadores máximos |3 |5 ou 15|50 |200 |200 |N/A |10 |10 |
| Origens de dados máximas |3 |5 ou 15 |50 |200 |200 |N/A |10 |10 |
| Máximo de habilidades <sup>4</sup> |3 |5 ou 15 |50 |200 |200 |N/A |10 |10 |
| Carga de indexação máxima por invocação |10.000 documentos |Limitado apenas por documentos máximos |Limitado apenas por documentos máximos |Limitado apenas por documentos máximos |Limitado apenas por documentos máximos |N/A |Sem limite |Sem limite |
| Agenda mínima | 5 minutos |5 minutos |5 minutos |5 minutos |5 minutos |5 minutos |5 minutos | 5 minutos |
| Tempo máximo de execução <sup>5</sup> | 1-3 minutos |24 horas |24 horas |24 horas |24 horas |N/A  |24 horas |24 horas |
| Tempo máximo de execução para habilidades de pesquisa cognitiva ou indexação de blob com análise de imagem <sup>5</sup> | 3-10 minutos |2 horas |2 horas |2 horas |2 horas |N/A  |2 horas |2 horas |
| Indexador de blob: tamanho máximo do blob, MB |16 |16 |128 |256 |256 |N/A  |256 |256 |
| Indexador de blob: máximo de caracteres de conteúdo extraídos de um blob |32,000 |64,000 |4&nbsp;milhão |8&nbsp;milhões |16 milhões&nbsp; |N/A |4&nbsp;milhão |4&nbsp;milhão |

<sup>1</sup> os serviços gratuitos têm o tempo máximo de execução do indexador de 3 minutos para fontes de BLOB e 1 minuto para todas as outras fontes de dados. Para a indexação de ia que chama serviços cognitivas, os serviços gratuitos são limitados a 20 transações gratuitas por dia, em que uma transação é definida como um documento que passa com êxito pelo pipeline de enriquecimento.

<sup>2</sup> os serviços básicos criados antes de dezembro de 2017 têm limites mais baixos (5 em vez de 15) em indexadores, fontes de dados e habilidades.

<sup>3</sup> os serviços de HD S3 não incluem suporte ao indexador.

<sup>4</sup> máximo de 30 habilidades por habilidade.

<sup>5</sup> as cargas de trabalho de pesquisa cognitiva e a análise de imagem na indexação de blob do Azure têm tempos de execução menores do que a indexação de texto regular. A análise de imagem e o processamento de linguagem natural são computacionalmente intensivas e consomem quantidades desproporcionais de capacidade de processamento disponível. O tempo de execução foi reduzido para fornecer a outros trabalhos na fila uma oportunidade de execução.  

> [!NOTE]
> Conforme indicado nos [limites de índice](#index-limits), os indexadores também impõem o limite superior de 3000 elementos em todas as coleções complexas por documento, começando com a versão mais recente da API GA que dá suporte a tipos complexos (`2019-05-06`) em diante. Isso significa que, se você criou o indexador com uma versão de API anterior, não estará sujeito a esse limite. Para preservar a compatibilidade máxima, um indexador criado com uma versão de API anterior e, em seguida, atualizado com uma versão de API `2019-05-06` ou posterior, ainda será **excluído** dos limites. Os clientes devem estar cientes do impacto adverso de ter coleções complexas muito grandes (como indicado anteriormente) e é altamente recomendável criar novos indexadores com a versão mais recente da API GA.

## <a name="synonym-limits"></a>Limites de sinônimo

O número máximo de mapas de sinônimos permitido varia de acordo com o tipo de preço. Cada regra pode ter até 20 expansões, em que uma expansão é um termo equivalente. Por exemplo, dado "gato", associação com "Kitty", "felinos" e "Felis" (o genus para gatos) contaria como 3 expansões.

| Recurso | Gratuito | Basic | S1 | S2 | S3 | S3-HD |L1 | ERROS DE L2 |
| -------- | -----|------ |----|----|----|-------|---|----|
| Máximo de mapas de sinônimo |3 |3|5 |10 |20 |20 | 10 | 10 |
| Número máximo de regras por mapa |5000 |20000|20000 |20000 |20000 |20000 | 20000 | 20000  |

## <a name="queries-per-second-qps"></a>Consultas por segundo (QPS)

As estimativas de QPS devem ser desenvolvidas de forma independente por cada cliente. O tamanho e a complexidade do índice, o tamanho e a complexidade da consulta e a quantidade de tráfego são os principais determinantes de QPS. Não há como oferecer estimativas significativas quando esses fatores são desconhecidos.

As estimativas são mais previsíveis quando calculadas em serviços em execução em recursos dedicados (camadas básica e Standard). Você pode estimar QPS mais de mais de acordo porque tem controle sobre mais parâmetros. Para obter orientação sobre como abordar a estimativa, consulte [desempenho e otimização do Azure pesquisa cognitiva](search-performance-optimization.md).

Para as camadas de armazenamento otimizado, você deve esperar uma taxa de transferência de consulta inferior e uma latência mais alta do que as camadas padrão.  A metodologia para estimar o desempenho da consulta que você experimentará é a mesma das camadas padrão.

## <a name="data-limits-ai-enrichment"></a>Limites de dados (enriquecimento de ia)

Um pipeline de enriquecimento de [IA](cognitive-search-concept-intro.md) que faz chamadas para um recurso De SMS Analytics para reconhecimento de [entidades,](cognitive-search-skill-entity-recognition.md)extração de [frases-chave,](cognitive-search-skill-keyphrases.md) [análise de sentimentos,](cognitive-search-skill-sentiment.md) [deteção de linguagem,](cognitive-search-skill-language-detection.md)e [deteção de PII](cognitive-search-skill-pii-detection.md) está sujeito a limites de dados. O tamanho máximo de um registro deve ser de 50.000 caracteres conforme medido por [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Se você precisar dividir seus dados antes de enviá-los para o analisador de sentimentos, use a [habilidade de divisão de texto](cognitive-search-skill-textsplit.md).

## <a name="throttling-limits"></a>Limites de limitação

As solicitações de consulta e indexação de pesquisa são limitadas conforme o sistema se aproxima da capacidade de pico. A limitação se comporta de forma diferente para APIs diferentes. As APIs de consulta (pesquisa/sugestão/preenchimento automático) e as APIs de indexação são restringidas dinamicamente com base na carga do serviço. As APIs de índice têm limites de taxa de solicitação estáticos. 

Limites de solicitação de taxa estática para operações relacionadas a um índice:

+ Listar índices (GET/indexes): 5 por segundo por unidade de pesquisa
+ Obter índice (GET/Indexes/myindex): 10 por segundo por unidade de pesquisa
+ CREATE INDEX (POST/indexes): 12 por minuto por unidade de pesquisa
+ Criar ou atualizar índice (PUT/Indexes/myindex): 6 por segundo por unidade de pesquisa
+ Excluir índice (excluir/Indexes/myindex): 12 por minuto por unidade de pesquisa 

## <a name="api-request-limits"></a>Limites de solicitação de API
* Máximo de 16 MB por solicitação <sup>1</sup>
* Comprimento máximo da URL de 8 KB
* Máximo de 1000 documentos por lote de carregamentos de índice, mesclagens ou exclusões
* Máximo de 32 campos na cláusula $orderby
* O tamanho máximo do termo de pesquisa é 32.766 bytes (32 KB menos 2 bytes) de texto codificado em UTF-8

<sup>1</sup> no Azure pesquisa cognitiva, o corpo de uma solicitação está sujeito a um limite superior de 16 MB, impondo um limite prático no conteúdo de campos individuais ou coleções que, de outra forma, não são restritas por limites teóricos (consulte [tipos de dados com suporte](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) para obter mais informações sobre a composição e restrições de campo).

## <a name="api-response-limits"></a>Limites de resposta da API
* Máximo de 1000 documentos retornados por página de resultados da pesquisa
* Máximo de 100 sugestões retornadas por solicitação de API de sugestão

## <a name="api-key-limits"></a>Limites de chave de API
As chaves de API são usadas para autenticação de serviço. Existem dois tipos. As chaves de administração são especificadas no cabeçalho da solicitação e concedem acesso completo de leitura/gravação ao serviço. As chaves de consulta são somente leitura, especificadas na URL e normalmente distribuídas para aplicativos cliente.

* Máximo de 2 chaves de administração por serviço
* Máximo de 50 chaves de consulta por serviço
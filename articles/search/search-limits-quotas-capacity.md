---
title: Limites de serviço para camadas e SKUs-Azure Search
description: Limites de serviço usados para o planejamento de capacidade e limites máximos de solicitações e respostas para Azure Search.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: heidist
ms.openlocfilehash: fd65bb134d9057246a1b8c5cc2986e979713d20b
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327154"
---
# <a name="service-limits-in-azure-search"></a>Limites de serviço no Azure Search
Os limites máximos de armazenamento, cargas de trabalho e quantidades de índices, documentos e outros objetos dependem de você [provisionar Azure Search](search-create-service-portal.md) nos tipos de preço **gratuito**, **básico**, **padrão**ou **otimizado para armazenamento** .

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

| Resource | Livre | Básico&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | ERROS DE L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Índices máximos |3 |5 ou 15 |50 |200 |200 |1000 por partição ou 3000 por serviço |10 |10 |
| Máximo de campos simples por índice |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Máximo de campos de coleta complexos por índice |40 |40 |40 |40 |40 |40 |40 |40 |
| Máximo de elementos em todas as coleções complexas por documento |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Profundidade máxima de campos complexos |10 |10 |10 |10 |10 |10 |10 |10 |
| [Sugestores](https://docs.microsoft.com/rest/api/searchservice/suggesters) máximos por índice |1 |1 |1 |1 |1 |1 |1 |1 |
| Máximo de [perfis de Pontuação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) por índice |100 |100 |100 |100 |100 |100 |100 |100 |
| Máximo de funções por perfil |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> os serviços básicos criados antes de dezembro de 2017 têm limites mais baixos (5 em vez de 15) em índices. A camada básica é a única SKU com um limite inferior de 100 campos por índice.

<a name="document-limits"></a>

## <a name="document-limits"></a>Limites de documentos 

A partir de outubro de 2018, não há mais nenhum limite de documento<sup>1</sup> para qualquer novo serviço criado em qualquer camada faturável (básico, S1, S2, S3, S3 HD) em qualquer região. Embora a maioria das regiões tenha tido contagens de documentos ilimitadas desde novembro/dezembro de 2017, havia cinco regiões que continuaram a impor limites de documentos. Dependendo de quando e onde você criou um serviço de pesquisa, você pode estar executando um serviço que ainda está sujeito a limites de documentos.

Para determinar se o serviço tem limites de documento, verifique o bloco uso na página Visão geral do seu serviço. As contagens de documentos são ilimitadas ou estão sujeitas a um limite com base na camada.

  ![Mosaico utilização](media/search-limits-quotas-capacity/portal-usage-tile.png)

<sup>1</sup> mesmo que não haja nenhum limite de documento específico de SKU, cada índice ainda está sujeito a um limite de segurança máximo para garantir a estabilidade do serviço. Esse limite vem do Lucene. Cada documento de Azure Search é indexado internamente como um ou mais documentos do Lucene. O número de documentos do Lucene por documento do Azure Search depende do número total de elementos em campos de coleção complexos. Cada elemento é indexado como um documento Lucene separado. Por exemplo, um documento com 3 elementos em um campo de coleção complexo será indexado como quatro documentos Lucene-1 para o documento em si e 3 para os elementos. O número máximo de documentos Lucene é de aproximadamente 25.000.000.000 por índice.

### <a name="regions-previously-having-document-limits"></a>Regiões com limites de documentos anteriormente

Se o portal indicar um limite de documento, o serviço foi criado antes de 2017 ou foi criado em um data center usando clusters de menor capacidade para hospedar serviços de Azure Search:

+ Leste da Austrália
+ Ásia Oriental
+ Índia Central
+ Oeste do Japão
+ EUA Centro-Oeste

Para serviços sujeitos a limites de documentos, os limites máximos a seguir se aplicam:

|  Livre | Básica | S1 | S2 | S3 | S3&nbsp;HD |
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


| Resource | Gratuito&nbsp;<sup>1</sup> | Básico&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |ERROS DE L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Indexadores máximos |3 |5 ou 15|50 |200 |200 |N/A |10 |10 |
| Origens de dados máximas |3 |5 ou 15 |50 |200 |200 |N/A |10 |10 |
| Máximo de habilidades <sup>4</sup> |3 |5 ou 15 |50 |200 |200 |N/A |10 |10 |
| Carga de indexação máxima por invocação |10.000 documentos |Limitado apenas por documentos máximos |Limitado apenas por documentos máximos |Limitado apenas por documentos máximos |Limitado apenas por documentos máximos |N/A |Sem limite |Sem limite |
| Agenda mínima | 5 minutos |5 minutos |5 minutos |5 minutos |5 minutos |5 minutos |5 minutos | 5 minutos |
| Tempo máximo de execução <sup>5</sup> | 1-3 minutos |24 horas |24 horas |24 horas |24 horas |N/A  |24 horas |24 horas |
| Tempo máximo de execução para habilidades de pesquisa cognitiva ou indexação de blob com análise de imagem <sup>5</sup> | 3-10 minutos |Duas horas |Duas horas |Duas horas |Duas horas |N/A  |Duas horas |Duas horas |
| Indexador de blob: tamanho máximo do blob, MB |16 |16 |128 |256 |256 |N/A  |256 |256 |
| Indexador de blob: máximo de caracteres de conteúdo extraídos de um blob |32,000 |64,000 |4&nbsp;milhões |4&nbsp;milhões |4&nbsp;milhões |N/A |4&nbsp;milhões |4&nbsp;milhões |

<sup>1</sup> os serviços gratuitos têm o tempo máximo de execução do indexador de 3 minutos para fontes de BLOB e 1 minuto para todas as outras fontes de dados. Para a indexação de ia que chama serviços cognitivas, os serviços gratuitos são limitados a 20 transações gratuitas por dia, em que uma transação é definida como um documento que passa com êxito pelo pipeline de enriquecimento.

<sup>2</sup> os serviços básicos criados antes de dezembro de 2017 têm limites mais baixos (5 em vez de 15) em indexadores, fontes de dados e habilidades.

<sup>3</sup> os serviços de HD S3 não incluem suporte ao indexador.

<sup>4</sup> máximo de 30 habilidades por habilidade.

<sup>5</sup> as cargas de trabalho de pesquisa cognitiva e a análise de imagem na indexação de blob do Azure têm tempos de execução menores do que a indexação de texto regular. A análise de imagem e o processamento de linguagem natural são computacionalmente intensivas e consomem quantidades desproporcionais de capacidade de processamento disponível. O tempo de execução foi reduzido para fornecer a outros trabalhos na fila uma oportunidade de execução.  

## <a name="synonym-limits"></a>Limites de sinônimo

O número máximo de mapas de sinônimos permitido varia de acordo com o tipo de preço. Cada regra pode ter até 20 expansões, em que uma expansão é um termo equivalvent. Por exemplo, dado "gato", associação com "Kitty", "felinos" e "Felis" (o genus para gatos) contaria como 3 expansões.

| Resource | Livre | Básica | S1 | S2 | S3 | S3-HD |L1 | ERROS DE L2 |
| -------- | -----|------ |----|----|----|-------|---|----|
| Máximo de mapas de sinônimo |3 |3|5 |10 |20 |20 | 10 | 10 |
| Número máximo de regras por mapa |5000 |20000|20000 |20000 |20000 |20000 | 20000 | 20000  |

## <a name="queries-per-second-qps"></a>Consultas por segundo (QPS)

As estimativas de QPS devem ser desenvolvidas de forma independente por cada cliente. O tamanho e a complexidade do índice, o tamanho e a complexidade da consulta e a quantidade de tráfego são os principais determinantes de QPS. Não há como oferecer estimativas significativas quando esses fatores são desconhecidos.

As estimativas são mais previsíveis quando calculadas em serviços em execução em recursos dedicados (camadas básica e Standard). Você pode estimar QPS mais de mais de acordo porque tem controle sobre mais parâmetros. Para obter orientação sobre como abordar a estimativa, consulte [Azure Search desempenho e otimização](search-performance-optimization.md).

Para as camadas de armazenamento otimizado, você deve esperar uma taxa de transferência de consulta inferior e uma latência mais alta do que as camadas padrão.  A metodologia para estimar o desempenho da consulta que você experimentará é a mesma das camadas padrão.

## <a name="data-limits-cognitive-search"></a>Limites de dados (pesquisa cognitiva)

Um [pipeline de pesquisa cognitiva](cognitive-search-concept-intro.md) que faz chamadas para um recurso de análise de texto para [reconhecimento de entidade](cognitive-search-skill-entity-recognition.md), [extração de frases-chave](cognitive-search-skill-keyphrases.md), análise de [sentimentos](cognitive-search-skill-sentiment.md)e [detecção de idioma](cognitive-search-skill-language-detection.md) está sujeito a limites de dados. O tamanho máximo de um registro deve ser de 50.000 caracteres conforme medido por [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Se você precisar dividir seus dados antes de enviá-los para o analisador de sentimentos, use a [habilidade de divisão de texto](cognitive-search-skill-textsplit.md).

## <a name="api-request-limits"></a>Limites de solicitação de API
* Máximo de 16 MB por solicitação <sup>1</sup>
* Comprimento máximo da URL de 8 KB
* Máximo de 1000 documentos por lote de carregamentos de índice, mesclagens ou exclusões
* Máximo de 32 campos na cláusula $orderby
* O tamanho máximo do termo de pesquisa é 32.766 bytes (32 KB menos 2 bytes) de texto codificado em UTF-8

<sup>1</sup> em Azure Search, o corpo de uma solicitação está sujeito a um limite superior de 16 MB, impondo um limite prático no conteúdo de campos individuais ou coleções que não são restritas por limites teóricos (consulte tipos de [dados com suporte](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) para obter mais informações sobre composição e restrições de campo).

## <a name="api-response-limits"></a>Limites de resposta da API
* Máximo de 1000 documentos retornados por página de resultados da pesquisa
* Máximo de 100 sugestões retornadas por solicitação de API de sugestão

## <a name="api-key-limits"></a>Limites de chave de API
As chaves de API são usadas para autenticação de serviço. Existem dois tipos. As chaves de administração são especificadas no cabeçalho da solicitação e concedem acesso completo de leitura/gravação ao serviço. As chaves de consulta são somente leitura, especificadas na URL e normalmente distribuídas para aplicativos cliente.

* Máximo de 2 chaves de administração por serviço
* Máximo de 50 chaves de consulta por serviço

---
title: Limites de serviço para escalões e skus - Azure Search
description: Os limites de serviço utilizados para planeamento da capacidade e limites máximos em solicitações e respostas para o Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: b97c84a7a5d7732c8c895fd3074734762e5e040c
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780410"
---
# <a name="service-limits-in-azure-search"></a>Limites de serviço do Azure Search
Máximo limita-se no armazenamento, cargas de trabalho e as quantidades de índices, documentos, e outros objetos dependem se [aprovisionar o Azure Search](search-create-service-portal.md) na **gratuito**, **básica**, ou **Padrão** escalões de preço.

+ **Gratuito** é um serviço partilhado de multi-inquilino que vem com a sua subscrição do Azure.

+ **Básico** fornece recursos de computação dedicados para cargas de trabalho de produção em escala mais pequena.

+ **Padrão** é executado em máquinas dedicadas com mais capacidade de armazenamento e processamento em todos os níveis. Standard é fornecido em quatro níveis: S1, S2, S3 e S3 HD.

  S3 alta densidade (S3 HD) foi desenvolvida para cargas de trabalho específicas: [multi-inquilinos](search-modeling-multitenant-saas-applications.md) e grandes quantidades de índices pequeno (um milhão de documentos por índice, três mil índices por serviço). Este escalão não fornece o [funcionalidade de indexador](search-indexer-overview.md). No S3 HD, ingestão de dados tem de aproveitar a abordagem de push, através de chamadas à API para enviar dados de origem para o índice. 

> [!NOTE]
> Um serviço é aprovisionado num escalão específico. Saltar camadas para obter a capacidade envolve o aprovisionamento de um novo serviço (não há nenhuma atualização no local). Para obter mais informações, consulte [escolher um SKU ou escalão](search-sku-tier.md). Para saber mais sobre como ajustar a capacidade de dentro de um serviço já provisionou, veja [Dimensionar níveis de recursos para consulta e indexação de cargas de trabalho](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Limites da subscrição
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Limites de armazenamento
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Limites de índice

| Recurso | Gratuito | Básica&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD |
| -------- | ---- | ------------------- | --- | --- | --- | --- |
| Índices máximos |3 |5 ou 15 |50 |200 |200 |1000 por partição ou 3000 por serviço |
| Campos máximos por índice |1000 |100 |1000 |1000 |1000 |1000 |
| Máximo [sugestores](https://docs.microsoft.com/rest/api/searchservice/suggesters) por índice |1 |1 |1 |1 |1 |1 |
| Máximo [perfis de classificação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) por índice |100 |100 |100 |100 |100 |100 |
| Funções máximas por perfil |8 |8 |8 |8 |8 |8 |

<sup>1</sup> criados após o final de 2017 têm um limite maior de 15 índices, indexadores e origens de dados, de serviços básicos. Os serviços que criou anteriormente têm 5. Escalão básico é o único SKU com um limite inferior de 100 campos por índice.

<a name="document-limits"></a>

## <a name="document-limits"></a>Limites do documento 

A partir de Outubro de 2018, já não existem limites de documento para qualquer serviço novo, criado em qualquer escalão a cobrar (Basic, S1, S2, S3, S3 HD) em qualquer região. Embora tenha tido a maioria das regiões contagem de documentos ilimitado desde Novembro/Dezembro de 2017, existiam cinco regiões que continuaram a impor limites de documento. Dependendo de quando e onde criou um serviço de pesquisa, pode estar em execução um serviço que é ainda, sujeito aos limites do documento.

Para determinar se o seu serviço tem limites de documento, verifique no mosaico na página de descrição geral do seu serviço. Contagens de documentos estão ilimitado ou sujeita a um limite com base na camada.

  ![Mosaico utilização](media/search-limits-quotas-capacity/portal-usage-tile.png)

### <a name="regions-previously-having-document-limits"></a>Regiões anteriormente com limites de documento

Se o portal indica um limite de documento, o serviço ou foi criado antes de final de 2017 ou foi criado no Centro de dados com clusters de baixa capacidade para alojar serviços do Azure Search:

+ Leste da Austrália
+ Ásia Oriental
+ Índia Central
+ Oeste do Japão
+ EUA Centro-Oeste

Para os serviços sujeitos a limites de documento, aplicam-se aos limites máximos seguintes:

|  Gratuito | Básica | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10,000 |1 milhão |15 milhões por partição ou 180 milhões por serviço |60 milhões por partição ou 720 milhões por serviço |120 milhões por partição ou 1,4 biliões por serviço |1 milhão por índice ou 200 milhões por partição |

Se o seu serviço tem limites que estão a bloquear, criar um novo serviço e, em seguida, voltar a publicar todo o conteúdo para esse serviço. Não existe nenhum mecanismo para reprovisionamento forma totalmente integrada o seu serviço em hardware novo em segundo plano.

> [!Note] 
> Para os serviços de S3 alta densidade criados após o final de 2017, foi removido o documento de 200 milhões por partição, mas o documento de 1 milhão por índice limite permanece.


### <a name="document-size-limits-per-api-call"></a>Limites de tamanho do documento por chamada de API

O tamanho máximo do documento ao chamar uma API de índice é aproximadamente 16 megabytes.

Tamanho do documento é realmente um limite no tamanho do corpo do pedido de API do índice. Uma vez que pode passar um lote de vários documentos para a API de índice ao mesmo tempo, o limite de tamanho depende na verdade o número de documentos são no lote. Para um lote com um único documento, o tamanho do documento máxima é 16 MB de JSON.

Para manter o tamanho do documento para baixo, lembre-se de excluir os dados não consultável no pedido. Imagens e outros dados binários não são diretamente consultáveis e não devem ser armazenados no índice. Para integrar dados não consultável resultados da pesquisa, defina um campo não pesquisável, que armazena uma referência de URL para o recurso.

## <a name="indexer-limits"></a>Limites do indexador

Serviços básicos criados após o final de 2017 têm um limite maior de 15 índices, origens de dados, conjuntos de competências e indexadores.

Operações com muitos recursos, como a análise de imagem na indexação de Blobs do Azure ou de processamento de linguagem natural na pesquisa cognitiva, tem tempos mais curtos de execução máximos para que podem ser satisfeitos a outros trabalhos de indexação. Se uma tarefa de indexação não é possível concluir no tempo máximo permitido, tente executá-lo com base numa agenda. O scheduler mantém um registo do Estado de indexação. Se uma tarefa agendada de indexação é interrompida por qualquer motivo, o indexador possam começar onde pela última vez parou na próxima execução agendada.

| Recurso | Livre&nbsp;<sup>1</sup> | Básica&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|
| -------- | ----------------- | ----------------- | --- | --- | --- | --- |
| Indexadores máximos |3 |5 ou 15|50 |200 |200 |N/A |
| Origens de dados máximas |3 |5 ou 15 |50 |200 |200 |N/A |
| Conjuntos de competências máximos <sup>4</sup> |3 |5 ou 15 |50 |200 |200 |N/A |
| Carga máxima de indexação por invocação |10.000 documentos |Limitado apenas pelo documentos máximos |Limitado apenas pelo documentos máximos |Limitado apenas pelo documentos máximos |Limitado apenas pelo documentos máximos |N/A |
| Máximo de tempo de execução <sup>5</sup> | 1 a 3 minutos |24 horas |24 horas |24 horas |24 horas |N/A  |
| Máximo de tempo de blob de indexação com a análise de imagem ou de conjuntos de habilidades de pesquisa cognitiva execução <sup>5</sup> | 3 a 10 minutos |2 horas |2 horas |2 horas |2 horas |N/A  |
| Indexador de BLOBs: tamanho máximo de blob, MB |16 |16 |128 |256 |256 |N/A  |
| Indexador de BLOBs: número máximo de carateres de conteúdo extraído de um blob |32,000 |64,000 |4 milhões |4 milhões |4 milhões |N/A |

<sup>1</sup> serviços gratuitos têm indexador tempo de execução máximo de 3 minutos para as origens de blob e 1 minuto para todas as outras origens de dados.

<sup>2</sup> criados após o final de 2017 têm um limite maior de 15 índices, indexadores e origens de dados, de serviços básicos. Os serviços que criou anteriormente têm 5.

<sup>3</sup> serviços S3 HD não incluem o suporte de indexador.

<sup>4</sup> máximo de 30 habilidades por conjunto de capacidades.

<sup>5</sup> cargas de trabalho de pesquisa cognitiva e análise de imagem na indexação de Blobs do Azure têm tempos de execução mais curtos que a indexação de texto normal. Análise de imagem e processamento de linguagem natural são computacionalmente intensivas e consumam quantidades desproporcional de potência de processamento disponível. Tempo de execução foi reduzido para dar uma oportunidade para executar a outros trabalhos da fila.  

## <a name="queries-per-second-qps"></a>Consultas por segundo (QPS)

As estimativas de QPS devem ser desenvolvidas de forma independente por cada cliente. Tamanho do índice e o complexidade, o tamanho da consulta e a complexidade e a quantidade de tráfego são determinants primários de QPS. Não é possível oferecer estimativas significativas quando esses fatores são desconhecidos.

As estimativas são mais previsíveis quando calculado em serviços executados em recursos dedicados (escalões básico e Standard). Pode estimar QPS mais de perto, porque tem controle sobre mais dos parâmetros. Para obter orientações sobre como a estimativa de abordagem, consulte [desempenho de pesquisa do Azure e a otimização de](search-performance-optimization.md).

## <a name="data-limits-cognitive-search"></a>Limites de dados (pesquisa cognitiva)

R [pipeline de pesquisa cognitiva](cognitive-search-concept-intro.md) que faz chamadas para um recurso de análise de texto para [reconhecimento de entidades](cognitive-search-skill-entity-recognition.md), [extração de expressões da chave](cognitive-search-skill-keyphrases.md), [análise de sentimentos ](cognitive-search-skill-sentiment.md), e [deteção de idioma](cognitive-search-skill-language-detection.md) estão sujeitas a limites de dados. O tamanho máximo de um registo deve ser 50.000 carateres conforme medido pela `String.Length`. Se tiver de dividir os dados antes de os enviar para o analisador de sentimentos, utilize o [habilidade de divisão de texto](cognitive-search-skill-textsplit.md).

## <a name="api-request-limits"></a>Limites de pedido de API
* Máximo de 16 MB por pedido <sup>1</sup>
* Comprimento máximo do URL de 8 KB
* Documentos máximos de 1000 por lote de índice carrega, mescla ou elimina
* Campos máximos de 32 na cláusula $orderby
* Tamanho do termo de pesquisa máximo é 32,766 bytes (32 KB, menos de 2 bytes) do texto codificado UTF-8

<sup>1</sup> no Azure Search, o corpo de um pedido é sujeitos a um limite superior de 16 MB, impor um limite prático no conteúdo de campos individuais ou coleções que caso contrário, não estão limitadas por teóricos limites (ver [suporte de dados tipos de](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) para obter mais informações sobre a composição de campo e restrições).

## <a name="api-response-limits"></a>Limites de resposta da API
* Máximos 1000 documentos devolvidos por página de resultados da pesquisa
* Sugestões de 100 máximos devolvidos por pedido de API de sugestão

## <a name="api-key-limits"></a>Limites de chave de API
As chaves de API são utilizadas para autenticação de serviço. Existem dois tipos. Chaves de administração são especificadas no cabeçalho do pedido e concedem acesso completo de leitura / escrita para o serviço. Chaves de consulta são só de leitura, especificado no URL e, normalmente distribuídos para as aplicações cliente.

* Máximo de 2 chaves de administração por serviço
* Máximo de 50 chaves de consulta por serviço

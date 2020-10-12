---
title: Guia de migração para v2 da API text Analytics
titleSuffix: Azure Cognitive Services
description: Saiba como mover as suas aplicações para utilizar a versão 3 da API text Analytics.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 06/25/2020
ms.author: aahi
ms.openlocfilehash: 12c09ad8e1db3914263fcc864c9c2d09069d63a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85412588"
---
# <a name="migrate-to-version-3x-of-the-text-analytics-api"></a>Migrar para a versão 3.x da API text Analytics

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]

Se estiver a utilizar a versão 2.1 da API text Analytics, este artigo irá ajudá-lo a atualizar a sua aplicação para utilizar a versão 3.x. A versão 3.0 está geralmente disponível e introduz novas funcionalidades, como o reconhecimento de entidades nomeadas expandidas [(NER)](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) e [a versão de modelo.](concepts/model-versioning.md) Está também disponível uma versão de pré-visualização do v3.1 (v3.1-preview.x), que adiciona funcionalidades como [a mineração de opinião.](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) Os modelos utilizados na V2 não receberão futuras atualizações. 

#### <a name="sentiment-analysis"></a>[Análise de sentimentos](#tab/sentiment-analysis)

## <a name="feature-changes"></a>Alterações de recursos 

Análise de Sentimento na versão 2.1 devolve pontuações de sentimento entre 0 e 1 para cada documento enviado à API, com pontuações mais próximas de 1 indicando um sentimento mais positivo. Em vez disso, a versão 3 devolve rótulos de sentimento (como "positivo" ou "negativo") tanto para as frases como para o documento como um todo, e as suas pontuações de confiança associadas. 

## <a name="steps-to-migrate"></a>Passos para migrar

### <a name="rest-api"></a>API REST

Se a sua aplicação utilizar a API REST, atualize o seu ponto final de pedido para o ponto final v3 para análise de sentimento. Por exemplo: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment` . Também terá de atualizar a aplicação para utilizar as etiquetas de sentimento devolvidas na [resposta JSON](how-tos/text-analytics-how-to-sentiment-analysis.md#view-the-results). 

### <a name="client-libraries"></a>Bibliotecas de cliente

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

#### <a name="ner-and-entity-linking"></a>[Ligação NER e entidade](#tab/named-entity-recognition)

## <a name="feature-changes"></a>Alterações de recursos

> [!NOTE] 
> Atualmente, [as categorias de entidades V3](named-entity-types.md) só são devolvidas em texto inglês e espanhol. A API devolve os resultados da versão 2.1 para pedidos noutros idiomas, desde que sejam suportados na versão 2.1.

Na versão 2.1, a API text Analytics utiliza um ponto final para reconhecimento de entidades nomeadas (NER) e ligação de entidades. A versão 3 fornece deteção de entidades nomeadas expandidas e utiliza pontos finais separados para NER e entidade que ligam pedidos. A partir de v3.1-preview.1, NER pode ainda detetar informações pessoais `pii` e de `phi` saúde. 

## <a name="steps-to-migrate"></a>Passos para migrar

### <a name="rest-api"></a>API REST

Se a sua aplicação utilizar a API REST, atualize o seu ponto final de pedido para os pontos finais v3 para ligação NER e/ou entidade.

Ligar à Entidade
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

Também terá de atualizar a sua aplicação para utilizar as [categorias de entidades devolvidas](named-entity-types.md) na [resposta JSON.](how-tos/text-analytics-how-to-entity-linking.md#view-results)

### <a name="client-libraries"></a>Bibliotecas de cliente

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]


#### <a name="language-detection"></a>[Deteção linguística](#tab/language-detection)

## <a name="feature-changes"></a>Alterações de recursos 

A função de deteção de idiomas não mudou em V3 fora da versão endpoint, mas a resposta JSON irá conter `ConfidenceScore` em vez de `score` . V3 também devolve apenas uma única língua na saída. 

## <a name="steps-to-migrate"></a>Passos para migrar

### <a name="rest-api"></a>API REST

Se a sua aplicação utilizar a API REST, atualize o seu ponto final de pedido para o ponto final v3 para deteção de idiomas. Por exemplo: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages` . Também terá de atualizar a aplicação a utilizar `ConfidenceScore` em vez de na resposta `score` [JSON](how-tos/text-analytics-how-to-language-detection.md#step-3-view-the-results). 

### <a name="client-libraries"></a>Bibliotecas de cliente

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]


#### <a name="key-phrase-extraction"></a>[Extração de frase-chave](#tab/key-phrase-extraction)

## <a name="feature-changes"></a>Alterações de recursos 

A função de extração de frase-chave não foi alterada em V3 fora da versão endpoint.

## <a name="steps-to-migrate"></a>Passos para migrar

### <a name="rest-api"></a>API REST

Se a sua aplicação utilizar a API REST, atualize o ponto final do pedido para o ponto final v3 para a extração de frases-chave. Por exemplo: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`

### <a name="client-libraries"></a>Bibliotecas de cliente

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

---


## <a name="see-also"></a>Consulte também

* [Referência V2 da API analítica de texto](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/)
* [O que é a API de Análise de Texto](overview.md)
* [Suporte de idiomas](language-support.md)
* [Controlo de versões de modelos](concepts/model-versioning.md)
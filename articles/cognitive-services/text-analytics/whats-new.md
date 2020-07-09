---
title: Novidades na API de Análise de Texto
titleSuffix: Text Analytics - Azure Cognitive Services
description: Este artigo fornece-lhe informações sobre novos lançamentos e funcionalidades para o Azure Cognitive Services Text Analytics.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 3d9fb7f3fa01c06cc778e4062dd1d136e9c6e342
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86103403"
---
# <a name="whats-new-in-the-text-analytics-api"></a>O que há de novo na API de Análise de Texto?

A API text Analytics é atualizada numa base contínua. Para se manter atualizado com os recentes desenvolvimentos, este artigo fornece-lhe informações sobre novos lançamentos e funcionalidades.

## <a name="july-2020"></a>Julho de 2020

### <a name="text-analytics-for-health-container-public-gated-preview"></a>Análise de texto para portaria do recipiente de saúde visualização do portão

O recipiente Text Analytics for Health encontra-se agora em pré-visualização fechada pública, que permite extrair informações de textos não estruturados em língua inglesa em documentos clínicos como: formulários de admissão de pacientes, notas do médico, trabalhos de investigação e resumos de descarga. Atualmente, não será cobrado para text analytics para uso de recipientes de saúde. 

O recipiente oferece as seguintes características:

* Reconhecimento de Entidades Nomeadas
* Extração de relação
* Ligação de entidades
* Negação


> [!div class="nextstepaction"]
> [Saiba mais sobre o texto Analytics for Health container](how-tos/text-analytics-for-health.md)

## <a name="may-2020"></a>Maio de 2020

### <a name="text-analytics-api-v3-general-availability"></a>Disponibilidade Geral de API v3 API de Texto

A API v3 de Análise de Texto está disponível geralmente com as seguintes atualizações:

* Versão modelo`2020-04-01`
* Novos [limites de dados](concepts/data-limits.md) para cada recurso
* Suporte [linguístico](language-support.md) atualizado para [análise de sentimento (SA) v3](how-tos/text-analytics-how-to-sentiment-analysis.md)
* Ponto final separado para ligação de entidades 
* Nova categoria de entidade "Address" em [Reconhecimento de Entidades Nomeadas (NER) v3](how-tos/text-analytics-how-to-entity-linking.md).
* Novas subcategorias em NER v3:
   * Localização - Geográfica
   * Localização - Estrutural
   * Organização - Bolsa de Valores
   * Organização - Médico
   * Organização - Esportes
   * Evento - Cultural
   * Evento - Natural
   * Evento - Esportes

Foram adicionadas as seguintes propriedades na resposta JSON:
   * `SentenceText`em Análise de Sentimento
   * `Warnings`para cada documento 

Os nomes das seguintes propriedades na resposta JSON foram alterados, se aplicável:

* `score` mudou de nome para `confidenceScore`
    * `confidenceScore`tem dois pontos decimais de precisão. 
* `type` mudou de nome para `category`
* `subtype` mudou de nome para `subcategory`

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]

> [!div class="nextstepaction"]
> [Saiba mais sobre text Analytics API v3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

### <a name="text-analytics-api-v31-public-preview"></a>API API de Análise de Texto v3.1 Visualização pública
   * Novo recurso de Análise de Sentimento - [Mineração de Opinião](how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)
   * Novo [filtro de `PII` domínio pessoal para](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) informações de saúde protegidas `PHI` ().

> [!div class="nextstepaction"]
> [Saiba mais sobre a API API v3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## <a name="february-2020"></a>Fevereiro de 2020

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>Suporte SDK para API API v3 Visualização pública de texto

Como parte da versão unificada do [Azure SDK,](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)a API v3 SDK text Analytics está agora disponível como pré-visualização pública para as seguintes linguagens de programação:
   * [C#](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript (Node.js)](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)
   
   > [!div class="nextstepaction"]
> [Saiba mais sobre Text Analytics API v3 SDK](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Nome de Reconhecimento de Entidade v3 visualização pública

Os tipos de entidades adicionais estão agora disponíveis no serviço de pré-visualização pública denominado NER v3 à medida que expandimos a deteção de entidades de informação geral e pessoal encontradas em texto. Esta atualização introduz [a versão modelo,](concepts/model-versioning.md) `2020-02-01` que inclui:

* Reconhecimento dos seguintes tipos gerais de entidades (apenas em inglês):
    * PersonType
    * Produto
    * Evento
    * Entidade Geopolítica (GPE) como subtipo na Localização
    * Habilidade

* Reconhecimento dos seguintes tipos de entidades de informação pessoal (apenas em inglês):
    * Pessoa
    * Organização
    * Idade como subtipo em Quantidade
    * Data como subtipo em DateTime
    * E-mail 
    * Número de telefone (apenas NOS)
    * URL
    * Endereço IP

> [!div class="nextstepaction"]
> [Saiba mais sobre o Reconhecimento de Entidades Nomeadas v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>Outubro de 2019

#### <a name="named-entity-recognition-ner"></a>Reconhecimento de Entidades Nomeadas (NER)

* Um [novo ponto final](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) para o reconhecimento de tipos de entidades de informação pessoal (apenas em inglês)

* Pontos finais separados para [reconhecimento de entidades](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) e [ligação de entidades.](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking)

* [Versão do modelo,](concepts/model-versioning.md) `2019-10-01` que inclui:
    * Deteção e categorização alargadas de entidades encontradas em texto. 
    * Reconhecimento dos seguintes novos tipos de entidades:
        * Número de telefone
        * Endereço IP

A ligação de entidade suporta inglês e espanhol. O suporte linguístico NER varia de acordo com o tipo de entidade.

#### <a name="sentiment-analysis-v3-public-preview"></a>Análise de Sentimento v3 visualização pública

* Um [novo ponto final](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) para analisar o sentimento.
* [Versão do modelo,](concepts/model-versioning.md) `2019-10-01` que inclui:

    * Melhorias significativas na precisão e detalhe da categorização e pontuação de texto da API.
    * Rotulagem automática para diferentes sentimentos em texto.
    * Análise de sentimento e saída no nível de documento e frase. 

Apoia o inglês ( ( ( ( japonês ), chinês simplificado ( , chinês tradicional ( ( ( ( ( ( ( ( ( ( ( ( ( ( ( ( ( ( ( `en` `ja` ) e alemão , e `zh-Hans` está `zh-Hant` `fr` `it` `es` `nl` `pt` `de` disponível nas seguintes regiões: `Australia East` , , , , , , , , , , , `Central Canada` , , , , , `Central US` , , , , , , , , `East Asia` e `East US` `East US 2` `North Europe` `Southeast Asia` `South Central US` `UK South` `West Europe` `West US 2` . 

> [!div class="nextstepaction"]
> [Saiba mais sobre a Análise de Sentimento v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Próximos passos

* [O que é a API de Análise de Texto?](overview.md)  
* [Cenários de utilizador acionáveis](text-analytics-user-scenarios.md)
* [Análise de sentimento](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Deteção linguística](how-tos/text-analytics-how-to-language-detection.md)
* [Reconhecimento de entidades](how-tos/text-analytics-how-to-entity-linking.md)
* [Extração de frase-chave](how-tos/text-analytics-how-to-keyword-extraction.md)

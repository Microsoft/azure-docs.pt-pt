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
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 162e60ac8d33dc5d1951a58b0a9643b668608d7b
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188808"
---
# <a name="whats-new-in-the-text-analytics-api"></a>O que há de novo na API de Análise de Texto?

A API text analytics é atualizada de forma contínua. Para se manter atualizado com os recentes desenvolvimentos, este artigo fornece-lhe informações sobre novos lançamentos e funcionalidades.

## <a name="february-2020"></a>fevereiro de 2020

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>Suporte SDK para API v3 Visualização pública de Análise de Texto

Como parte da versão unificada do [Azure SDK,](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)o Text Analytics API v3 SDK está agora disponível como pré-visualização pública para as seguintes linguagens de programação:
   * [C#](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript (Node.js)](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)

> [!div class="nextstepaction"]
> [Saiba mais sobre O Texto Analytics API v3 SDK](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Nomeado Reconhecimento de Entidade v3 pré-visualização pública

Os tipos de entidades adicionais estão agora disponíveis no serviço de pré-visualização pública de Reconhecimento de Entidades (NER) v3, à medida que expandimos a deteção de entidades de informação geral e pessoal encontradas em texto. Esta atualização introduz a [versão do modelo](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2020-02-01`, que inclui:

* Reconhecimento dos seguintes tipos de entidades gerais (apenas inglês):
    * PersonType
    * Produto
    * Evento
    * Entidade Geopolítica (GPE) como subtipo sob localização
    * Habilidade

* Reconhecimento dos seguintes tipos de entidades de informação pessoal (apenas inglês):
    * Pessoa
    * Organização
    * Idade como subtipo em quantidade
    * Data como subtipo em DataTime
    * Email 
    * Número de telefone (apenas nos EUA)
    * do IdP
    * Endereço IP

> [!div class="nextstepaction"]
> [Saiba mais sobre o Reconhecimento de Entidades Nomeadas v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>Outubro de 2019

#### <a name="named-entity-recognition-ner"></a>Reconhecimento de Entidades Nomeada (NER)

* Um [novo ponto final](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) para reconhecer tipos de entidades de informação pessoal (apenas inglês)

* Pontos finais separados para [reconhecimento de entidades](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) e [ligação de entidades.](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking)

* [Versão](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) modelo `2019-10-01`, que inclui:
    * Deteção e categorização alargada de entidades encontradas em texto. 
    * Reconhecimento dos seguintes novos tipos de entidades:
        * Número de telefone
        * Endereço IP

Entidade que liga o inglês e o espanhol. O suporte linguístico NER varia pelo tipo de entidade.

#### <a name="sentiment-analysis-v3-public-preview"></a>Análise de Sentimento v3 pré-visualização pública

* Um [novo ponto final](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) para analisar o sentimento.
* [Versão](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) modelo `2019-10-01`, que inclui:

    * Melhorias significativas na precisão e detalhe da categorização e pontuação de texto da API.
    * Rotulagem automática para diferentes sentimentos em texto.
    * Análise de sentimentoe saída a nível de documento e frase. 

Apoia o inglês (`en`), japonês (`ja`), chinês simplificado (`zh-Hans`), chinês tradicional (`zh-Hant`), francês (`fr`), italiano (`it`), espanhol (`es`), holandês (`nl`), português`pt`(`de`) e alemão ( ) e está disponível nas seguintes regiões: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe`e `West US 2`. 

> [!div class="nextstepaction"]
> [Saiba mais sobre a Análise de Sentimentos v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Passos seguintes

* [O que é a API de Análise de Texto?](overview.md)  
* [Exemplo de cenários de utilizador](text-analytics-user-scenarios.md)
* [Análise de sentimento](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Deteção de linguagem](how-tos/text-analytics-how-to-language-detection.md)
* [Reconhecimento de entidades](how-tos/text-analytics-how-to-entity-linking.md)
* [Extração de frase-chave](how-tos/text-analytics-how-to-keyword-extraction.md)

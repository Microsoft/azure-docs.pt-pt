---
title: O que há de novo na API de Análise de Texto
titleSuffix: Text Analytics - Azure Cognitive Services
description: Este artigo fornece informações sobre novas versões e recursos para os serviços cognitivas do Azure Análise de Texto.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: e3fbff7a5919d4d43656f3112cb24f2017191348
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086798"
---
# <a name="whats-new-in-the-text-analytics-api"></a>O que há de novo na API de Análise de Texto?

O API de Análise de Texto é atualizado em uma base contínua. Para se manter atualizado com os recentes desenvolvimentos, este artigo fornece informações sobre novas versões e recursos.

## <a name="named-entity-recognition-v3-public-preview---february-2020"></a>Nomeado Reconhecimento de Entidade v3 pré-visualização pública - fevereiro 2020

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

#### <a name="named-entity-recognition-ner"></a>Reconhecimento de entidade nomeada (NER)

* Um [novo ponto final](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) para reconhecer tipos de entidades de informação pessoal (apenas inglês)

* Pontos finais separados para [reconhecimento de entidades](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) e [ligação de entidades.](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking)

* [Versão](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) modelo `2019-10-01`, que inclui:
    * Deteção e categorização alargada de entidades encontradas em texto. 
    * Reconhecimento dos seguintes novos tipos de entidade:
        * Número de telefone
        * Endereço IP

A vinculação de entidades dá suporte a inglês e espanhol. O suporte ao idioma NER varia de acordo com o tipo de entidade.

#### <a name="sentiment-analysis-v3-public-preview"></a>Análise de Sentimento v3 pré-visualização pública

* Um [novo ponto final](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) para analisar o sentimento.
* [Versão](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) modelo `2019-10-01`, que inclui:

    * Melhorias significativas na precisão e detalhe da categorização e pontuação de texto da API.
    * Rotulagem automática para diferentes sentimentos no texto.
    * Análise de sentimentos e saída em um nível de documento e frase. 

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

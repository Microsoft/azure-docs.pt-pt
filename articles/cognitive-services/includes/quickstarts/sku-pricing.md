---
title: SKUs de serviço cognitivo e preços
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: cb066ecc1dc11985c0ccb0fa687a15d6b038b9fa
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89321613"
---
Consulte a lista de SKUs e informações sobre preços abaixo. 

#### <a name="multi-service"></a>Multi-serviço

| Serviço                    | Tipo                      |
|----------------------------|---------------------------|
| Vários serviços. Para mais informações, consulte a página [de preços.](https://azure.microsoft.com/pricing/details/cognitive-services/)            | `CognitiveServices`     |


#### <a name="vision"></a>Visão

| Serviço                    | Tipo                      |
|----------------------------|---------------------------|
| Imagem Digitalizada            | `ComputerVision`          |
| Visão Personalizada - Previsão | `CustomVision.Prediction` |
| Visão Personalizada - Treino   | `CustomVision.Training`   |
| Rostos                       | `Face`                    |
| Reconhecedor de Formato            | `FormRecognizer`          |
| Reconhecedor de Tinta Digital             | `InkRecognizer`           |

#### <a name="search"></a>Pesquisa

| Serviço            | Tipo                  |
|--------------------|-----------------------|
| Sugestão Automática do Bing   | `Bing.Autosuggest.v7` |
| Pesquisa Personalizada do Bing | `Bing.CustomSearch`   |
| Pesquisa de Entidades do Bing | `Bing.EntitySearch`   |
| Pesquisa do Bing        | `Bing.Search.v7`      |
| Verificação Ortográfica do Bing   | `Bing.SpellCheck.v7`  |

#### <a name="speech"></a>Voz

| Serviço            | Tipo                 |
|--------------------|----------------------|
| Serviços de Voz    | `SpeechServices`     |
| Reconhecimento de Voz | `SpeakerRecognition` |

#### <a name="language"></a>Linguagem

| Serviço            | Tipo                |
|--------------------|---------------------|
| Compreensão de formulários | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Análise de Texto     | `TextAnalytics`     |
| Tradução de Texto   | `TextTranslation`   |

#### <a name="decision"></a>Decisão

| Serviço           | Tipo               |
|-------------------|--------------------|
| Detetor de Anomalias  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizador      | `Personalizer`     |


#### <a name="pricing-tiers-and-billing"></a>Níveis de preços e faturação

Os níveis de preços (e o valor que recebe faturado) baseiam-se no número de transações que envia usando as suas informações de autenticação. Cada nível de preços especifica:
* número máximo de transações permitidas por segundo (TPS).
* funcionalidades de serviço ativadas dentro do nível de preços.
* custo para um número pré-refinado de transações. Ir acima deste número irá causar uma taxa extra, conforme especificado nos detalhes de [preços](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) do seu serviço.

> [!NOTE]
> Muitos dos Serviços Cognitivos têm um nível gratuito que pode usar para experimentar o serviço. Para utilizar o nível livre, utilize `F0` como SKU para o seu recurso.
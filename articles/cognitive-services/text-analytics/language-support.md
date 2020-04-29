---
title: Suporte linguístico - API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: 'Uma lista de línguas naturais suportadas pela API text Analytics. Este artigo explica quais as línguas suportadas para cada operação: análise de sentimentos, extração de frases-chave, deteção de linguagem e reconhecimento de entidades.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: c5a413a4fe8d9ac9b7aac59ca78cedc6d5a7a313
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79219282"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Suporte de língua e região para a API de Análise de Texto

Este artigo explica quais as línguas suportadas para cada operação: análise de sentimentos, extração de frases-chave, deteção de linguagem e reconhecimento de entidades nomeadas.

## <a name="language-detection"></a>Deteção de Idioma

A API text Analytics pode detetar uma vasta gama de línguas, variantes, dialetos e algumas línguas regionais/culturais.  Deteção de Idiomas devolve o "script" de uma língua. Por exemplo, para a frase "Tenho um `en` cão" vai voltar em vez de `en-US`. O único caso especial é o chinês, `zh_CHS` onde `zh_CHT` a capacidade de deteção de idiomas regressará ou se pode determinar o script dado o texto fornecido. Em situações em que um guião específico não pode `zh`ser identificado para um documento chinês, ele regressará simplesmente .

Não publicamos a lista exata de línguas para esta funcionalidade, mas pode detetar uma vasta gama de línguas, variantes, dialetos e algumas línguas regionais/culturais. 

Se tiver conteúdo expresso num idioma menos utilizado, pode tentar deteção de idiomas para ver se devolve um código. A resposta para línguas que `unknown`não podem ser detetadas é .

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Análise de Sentimentos, Extração de Frases-Chave e Reconhecimento de Entidades Nomeadas

Para análise de sentimentos, extração de frases-chave e reconhecimento de entidades, a lista de línguas apoiadas é mais seletiva à medida que os analisadores são refinados para acomodar as regras linguísticas de línguas adicionais. Em Nome nomeado Reconhecimento de Entidades v2, o suporte para todo o conjunto de tipos de [entidades](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) está atualmente limitado aos seguintes idiomas: 
* Inglês
* Simplificado chinês
* Francês
* Alemão
* Espanhol

Apenas `Person` `Location` as `Organization` entidades e entidades nomeadas são devolvidas para as outras línguas.

## <a name="language-list-and-status"></a>Lista de idiomas e estado

O suporte linguístico é inicialmente lançado em pré-visualização, graduando-se para o estado geralmente disponível (GA), independentemente uns dos outros e do serviço Text Analytics em geral. É possível que as línguas permaneçam na pré-visualização, mesmo quando a API text analytics transita para geralmente disponível.

> [!NOTE]
> Para um suporte linguístico detalhado para a pré-visualização pública de Reconhecimento de Entidades (NER) v3, consulte [os tipos de entidades nomeados.](named-entity-types.md)

| Idioma              | Código do idioma | Sentimento | Frases-chave | Reconhecimento de Entidades Nomeadas | Ligação de entidades |       Notas        |
|:----------------------|:-------------:|:---------:|:-----------:|:------------------------:|:--------------:|:------------------:|
| Árabe                |     `ar`      |           |             |           ✔\*           |                |                    |
| Checo                 |     `cs`      |           |             |           ✔\*           |                |                    |
| Simplificado chinês    |   `zh-hans`   |  ✔\*\*   |             |            ✔             |                | `zh`também aceite                   |
| Chinês-Tradicional   |   `zh-hant`   |  ✔\*\*   |             |                          |                |                    |
| Dinamarquês                |     `da`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Neerlandês                 |     `nl`      |   ✔\**   |      ✔      |           ✔\*           |                |                    |
| Inglês               |     `en`      |   ✔\**   |      ✔      |          ✔\*\*          |     ✔\**      |                    |
| Finlandês               |     `fi`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Francês                |     `fr`      |   ✔\**   |      ✔      |            ✔             |                |                    |
| Alemão                |     `de`      |   ✔\**   |      ✔      |            ✔             |                |                    |
| Grego                 |     `el`      |   ✔\*    |             |                          |                |                    |
| Húngaro             |     `hu`      |           |             |           ✔\*           |                |                    |
| Italiano               |     `it`      |   ✔\**   |      ✔      |           ✔\*           |                |                    |
| Japonês              |     `ja`      |   ✔\**   |      ✔      |           ✔\*           |                |                    |
| Coreano                |     `ko`      |   ✔\*\*  |      ✔      |           ✔\*           |                |                    |
| Norueguês (Bokmål)   |     `no`      |   ✔\*    |      ✔      |           ✔\*           |                | `nb`também aceite                   |
| Polaco                |     `pl`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Português (Portugal) |    `pt-PT`    |   ✔\**    |      ✔      |           ✔\*           |                | `pt`também aceite |
| Português (Brasil)   |    `pt-BR`    |           |      ✔      |           ✔\*           |                |                    |
| Russo               |     `ru`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Espanhol               |     `es`      |   ✔\**    |      ✔      |           ✔\*           |     ✔\**      |                    |
| Sueco               |     `sv`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Turco               |     `tr`      |   ✔\*    |             |           ✔\*           |                |                    |

\*O suporte linguístico está em pré-visualização

\** Também disponível nas pré-visualizações públicas de Reconhecimento de [Sentimentos v3](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis#sentiment-analysis-versions-and-features) e/ou Nomeados de Reconhecimento de [Entidades V3.](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

## <a name="see-also"></a>Consulte também

[Página de Documentação de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/)   
[Página de Produto dos Serviços Cognitivos](https://azure.microsoft.com/services/cognitive-services/)

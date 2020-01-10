---
title: Suporte ao idioma-API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: 'Uma lista de idiomas naturais com suporte pelo API de Análise de Texto. Este artigo explica quais idiomas têm suporte para cada operação: análise de sentimentos, extração de frases-chave, detecção de idioma e reconhecimento de entidade.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: fcf7051a631e74f2f6db918c7c861e32295ae9e8
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732642"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Suporte a idiomas e regiões para o API de Análise de Texto

Este artigo explica quais idiomas têm suporte para cada operação: análise de sentimentos, extração de frases-chave, detecção de idioma e reconhecimento de entidade nomeada.

## <a name="language-detection"></a>Deteção de Idioma

O API de Análise de Texto pode detectar uma ampla gama de linguagens, variantes, dialetos e alguns idiomas regionais/culturais.  Detecção de Idioma retorna o "script" de um idioma. Por exemplo, para a frase "Eu tenho um cachorro", ele retornará `en` em vez de `en-US`. O único caso especial é chinês, em que o recurso de detecção de idioma retornará `zh_CHS` ou `zh_CHT` se puder determinar o script fornecido pelo texto fornecido. Em situações em que um script específico não pode ser identificado para um documento chinês, ele retornará simplesmente `zh`.

Não publicamos a lista exata de idiomas para esse recurso, mas ele pode detectar uma ampla gama de linguagens, variantes, dialetos e alguns idiomas regionais/culturais. 

Se você tiver conteúdo expresso em uma linguagem usada com menos frequência, poderá tentar Detecção de Idioma para ver se ele retorna um código. A resposta para idiomas que não podem ser detectados é `unknown`.

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Reconhecimento de entidade Análise de Sentimento, Extração de Frases-chave e nomeada

Para análise de sentimentos, extração de frases-chave e reconhecimento de entidade, a lista de idiomas com suporte é mais seletiva, pois os analisadores são refinados para acomodar as regras linguísticas de idiomas adicionais. No reconhecimento de entidade nomeada v2, o suporte para o conjunto completo de [tipos de entidade](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) está atualmente limitado aos seguintes idiomas: 
* Português
* Chinês-simplificado
* Francês
* Alemão
* Espanhol

Somente as `Person`, `Location` e `Organization` entidades nomeadas são retornadas para os outros idiomas.

## <a name="language-list-and-status"></a>Lista de idiomas e status

O suporte a idiomas é inicialmente distribuído na visualização, graduando para o status de GA (disponibilidade geral), independentemente uns dos outros e do serviço de Análise de Texto geral. É possível que as linguagens permaneçam em visualização, mesmo que API de Análise de Texto transições para disponibilidade geral.

> [!NOTE]
> Para obter suporte de idioma detalhado para a visualização pública de reconhecimento de entidade nomeada (NER) v3, consulte [tipos de entidade nomeada](named-entity-types.md).

| Linguagem              | Código do idioma | Sentimento | Frases-chave | Reconhecimento de Entidades Nomeadas | Vinculação de entidade |       Notas        |
|:----------------------|:-------------:|:---------:|:-----------:|:------------------------:|:--------------:|:------------------:|
| Árabe                |     `ar`      |           |             |           ✔ \*           |                |                    |
| Checo                 |     `cs`      |           |             |           ✔ \*           |                |                    |
| Chinês-simplificado    |   `zh-hans`   |  ✔ \*\*   |             |            ✔             |                |                    |
| Chinês-tradicional   |   `zh-hant`   |  ✔ \*\*   |             |                          |                |                    |
| Dinamarquês                |     `da`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Neerlandês                 |     `nl`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| Português               |     `en`      |   ✔ \**   |      ✔      |          ✔ \*\*          |     ✔ \**      |                    |
| Finlandês               |     `fi`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Francês                |     `fr`      |   ✔ \**   |      ✔      |            ✔             |                |                    |
| Alemão                |     `de`      |   ✔ \**   |      ✔      |            ✔             |                |                    |
| Grego                 |     `el`      |   ✔ \*    |             |                          |                |                    |
| Húngaro             |     `hu`      |           |             |           ✔ \*           |                |                    |
| Italiano               |     `it`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| Japonês              |     `ja`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| Coreano                |     `ko`      |           |      ✔      |           ✔ \*           |                |                    |
| Norueguês (bokmål)   |     `no`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Polaco                |     `pl`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Português (Portugal) |    `pt-PT`    |   ✔\**    |      ✔      |           ✔ \*           |                | `pt` também aceito |
| Português (Brasil)   |    `pt-BR`    |           |      ✔      |           ✔ \*           |                |                    |
| Russo               |     `ru`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Espanhol               |     `es`      |   ✔\**    |      ✔      |           ✔ \*           |     ✔ \**      |                    |
| Sueco               |     `sv`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Turco               |     `tr`      |   ✔ \*    |             |           ✔ \*           |                |                    |

o suporte ao idioma \* está em versão prévia

\** também disponível no [análise de sentimento v3](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis#sentiment-analysis-versions-and-features) e/ou em visualizações públicas do [reconhecimento de entidade nomeada v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) .

## <a name="see-also"></a>Ver também

[Página de documentação de serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/)   
[Página de Produto dos Serviços Cognitivos](https://azure.microsoft.com/services/cognitive-services/)

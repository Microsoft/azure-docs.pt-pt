---
title: Suporte de idiomas - API de análise de texto
titleSuffix: Azure Cognitive Services
description: 'Uma lista de idiomas suportados pela API de análise de texto. Este artigo explica que idiomas são suportados para cada operação: análise de sentimentos, extração de expressões-chave, deteção de idioma e reconhecimento de entidades.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: aahi
ms.openlocfilehash: 704a1193eb47f9346900c6c8a003122c30c8ab44
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203976"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Suporte de idioma e região para a API de análise de texto

Este artigo explica que idiomas são suportados para cada operação: análise de sentimentos, extração de expressões-chave, deteção de idioma e o reconhecimento de entidades.

## <a name="language-detection"></a>Deteção de Idioma

A API de análise de texto pode detectar uma grande variedade de linguagens, variantes, dialetos e alguns idiomas regionais/cultural.  Deteção de idioma devolve "script" de um idioma. Por exemplo, para a frase "Tenho um cachorro" irá devolver `en` em vez de `en-US`. Caso apenas especial é chinês, onde irá devolver a capacidade de deteção de idioma `zh_CHS` ou `zh_CHT` se ele pode determinar o script tendo em conta o texto fornecido. Em situações em que um script específico não é possível identificar de um documento do chinês, irá devolver simplesmente `zh`.

Não publicamos a lista exata de idiomas para esta funcionalidade, mas ele pode detectar uma grande variedade de linguagens, variantes, dialetos e alguns idiomas regionais/cultural. 

Se tiver conteúdo expressado numa linguagem com menos frequência utilizada, pode tentar a deteção de idioma para ver se ele retorna um código. A resposta para idiomas que não puderem ser detetadas está `unknown`.

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Análise de sentimentos, extração de expressões-chave e o reconhecimento de entidades

Para a análise de sentimentos, extração de expressões-chave e reconhecimento de entidades, a lista de idiomas suportados é mais seletiva conforme os analisadores são refinados para acomodar as regras de idiomas adicionais. Suporte para o conjunto completo de [tipos de entidade](how-tos/text-analytics-how-to-entity-linking.md#supported-types-for-named-entity-recognition) está limitado a idiomas seguintes: 
* Português
* Chinês simplificado
* Francês
* Alemão
* Espanhol

Apenas os `Person`, `Location` e `Organization` entidades nomeadas são devolvidas para os outros idiomas.

## <a name="language-list-and-status"></a>Lista de idioma e o Estado

Suporte de idiomas é inicialmente lançado em pré-visualização, graduando para disponibilidade geral (GA) do Estado, independentemente uns dos outros e do serviço de análise de texto geral. É possível para idiomas permaneça na pré-visualização, mesmo durante as transições de API de análise de texto para disponibilidade geral.

| Idioma    | Código do idioma | Sentimento | Frases-chave | Reconhecimento de Entidades Nomeadas |   Notas  |
|:----------- |:-------------:|:---------:|:-----------:|:-----------:|:-----------:
| Árabe      | `ar`          |           |             | ✔ \*                     | |
| Checo       | `cs`          |           |             | ✔ \*                     | |
| Chinês simplificado | `zh-CN`|           |             | ✔ \*        |    |
| Dinamarquês      | `da`          | ✔ \*     | ✔           | ✔ \*            |     |
| Neerlandês       | `nl`          | ✔ \*     | ✔          |  ✔ \*           |     |
| Português     | `en`          | ✔        | ✔           |  ✔ \*\*     |      |
| Finlandês     | `fi`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Francês      | `fr`          | ✔        | ✔           |  ✔ \*           |     |
| Alemão      | `de`          | ✔ \*     | ✔           |  ✔ \*          |     |
| Grego       | `el`          | ✔ \*     |             |            |     |
| Húngaro   | `hu`          |           |             |  ✔ \*          |     | 
| Italiano     | `it`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Japonês    | `ja`          |          | ✔           |  ✔ \*          |     |
| Coreano      | `ko`          |          | ✔           |  ✔ \*          |     |
| Norueguês (Bokmål) | `no`  | ✔ \*     |  ✔          | ✔ \*            |     |
| Polaco      | `pl`          | ✔ \*     |  ✔          |  ✔ \*           |     |
| Português (Portugal) | `pt-PT`| ✔        |  ✔          | ✔ \*      |`pt` também aceites|
| Português (Brasil)   | `pt-BR`|          |  ✔   |  ✔ \*       |     |
| Russo     | `ru`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Espanhol     | `es`          | ✔        |            |   ✔ \*\*      |     | 
| Sueco     | `sv`          | ✔ \*     | ✔           |   ✔ \*          |     |
| Turco     | `tr`          | ✔ \*     |             |   ✔ \*          |  |

\* Suporte de idiomas está em pré-visualização

\*\* [Com o nome de entidades](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-ner) e [ligação de entidades](how-tos/text-analytics-how-to-entity-linking.md#entity-linking) estão disponíveis para este idioma.    

## <a name="see-also"></a>Consulte também

[Página de documentação dos serviços cognitiva](https://docs.microsoft.com/azure/cognitive-services/)   
[Página de Produto dos Serviços Cognitivos](https://azure.microsoft.com/services/cognitive-services/)

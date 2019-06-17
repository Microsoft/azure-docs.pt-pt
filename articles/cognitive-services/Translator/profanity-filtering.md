---
title: Filtragem de palavras ofensivas - API de texto do tradutor
titlesuffix: Azure Cognitive Services
description: Utilize linguagem inapropriada filtragem na API de texto do Translator.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: v-pawal
ms.openlocfilehash: 586fd93af127fbcf72caf64ad9523ff2c67c8d2d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66514204"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Adicionar palavras ofensivas filtragem com a API de texto do tradutor

Normalmente, o serviço Microsoft Translator retém linguagem inapropriada que está presente na origem na tradução. O grau de linguagem inapropriada e contexto que torna as palavras impróprias diferem entre culturas. Como resultado, o grau de linguagem inapropriada no idioma de destino pode ser Sim os amplificou ou reduzido.

Se quiser evitar ver linguagem inapropriada na tradução, mesmo que a linguagem inapropriada está presente no texto de origem, utilize a linguagem inapropriada opção disponível no método Translate() de filtragem. Esta opção permite-lhe escolher se pretende ver linguagem inapropriada eliminada, marcado com etiquetas adequadas, ou não tomar nenhuma ação executada.

O método de Translate() usa o parâmetro de "Opções", que contém o novo elemento "ProfanityAction". Os valores aceites do ProfanityAction são "NoAction", "Assinalado" e "Eliminado".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Valores aceites de ProfanityAction e exemplos
|Valor de ProfanityAction | Ação | Exemplo: Origem – japonês | Exemplo: Destino - inglês|
| :---|:---|:---|:---|
| NoAction | Predefinição. Mesmo que não definir a opção. Linguagem inapropriada passa de origem para destino. | 彼は変態です。 | Ele é idiota. |
| Marcado | Palavras profanas são cercadas por marcas XML \<linguagem inapropriada >... \</profanity >. | 彼は変態です。 | Ele é um \<linguagem inapropriada > puxão\</profanity >. |
| Eliminado | Palavras profanas são removidas da saída sem substituição. | 彼は。 | Ele é um. |

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Aplicam-se a linguagem inapropriada filtragem da chamada de API do Translator](reference/v3-0-translate.md)

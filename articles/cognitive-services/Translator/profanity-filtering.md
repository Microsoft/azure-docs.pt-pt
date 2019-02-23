---
title: Filtragem de palavras ofensivas - API de texto do tradutor
titlesuffix: Azure Cognitive Services
description: Utilize linguagem inapropriada filtragem na API de texto do Translator.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-jansko
ms.openlocfilehash: ab54f34dbdbda5af94c6ebc2bc8c1818ebf3d601
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729887"
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
| Eliminada | Palavras profanas são removidas da saída sem substituição. | 彼は。 | Ele é um. |

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Aplicam-se a linguagem inapropriada filtragem da chamada de API do Translator](reference/v3-0-translate.md)

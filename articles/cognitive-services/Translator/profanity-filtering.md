---
title: Filtragem de profanidade - Tradutor
titleSuffix: Azure Cognitive Services
description: Utilize a filtragem de palavrões para determinar o nível de profanação traduzido no seu texto no Tradutor de Serviços Cognitivos Azure.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 864f6a6d92306c40713f66b526c8a8df1683d3c4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586801"
---
# <a name="add-profanity-filtering-with-the-translator"></a>Adicione filtragem de profanação com o Tradutor

Normalmente, o serviço tradutor mantém a profanação que está presente na fonte na tradução. O grau de profanação e o contexto que faz com que as palavras profanas diferem entre culturas. Como resultado, o grau de profanação na linguagem-alvo pode ser amplificado ou reduzido.

Se pretender evitar ver palavrões na tradução, mesmo que a profanação esteja presente no texto de origem, utilize a opção de filtragem de palavrões disponível no método Traduzir() Esta opção permite-lhe escolher se pretende ver a profanação apagada, marcada com etiquetas apropriadas, ou se não tomar nenhuma medida tomada.

O método De Traduzir() toma o parâmetro "opções", que contém o novo elemento "ProfanityAction". Os valores aceites da ProfanityAction são "NoAction", "Marked" e "Apagado".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Valores aceites de ProfanityAction e exemplos
|Valor de Ação profana | Ação | Exemplo: Fonte - Japonês | Exemplo: Target - Inglês|
| :---|:---|:---|:---|
| NoAction | Predefinição. O mesmo que não definir a opção. Profanidade passa de origem para alvo. | 彼は変態です。 | É um idiota. |
| Marcado | Palavras profanas estão rodeadas por tags XML \< profanam>... \< /profanação>. | 彼は変態です。 | É um \<>idiota \< profanação>. |
| Eliminado | As palavras profanas são removidas da saída sem substituição. | 彼は。 | É um. |

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Aplique a filtragem de palavrões com a chamada do Tradutor](reference/v3-0-translate.md)

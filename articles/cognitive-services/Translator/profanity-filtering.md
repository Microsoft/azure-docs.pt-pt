---
title: Filtragem de profanidades - Tradutor
titleSuffix: Azure Cognitive Services
description: Utilize filtragem de profanidades para determinar o nível de profanação traduzido no seu texto no Tradutor de Serviços Cognitivos Azure.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 9f389d669e69dbfa6ec6d4d0b4716d2367443f17
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896804"
---
# <a name="add-profanity-filtering-with-the-translator"></a>Adicione filtragem de profanação com o Tradutor

Normalmente, o serviço Tradutor mantém profanação que está presente na fonte na tradução. O grau de profanação e o contexto que faz as palavras profanas diferem entre culturas. Como resultado, o grau de profanação na língua-alvo pode ser amplificado ou reduzido.

Se quiser evitar ver profanação na tradução, mesmo que a profanação esteja presente no texto-fonte, utilize a opção de filtragem de profanidades disponível no método Tradução.). Esta opção permite-lhe escolher se pretende ver profanação apagada, marcada com etiquetas apropriadas, ou se não tomar medidas tomadas.

O método Translate() toma o parâmetro "opções", que contém o novo elemento "ProfanityAction". Os valores aceites da ProfanityAction são "NoAction", "Marked" e "Deleted".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Valores aceites de ProfanityAction e exemplos
|Valor ProfanityAction | Ação | Exemplo: Fonte - Japonês | Exemplo: Target - Inglês|
| :---|:---|:---|:---|
| NoAction | Predefinição. O mesmo que não definir a opção. Profanidade passa de fonte para alvo. | 彼は変態です。 | É um idiota. |
| Marcado | Palavras profanas estão rodeadas por tags \<profanity> XML... \</profanity> . | 彼は変態です。 | É um \<profanity> \</profanity> idiota. |
| Eliminado | Palavras profanas são removidas da saída sem substituição. | 彼は。 | É um. |

## <a name="next-steps"></a>Próximos passos
> [!div class="nextstepaction"]
> [Aplicar filtragem de profanidade com a sua chamada de Tradutor](reference/v3-0-translate.md)

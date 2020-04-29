---
title: Filtragem de Profanidade - Tradutor Text API
titleSuffix: Azure Cognitive Services
description: Utilize a filtragem de palavrões para determinar o nível de profanação traduzido no seu texto na API do Tradutor de Serviços Cognitivos Azure.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: e7e2097e0d3daf360f1fa0f30bf3fd2c62c07163
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73836233"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Adicione filtragem de profanação com a API de Texto tradutor

Normalmente, o serviço tradutor mantém a profanação que está presente na fonte na tradução. O grau de profanação e o contexto que faz com que as palavras profanas diferem entre culturas. Como resultado, o grau de profanação na linguagem-alvo pode ser amplificado ou reduzido.

Se pretender evitar ver palavrões na tradução, mesmo que a profanação esteja presente no texto de origem, utilize a opção de filtragem de palavrões disponível no método Traduzir() Esta opção permite-lhe escolher se pretende ver a profanação apagada, marcada com etiquetas apropriadas, ou se não tomar nenhuma medida tomada.

O método De Traduzir() toma o parâmetro "opções", que contém o novo elemento "ProfanityAction". Os valores aceites da ProfanityAction são "NoAction", "Marked" e "Apagado".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Valores aceites de ProfanityAction e exemplos
|Valor de Ação profana | Ação | Exemplo: Fonte - Japonês | Exemplo: Target - Inglês|
| :---|:---|:---|:---|
| NoAction | Predefinição. O mesmo que não definir a opção. Profanidade passa de origem para alvo. | 彼は変態です。 | É um idiota. |
| Marcado | Palavras profanas estão rodeadas \<por tags XML profanando>... \</profanação>. | 彼は変態です。 | É um \<>palavrões\<>. |
| Eliminado | As palavras profanas são removidas da saída sem substituição. | 彼は。 | É um. |

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Aplique filtragem de profanação com a sua chamada DeAPi tradutora](reference/v3-0-translate.md)

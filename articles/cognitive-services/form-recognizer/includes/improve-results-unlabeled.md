---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: f0761847c3677b324ef16c5987eb9a1561dbcbe0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75379301"
---
Examine os valores de `"confidence"` para cada resultado de chave/valor no nó `"pageResults"`. Você também deve examinar as pontuações de confiança no nó `"readResults"`, que correspondem à operação de leitura de texto. A confiança dos resultados de leitura não afeta a confiança dos resultados de extração de chave/valor, portanto, você deve verificar ambos.
* Se as pontuações de confiança da operação de leitura estiverem baixas, tente melhorar a qualidade dos documentos de entrada (consulte [os requisitos de entrada](../overview.md#input-requirements)).
* Se as pontuações de confiança para a operação de extração de chave/valor estiverem baixas, verifique se os documentos que estão sendo analisados são do mesmo tipo que os documentos usados no conjunto de treinamento. Se os documentos no conjunto de treinamento tiverem variações de aparência, considere dividi-los em pastas diferentes e treinar um modelo para cada variação.

As pontuações de confiança que você visa dependerão do seu caso de uso, mas geralmente é uma boa prática visar uma pontuação de 80% ou acima. Para casos mais confidenciais, como ler registros médicos ou demonstrativos de cobrança, é recomendável uma pontuação de 100%.
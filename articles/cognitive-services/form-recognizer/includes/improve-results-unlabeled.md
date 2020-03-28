---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: f0761847c3677b324ef16c5987eb9a1561dbcbe0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75379301"
---
Examine `"confidence"` os valores de cada `"pageResults"` resultado chave/valor sob o nó. Deve também olhar para as `"readResults"` pontuações de confiança no nó, que correspondem à operação de leitura de texto. A confiança dos resultados da leitura não afeta a confiança dos resultados da extração chave/valor, pelo que deve verificar ambos.
* Se as pontuações de confiança para a operação de leitura forem baixas, tente melhorar a qualidade dos seus documentos de entrada (ver requisitos de [entrada).](../overview.md#input-requirements)
* Se as pontuações de confiança para a operação de extração chave/valor forem baixas, certifique-se de que os documentos analisados são do mesmo tipo que os documentos utilizados no conjunto de formação. Se os documentos do conjunto de formação tiverem variações de aparência, considere dividi-los em diferentes pastas e treinar um modelo para cada variação.

As pontuações de confiança que você alvo dependerá do seu caso de uso, mas geralmente é uma boa prática para atingir uma pontuação de 80% ou mais. Para casos mais sensíveis, como ler registos médicos ou declarações de faturação, recomenda-se uma pontuação de 100%.
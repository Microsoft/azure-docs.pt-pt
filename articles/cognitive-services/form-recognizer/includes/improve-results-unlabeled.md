---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: lajanuar
ms.openlocfilehash: 89b035397ea2050ae7e61f2a19310b6a7fb4192c
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467241"
---
Examine os `"confidence"` valores de cada resultado chave/valor sob o `"pageResults"` nó. Deve também olhar para as notas de confiança no `"readResults"` nó, que correspondem à operação de leitura de texto. A confiança dos resultados da leitura não afeta a confiança dos resultados da extração chave/valor, pelo que deve verificar ambos.
* Se as notas de confiança para a operação de leitura forem baixas, tente melhorar a qualidade dos seus documentos de entrada (ver [requisitos de entrada).](../overview.md#input-requirements)
* Se as notas de confiança para a operação de extração de chaves/valor forem baixas, certifique-se de que os documentos que estão a ser analisados são do mesmo tipo que os documentos utilizados no conjunto de formação. Se os documentos do conjunto de formação tiverem variações de aparência, considere dividi-los em diferentes pastas e treinar um modelo para cada variação.

As pontuações de confiança que você alvo dependerá do seu caso de uso, mas geralmente é uma boa prática para atingir uma pontuação de 80% ou mais. Para casos mais sensíveis, como ler registos médicos ou declarações de faturação, recomenda-se uma pontuação de 100%.
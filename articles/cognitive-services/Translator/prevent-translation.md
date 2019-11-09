---
title: Impedir a conversão de conteúdo-API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: Impedir a tradução de conteúdo com o API de Tradução de Texto. O API de Tradução de Texto permite que você marque o conteúdo para que ele não seja traduzido.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: f3bf784898f7f51beea890d8d2a8401af1403fbc
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888124"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Como impedir a tradução de conteúdo com o API de Tradução de Texto

O API de Tradução de Texto permite que você marque o conteúdo para que ele não seja traduzido. Por exemplo, você pode querer marcar o código, um nome da marca ou uma palavra/frase que não faça sentido quando localizada.

## <a name="methods-for-preventing-translation"></a>Métodos para impedir a tradução
1. Escape para uma marca do Twitter @somethingtopassthrough ou #somethingtopassthrough. Cancele o escape após a tradução.

2. Marque seu conteúdo com `notranslate`.

   Exemplo:

   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. Use o [dicionário dinâmico](dynamic-dictionary.md) para prescrever uma tradução específica.

4. Não transmita a cadeia de caracteres para a API de Tradução de Texto para tradução.

5. Tradutor personalizado: Use um [dicionário no Tradutor personalizado](custom-translator/what-is-dictionary.md) para prescrever a tradução de uma frase com uma probabilidade de 100%.


## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Evite a tradução em sua chamada à API do Tradutor](reference/v3-0-translate.md)

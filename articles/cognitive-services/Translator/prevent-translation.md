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
ms.date: 11/18/2019
ms.author: swmachan
ms.openlocfilehash: dd3684cbd7c03851bfcc75293a9690f77b4652b2
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184822"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Como impedir a tradução de conteúdo com o API de Tradução de Texto

O API de Tradução de Texto permite que você marque o conteúdo para que ele não seja traduzido. Por exemplo, você pode querer marcar o código, um nome da marca ou uma palavra/frase que não faça sentido quando localizada.

## <a name="methods-for-preventing-translation"></a>Métodos para impedir a tradução
1. Escape para uma marca do Twitter @somethingtopassthrough ou #somethingtopassthrough. Cancele o escape após a tradução. Esta é a expressão regular para as marcas válidas do Twitter: `\B@[A-Za-z]+[A-Za-z0-9_]+)`. Uma marca deve começar com um sinal de "@", seguido por um caractere e seguida por um ou vários caracteres, dígitos ou sublinhado. É recomendável manter as marcas curtas e a marca de abertura deve ser precedida por um espaço.

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

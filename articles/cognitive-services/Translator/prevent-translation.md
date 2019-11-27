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
ms.date: 11/21/2019
ms.author: swmachan
ms.openlocfilehash: 15a36451c18d65df6667f24284f3f69f3d1c06b8
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326765"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Como impedir a tradução de conteúdo com o API de Tradução de Texto

O API de Tradução de Texto permite que você marque o conteúdo para que ele não seja traduzido. Por exemplo, você pode querer marcar o código, um nome da marca ou uma palavra/frase que não faça sentido quando localizada.

## <a name="methods-for-preventing-translation"></a>Métodos para impedir a tradução
1. Escape para uma marca do Twitter @somethingtopassthrough ou #somethingtopassthrough. Cancele o escape após a tradução. Esta é a expressão regular para as marcas válidas do Twitter: `\B@[A-Za-z]+[A-Za-z0-9_]+)`. Uma marca deve começar com um sinal de "@", seguido por um caractere e seguida por um ou vários caracteres, dígitos ou sublinhado. É recomendável manter as marcas curtas e a marca de abertura deve ser precedida por um espaço.

2. Marque seu conteúdo com `notranslate`. É por design que isso funciona somente quando o TextType de entrada é definido como HTML

   Exemplo:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
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

---
title: Impedir a tradução de conteúdo - API de texto do tradutor
titlesuffix: Azure Cognitive Services
description: Impedir que a tradução de conteúdo com a API de texto do Translator.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-jansko
ms.openlocfilehash: cc06e020001e0e0696fe1e89863f7df705d7fe98
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56726981"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Como impedir a tradução de conteúdo com a API de texto do tradutor

A API de texto do Translator permite-lhe conteúdo de etiqueta, para que ele não está traduzido. Por exemplo, poderá marcar o código, um nome de marca ou uma palavra/expressão que não faz sentido quando localizado.

## <a name="methods-for-preventing-translation"></a>Métodos para impedir a tradução
1. Uma marca de Twitter de escape @somethingtopassthrough ou #somethingtopassthrough. Anular-escape após a tradução.

2. Etiquetar o conteúdo com `notranslate`.

   Exemplo:

   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. Utilize o [dicionário dinâmico](dynamic-dictionary.md) para prescrevem uma tradução específica.

4. Não passe a cadeia de caracteres para a API de texto do Translator para a tradução.

5. Tradutor personalizado: Utilize um [dicionário no personalizado Translator](custom-translator/what-is-dictionary.md) recomende uma tradução de uma frase com a probabilidade de 100%.


## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Evitar a tradução em sua chamada de API do Translator](reference/v3-0-translate.md)

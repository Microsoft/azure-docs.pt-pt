---
title: Impedir a tradução de conteúdos - API de Texto tradutor
titleSuffix: Azure Cognitive Services
description: Impedir a tradução de conteúdos com a API de Texto tradutor. A API de Texto tradutor permite-lhe marcar conteúdo para que não seja traduzido.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: swmachan
ms.openlocfilehash: c7be4a0ea1a9d24a8b262132632a0bbb63ae1b96
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80052476"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Como impedir a tradução de conteúdos com a API de Texto tradutor

A API de Texto tradutor permite-lhe marcar conteúdo para que não seja traduzido. Por exemplo, pode querer marcar código, uma marca ou uma palavra/frase que não faça sentido quando localizada.

## <a name="methods-for-preventing-translation"></a>Métodos de prevenção da tradução

1. Marque o `notranslate`seu conteúdo com . É por design que isto funciona apenas quando o texto de entradaType é definido como HTML

   Exemplo:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. Utilize o [dicionário dinâmico](dynamic-dictionary.md) para prescrever uma tradução específica.

3. Não passe a corda para a API de Texto tradutor para tradução.

4. Tradutor personalizado: Utilize um [dicionário em Tradutor Personalizado](custom-translator/what-is-dictionary.md) para prescrever a tradução de uma frase com 100% de probabilidade.


## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Evite a tradução na chamada da API tradutora](reference/v3-0-translate.md)

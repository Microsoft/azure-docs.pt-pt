---
title: Impedir a tradução de conteúdos - Tradutor
titleSuffix: Azure Cognitive Services
description: Impedir a tradução de conteúdos com o Tradutor. O Tradutor permite-lhe marcar conteúdo para que não seja traduzido.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 37d2e4c0131569ab50ebf49ff73b6adf7a420713
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996181"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>Como impedir a tradução de conteúdos com o Tradutor

O Tradutor permite-lhe marcar conteúdo para que não seja traduzido. Por exemplo, pode querer marcar código, uma marca ou uma palavra/frase que não faça sentido quando localizada.

## <a name="methods-for-preventing-translation"></a>Métodos de prevenção da tradução

1. Marque o seu conteúdo com `notranslate` . É por design que isto funciona apenas quando o texto de entradaType é definido como HTML

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

3. Não passe a corda ao Tradutor para tradução.

4. Tradutor personalizado: Utilize um [dicionário em Tradutor Personalizado](custom-translator/what-is-dictionary.md) para prescrever a tradução de uma frase com 100% de probabilidade.


## <a name="next-steps"></a>Próximos passos
> [!div class="nextstepaction"]
> [Evite a tradução na chamada do tradutor](reference/v3-0-translate.md)

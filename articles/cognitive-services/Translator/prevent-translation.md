---
title: Impedir a tradução de conteúdos - Tradutor
titleSuffix: Azure Cognitive Services
description: Impedir a tradução de conteúdos com o Tradutor. O Tradutor permite-lhe marcar conteúdo para que não seja traduzido.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: e89ff3f4c1f54d9e137d3dd51e325b908c826b03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "98897481"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>Como impedir a tradução de conteúdos com o Tradutor

O Tradutor permite-lhe marcar conteúdo para que não seja traduzido. Por exemplo, poderá querer etiquetar código, o nome de uma marca ou uma palavra/expressão que não faz sentido quando localizada.

## <a name="methods-for-preventing-translation"></a>Métodos para impedir a tradução

1. Marque o seu conteúdo com `notranslate` . É por design que isto só funciona quando o texto de entradaType é definido como HTML

   Exemplo:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. Marque o seu conteúdo com `translate="no"` . Isto só funciona quando o texto de entradaType é definido como HTML

   Exemplo:

   ```html
   <span translate="no">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div translate="no">This will not be translated.</div>
   <div>This will be translated. </div>
   ```
   
3. Utilize o [dicionário dinâmico](dynamic-dictionary.md) para prescrever uma tradução específica.

4. Não passe a corda ao Tradutor para tradução.

5. Tradutor personalizado: Utilize um [dicionário em Tradutor Personalizado](custom-translator/what-is-dictionary.md) para prescrever a tradução de uma frase com 100% de probabilidade.


## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Utilize a operação 'Traduzir' para traduzir texto](reference/v3-0-translate.md)

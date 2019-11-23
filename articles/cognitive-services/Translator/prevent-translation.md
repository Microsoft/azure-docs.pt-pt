---
title: Prevent content translation - Translator Text API
titleSuffix: Azure Cognitive Services
description: Prevent translation of content with the Translator Text API. The Translator Text API allows you to tag content so that it isn't translated.
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
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>How to prevent translation of content with the Translator Text API

The Translator Text API allows you to tag content so that it isn't translated. For example, you may want to tag code, a brand name, or a word/phrase that doesn't make sense when localized.

## <a name="methods-for-preventing-translation"></a>Methods for preventing translation
1. Escape to a Twitter tag @somethingtopassthrough or #somethingtopassthrough. Un-escape after translation. This is the regular expression for valid twitter tags: `\B@[A-Za-z]+[A-Za-z0-9_]+)`. A tag should start with a "@" sign, followed by a character and then followed by one or many characters, digits or underscore. It is recommended to keep tags short and the opening tag must be preceded by a space.

2. Tag your content with `notranslate`. It's by design that this works only when the input textType is set as HTML

   Exemplo:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. Use the [dynamic dictionary](dynamic-dictionary.md) to prescribe a specific translation.

4. Don't pass the string to the Translator Text API for translation.

5. Custom Translator: Use a [dictionary in Custom Translator](custom-translator/what-is-dictionary.md) to prescribe the translation of a phrase with 100% probability.


## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Avoid translation in your Translator API call](reference/v3-0-translate.md)

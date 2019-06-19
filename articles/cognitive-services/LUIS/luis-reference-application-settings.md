---
title: Definições da aplicação
titleSuffix: Azure Cognitive Services
description: Compreenda as definições de aplicações para aplicações de compreensão de idiomas.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/29/2019
ms.author: diberry
ms.openlocfilehash: a8a1f6fe9372d013d310c557161ceb813b8a478b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67163644"
---
# <a name="application-settings"></a>Definições da aplicação

Estas definições de aplicação são armazenadas no [exportados](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) aplicação e [atualizado](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) com as APIs REST. Alterar as definições de versão da aplicação repõe o seu estado de treinamento de aplicação para inoportuno.

|Definição|Valor predefinido|Notas|
|--|--|--|
|NormalizePunctuation|Verdadeiro|Remove a pontuação.|
|NormalizeDiacritics|Verdadeiro|Remove diacríticos.|

## <a name="diacritics-normalization"></a>Normalização de diacríticos 

Ativar o normalização de expressão para diacríticos para o ficheiro de aplicação LUIS JSON a `settings` parâmetro.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

As expressões seguintes mostram como a normalização de diacríticos afeta expressões com:

|Com diacríticos definidos como falso|Com diacríticos definido como true|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Suporte de idiomas para diacríticos

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Português (Brasil) `pt-br` diacríticos

|Diacríticos definidos como falso|Diacríticos definidos como falso|
|-|-|
|`á`|`a`|
|`â`|`a`|
|`ã`|`a`|
|`à`|`a`|
|`ç`|`c`|
|`é`|`e`|
|`ê`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ô`|`o`|
|`õ`|`o`|
|`ú`|`u`| 
|||

#### <a name="dutch-nl-nl-diacritics"></a>Holandês `nl-nl` diacríticos

|Diacríticos definidos como falso|Diacríticos definidos como falso|
|-|-|
|`á`|`a`|
|`à`|`a`|
|`é`|`e`|
|`ë`|`e`|
|`è`|`e`|
|`ï`|`i`|
|`í`|`i`|
|`ó`|`o`|
|`ö`|`o`|
|`ú`|`u`| 
|`ü`|`u`|
|||

#### <a name="french-fr--diacritics"></a>Francês `fr-` diacríticos

Isto inclui subcultures de francês e canadenses.

|Diacríticos definidos como falso|Diacríticos definidos como falso|
|--|--|
|`é`|`e`|
|`à`|`a`|
|`è`|`e`|
|`ù`|`u`|
|`â`|`a`| 
|`ê`|`e`| 
|`î`|`i`| 
|`ô`|`o`| 
|`û`|`u`| 
|`ç`|`c`| 
|`ë`|`e`| 
|`ï`|`i`| 
|`ü`|`u`| 
|`ÿ`|`y`| 

#### <a name="german-de-de-diacritics"></a>Alemão `de-de` diacríticos

|Diacríticos definidos como falso|Diacríticos definidos como falso|
|--|--|
|`ä`|`a`|
|`ö`|`o`| 
|`ü`|`u`| 

#### <a name="italian-it-it-diacritics"></a>Italiano `it-it` diacríticos

|Diacríticos definidos como falso|Diacríticos definidos como falso|
|--|--|
|`à`|`a`|
|`è`|`e`|
|`é`|`e`|
|`ì`|`i`| 
|`í`|`i`| 
|`î`|`i`| 
|`ò`|`o`| 
|`ó`|`o`| 
|`ù`|`u`|
|`ú`|`u`|

#### <a name="spanish-es--diacritics"></a>Espanhol `es-` diacríticos

Isto inclui o México espanhol e canadenses.

|Diacríticos definidos como falso|Diacríticos definidos como falso|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`| 
|`ó`|`o`| 
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>Normalização de pontuação

Ativar o normalização de expressão para pontuação para o ficheiro de aplicação LUIS JSON a `settings` parâmetro.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
] 
```

As expressões seguintes mostram como diacríticos afeta expressões com:

|Com diacríticos definidos como falso|Com diacríticos definidos como verdadeiro|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Pontuação

A pontuação seguinte é removida com `NormalizePunctuation` está definido como true.

|Pontuação|
|--|
|`-`| 
|`.`| 
|`'`|
|`"`|
|`\`|
|`/`|
|`?`|
|`!`|
|`_`|
|`,`|
|`;`|
|`:`|
|`(`|
|`)`|
|`[`|
|`]`|
|`{`|
|`}`|
|`+`|
|`¡`|

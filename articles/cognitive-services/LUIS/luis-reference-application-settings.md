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
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 3682b9e0c38344be1522440290b46f8c10bd5607
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275913"
---
# <a name="application-settings"></a>Definições da aplicação

Estas definições de aplicação são armazenadas no [exportados](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) aplicação e [atualizado](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) com as APIs REST. Alterar as definições de versão da aplicação repõe o seu estado de treinamento de aplicação para inoportuno.

|Definição|Valor predefinido|Notas|
|--|--|--|
|NormalizePunctuation|True|Remove a pontuação.|
|NormalizeDiacritics|True|Remove diacríticos.|

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

|Diacríticos definidos como falso|Diacríticos definido como verdadeiro|
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

|Diacríticos definidos como falso|Diacríticos definido como verdadeiro|
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

|Diacríticos definidos como falso|Diacríticos definido como verdadeiro|
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

|Diacríticos definidos como falso|Diacríticos definido como verdadeiro|
|--|--|
|`ä`|`a`|
|`ö`|`o`| 
|`ü`|`u`| 

#### <a name="italian-it-it-diacritics"></a>Italiano `it-it` diacríticos

|Diacríticos definidos como falso|Diacríticos definido como verdadeiro|
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

|Diacríticos definidos como falso|Diacríticos definido como verdadeiro|
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

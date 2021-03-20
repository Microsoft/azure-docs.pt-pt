---
title: Definições de aplicação - LUIS
description: As definições de aplicações para aplicações de compreensão de idiomas Azure Cognitive Services são armazenadas na aplicação e portal.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/04/2020
ms.openlocfilehash: 0578e3c3c952a475c6beb01ffcf354e19eda6e26
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91319168"
---
# <a name="app-and-version-settings"></a>Definições de aplicações e versão

Estas configurações são armazenadas na aplicação [exportada](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) e atualizadas com o portal REST APIs ou LUIS.

Alterar as definições da versão da aplicação reinicia o estado de formação da aplicação para não treinar.

[!INCLUDE [App and version settings](includes/app-version-settings.md)]


Referência de texto e exemplos incluem:

* [Pontuação](#punctuation-normalization)
* [Diacríticos](#diacritics-normalization)

## <a name="diacritics-normalization"></a>Normalização diacrítica

As seguintes declarações mostram como a normalização dos diacríticos impacta as expressões:

|Com diacríticos definidos para falso|Com diacríticos definidos para verdade|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Suporte linguístico para diacríticos

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>`pt-br`Diacríticos portugueses brasileiros

|Diacríticos definidos para falso|Diacrítico definido para verdadeiro|
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

#### <a name="dutch-nl-nl-diacritics"></a>`nl-nl`Diacríticos holandeses

|Diacríticos definidos para falso|Diacrítico definido para verdadeiro|
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

#### <a name="french-fr--diacritics"></a>`fr-`Diacríticos franceses

Isto inclui as subculturas francesas e canadianas.

|Diacríticos definidos para falso|Diacrítico definido para verdadeiro|
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

#### <a name="german-de-de-diacritics"></a>`de-de`Diacríticos alemães

|Diacríticos definidos para falso|Diacrítico definido para verdadeiro|
|--|--|
|`ä`|`a`|
|`ö`|`o`|
|`ü`|`u`|

#### <a name="italian-it-it-diacritics"></a>`it-it`Diacríticos italianos

|Diacríticos definidos para falso|Diacrítico definido para verdadeiro|
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

#### <a name="spanish-es--diacritics"></a>`es-`Diacríticos espanhóis

Isto inclui tanto o espanhol como o canadiano mexicano.

|Diacríticos definidos para falso|Diacrítico definido para verdadeiro|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|

## <a name="punctuation-normalization"></a>Normalização da pontuação

As seguintes declarações mostram como a pontuação impacta as expressões:

|Com pontuação definida para Falso|Com pontuação definida para Verdadeiro|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Pontuação removida

A seguinte pontuação é removida com `NormalizePunctuation` é definida como verdadeira.

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

## <a name="next-steps"></a>Passos seguintes

* Aprenda [conceitos](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) de diacríticos e pontuação.

---
title: Definições de candidatura - LUIS
description: As definições de aplicações para aplicações de compreensão linguística dos Serviços Cognitivos Azure estão armazenadas na aplicação e portal.
ms.topic: reference
ms.date: 05/04/2020
ms.openlocfilehash: 7b545e0959a43520b7d643ef8c0658a1e1a3b295
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590995"
---
# <a name="app-and-version-settings"></a>Definições de aplicativos e versão

Estas configurações são armazenadas na app [exportada](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) e atualizadas com as APIs rest ou portal LUIS.

Alterar as definições da versão da aplicação repõe o estado de treino da aplicação para destreinado.

[!INCLUDE [App and version settings](includes/app-version-settings.md)]


A referência de texto e os exemplos incluem:

* [Pontuação](#punctuation-normalization)
* [Diacritics](#diacritics-normalization)

## <a name="diacritics-normalization"></a>Normalização dos diacríticos

As seguintes declarações mostram como a normalização dos diacritics tem impacto nas expressões:

|Com diacritics definido para falso|Com diacritics definidos para verdade|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Suporte linguístico para diacríticos

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>`pt-br`Diacríticos portugueses brasileiros

|Diacritics definido para falso|Diacritics definido para verdade|
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

|Diacritics definido para falso|Diacritics definido para verdade|
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

Isto inclui subculturas francesas e canadianas.

|Diacritics definido para falso|Diacritics definido para verdade|
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

|Diacritics definido para falso|Diacritics definido para verdade|
|--|--|
|`ä`|`a`|
|`ö`|`o`|
|`ü`|`u`|

#### <a name="italian-it-it-diacritics"></a>`it-it`Diacríticos italianos

|Diacritics definido para falso|Diacritics definido para verdade|
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

Isto inclui tanto o espanhol como o mexicano canadiano.

|Diacritics definido para falso|Diacritics definido para verdade|
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

|Com pontuação definida para Falso|Com pontuação definida para True|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Pontuação removida

A pontuação a seguir é removida com `NormalizePunctuation` a verdadeira.

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

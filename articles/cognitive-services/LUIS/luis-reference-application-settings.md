---
title: Configurações do aplicativo-LUIS
titleSuffix: Azure Cognitive Services
description: As configurações de aplicativos para aplicativos de reconhecimento de linguagem de serviços cognitivas do Azure são armazenadas no aplicativo e no Portal.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: bae4f09b539e26ca8c0d4ce97999776dc0911601
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961779"
---
# <a name="application-settings"></a>Definições da aplicação

Essas configurações de aplicativo são armazenadas no aplicativo [exportado](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) e [atualizadas](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) com as APIs REST. Alterar as configurações de versão do aplicativo redefine o status de treinamento do aplicativo como não treinado.

|Definição|Valor predefinido|Notas|
|--|--|--|
|NormalizePunctuation|Verdadeiro|Remove a pontuação.|
|NormalizeDiacritics|Verdadeiro|Remove diacríticos.|

## <a name="diacritics-normalization"></a>Normalização de diacríticos 

Ative a normalização de expressão para diacríticos para o arquivo de aplicativo JSON LUIS no parâmetro `settings`.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

O declarações a seguir mostra como a normalização de diacríticos afeta o declarações:

|Com sinais diacríticos definidos como false|Com sinais diacríticos definidos como true|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Suporte a idiomas para sinais diacríticos

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Diacríticos do `pt-br` português (Brasil)

|Sinais diacríticos definidos como false|Sinais diacríticos definidos como true|
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

#### <a name="dutch-nl-nl-diacritics"></a>`nl-nl` diacríticos em Holandês

|Sinais diacríticos definidos como false|Sinais diacríticos definidos como true|
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

#### <a name="french-fr--diacritics"></a>`fr-` diacríticos em francês

Isso inclui as subculturas francesa e canadense.

|Sinais diacríticos definidos como false|Sinais diacríticos definidos como true|
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

#### <a name="german-de-de-diacritics"></a>`de-de` diacríticos em alemão

|Sinais diacríticos definidos como false|Sinais diacríticos definidos como true|
|--|--|
|`ä`|`a`|
|`ö`|`o`| 
|`ü`|`u`| 

#### <a name="italian-it-it-diacritics"></a>`it-it` diacríticos em Italiano

|Sinais diacríticos definidos como false|Sinais diacríticos definidos como true|
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

#### <a name="spanish-es--diacritics"></a>Sinais diacríticos de `es-` espanhol

Isso inclui espanhol e Canadá mexicano.

|Sinais diacríticos definidos como false|Sinais diacríticos definidos como true|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`| 
|`ó`|`o`| 
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>Normalização de Pontuação

Ative a normalização de expressão para pontuação em seu arquivo de aplicativo JSON LUIS no parâmetro `settings`.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
] 
```

O declarações a seguir mostra como a pontuação afeta declarações:

|Com pontuação definida como false|Com pontuação definida como true|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Pontuação removida

A pontuação a seguir é removida com `NormalizePunctuation` está definida como true.

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

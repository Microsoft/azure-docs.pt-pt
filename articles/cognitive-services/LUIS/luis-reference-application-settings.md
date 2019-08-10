---
title: Configurações do aplicativo-LUIS
titleSuffix: Azure Cognitive Services
description: Entenda as configurações de aplicativos para aplicativos de reconhecimento de linguagem.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 6d516590b90efd937789fa0847b707d2521f6459
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932584"
---
# <a name="application-settings"></a>Definições da aplicação

Essas configurações de aplicativo são armazenadas no [](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) aplicativo exportado e [atualizadas](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) com as APIs REST. Alterar as configurações de versão do aplicativo redefine o status de treinamento do aplicativo como não treinado.

|Definição|Valor predefinido|Notas|
|--|--|--|
|NormalizePunctuation|True|Remove a pontuação.|
|NormalizeDiacritics|True|Remove diacríticos.|

## <a name="diacritics-normalization"></a>Normalização de diacríticos 

Ative a `settings` normalização de expressão para diacríticos para o arquivo de aplicativo JSON Luis no parâmetro.

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

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Diacríticos do `pt-br` Português do Brasil

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

#### <a name="dutch-nl-nl-diacritics"></a>Diacríticos `nl-nl` holandeses

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

#### <a name="french-fr--diacritics"></a>Diacríticos `fr-` franceses

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

#### <a name="german-de-de-diacritics"></a>Diacríticos `de-de` em alemão

|Sinais diacríticos definidos como false|Sinais diacríticos definidos como true|
|--|--|
|`ä`|`a`|
|`ö`|`o`| 
|`ü`|`u`| 

#### <a name="italian-it-it-diacritics"></a>Diacríticos `it-it` em Italiano

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

#### <a name="spanish-es--diacritics"></a>Diacríticos `es-` em espanhol

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

Ative a `settings` normalização de expressão para pontuação em seu arquivo de aplicativo JSON Luis no parâmetro.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
] 
```

O declarações a seguir mostra como os diacríticos afetam o declarações:

|Com sinais diacríticos definidos como false|Com sinais diacríticos definidos como true|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Pontuação removida

A pontuação a seguir é removida com `NormalizePunctuation` é definido como true.

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

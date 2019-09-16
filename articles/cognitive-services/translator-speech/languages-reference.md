---
title: Método de idiomas API de Tradução de Fala
titleSuffix: Azure Cognitive Services
description: Use o método de idiomas API de Tradução de Fala.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 46ac3928238382f56db5a799226bd3d9243b7ca2
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966402"
---
# <a name="translator-speech-api-languages"></a>API de Tradução de Fala: Languages

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Tradução de Fala estende continuamente a lista de idiomas com suporte em seus serviços. Use essa API para descobrir o conjunto de idiomas disponíveis no momento para uso com o serviço de Tradução de Fala.

Exemplos de código que demonstram o uso da API para obter os idiomas disponíveis estão disponíveis no [site GitHub do Microsoft Translator](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Notas de implementação

### <a name="get-languages"></a>OBTER/Languages

Um amplo conjunto de idiomas está disponível para transcrever a fala, para traduzir o texto transcrita e para produzir fala sintetizada da tradução.

Um cliente usa o `scope` parâmetro de consulta para definir em quais conjuntos de idiomas ele está interessado.

* **Conversão de fala em texto:** Use o parâmetro `scope=speech` de consulta para recuperar o conjunto de idiomas disponíveis para transcrever a fala em texto.
* **Tradução de texto:** Use o parâmetro `scope=text` de consulta para recuperar o conjunto de idiomas disponíveis para traduzir o texto transcrevedo.
* **Conversão de texto em fala:**  Use o parâmetro `scope=tts` de consulta para recuperar o conjunto de idiomas e vozes disponíveis para sintetizar o texto traduzido de volta à fala.

Um cliente pode recuperar vários conjuntos simultaneamente, especificando uma lista separada por vírgulas de opções. Por exemplo, `scope=speech,text,tts`.

Uma resposta bem-sucedida é um objeto JSON com uma propriedade para cada conjunto solicitado.

```
{
    "speech": {
        //... Set of languages supported for speech-to-text
    },
    "text": {
        //... Set of languages supported for text translation
    },
    "tts": {
        //... Set of languages supported for text-to-speech
    }
}
```

Como um cliente pode usar o `scope` parâmetro de consulta para selecionar quais conjuntos de idiomas com suporte devem ser retornados, uma resposta real pode incluir apenas um subconjunto de todas as propriedades mostradas acima.

O valor fornecido com cada propriedade é o seguinte.

### <a name="speech-to-text-speech"></a>Conversão de fala em texto (fala)

O valor associado à propriedade de fala-para-texto, `speech`, é um dicionário de pares (chave, valor). Cada chave identifica um idioma com suporte para conversão de fala em texto. A chave é o identificador que o cliente passa para a API. O valor associado à chave é um objeto com as seguintes propriedades:

* `name`: Nome de exibição do idioma.
* `language`: Marca de idioma do idioma escrito associado. Consulte "transação de texto" abaixo.
Um exemplo é:

```
{
    "speech": {
        "en-US": { name: "English", language: "en" }
    }
}
```

### <a name="text-translation-text"></a>Conversão de texto (texto)

O valor associado `text` à propriedade também é um dicionário em que cada chave identifica um idioma com suporte para tradução de texto. O valor associado à chave descreve o idioma:

* `name`: Nome de exibição do idioma.
* `dir`: Direcionalidade, que `rtl` é para idiomas da direita para a esquerda `ltr` ou para linguagens da esquerda para a direita.

Um exemplo é:

```
{
    "text": {
        "en": { name: "English", dir: "ltr" }
    }
}
```

### <a name="text-to-speech-tts"></a>Conversão de texto em fala (TTS)

O valor associado à propriedade de conversão de texto em fala, TTS, também é um dicionário em que cada chave identifica uma voz com suporte. Os atributos de um objeto de voz são:

* `displayName`: Nome de exibição da voz.
* `gender`: Sexo da voz (masculino ou feminino).
* `locale`: Marca de idioma da voz com o idioma principal Subtag e a região Subtag.
* `language`: Marca de idioma do idioma escrito associado.
* `languageName`: Nome de exibição do idioma.
* `regionName`: Nome de exibição da região para este idioma.

Um exemplo é:

```
{
    "tts": {
        "en-US-Zira": {
            "displayName": "Zira",
            "gender": "female",
            "locale": "en-US",
            "languageName": "English",
            "regionName": "United States",
            "language": "en"
        }
}
```

### <a name="localization"></a>Localização
O serviço retorna todos os nomes no idioma do cabeçalho ' Accept-Language ' para todos os idiomas com suporte na tradução de texto.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
Objeto que descreve o conjunto de idiomas com suporte.

Valor de ModelExample:

Langagues {fala (objeto, opcional), texto (objeto, opcional), TTS (objeto, opcional)}

### <a name="headers"></a>Cabeçalhos

|Cabeçalho|Descrição|Type|
:--|:--|:--|
X-RequestId|Valor gerado pelo servidor para identificar a solicitação e usado para fins de solução de problemas.|Cadeia de caracteres|

### <a name="parameters"></a>Parâmetros

|Parâmetro|Descrição|Tipo de parâmetro|Tipo de Dados|
|:--|:--|:--|:--|
|versão de API    |Versão da API solicitada pelo cliente. Os valores permitidos são `1.0`:.|query|Cadeia de caracteres|
|scope  |Conjuntos de idiomas com suporte ou vozes para retornar ao cliente. Esse parâmetro é especificado como uma lista separada por vírgulas de palavras-chave. As seguintes palavras-chave estão disponíveis:<ul><li>`speech`: Fornece o conjunto de idiomas com suporte para transcrever a fala.</li><li>`tts`: Fornece o conjunto de vozes com suporte para conversão de texto-fala.</li><li>`text`: Fornece o conjunto de idiomas com suporte para traduzir texto.</li></ul>Se um valor não for especificado, o valor de `scope` padrão `text`será.|query|Cadeia de caracteres|
|X-ClientTraceId    |Um GUID gerado pelo cliente usado para rastrear uma solicitação. Para facilitar a solução de problemas, os clientes devem fornecer um novo valor com cada solicitação e registrar em log.|cabeçalho|Cadeia de caracteres|
|Aceitar idioma    |Alguns dos campos na resposta são nomes de idiomas ou regiões. Use esse parâmetro para definir o idioma no qual os nomes são retornados. O idioma é especificado fornecendo uma marca de linguagem BCP 47 bem formada. Selecione uma marca na lista de identificadores de idioma retornados com o `text` escopo. Para idiomas sem suporte, os nomes são fornecidos no idioma inglês.<br/>Por exemplo, use o valor `fr` para solicitar nomes em francês ou use o valor `zh-Hant` para solicitar nomes em Chinês tradicional.|cabeçalho|Cadeia de caracteres|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Reason|
|:--|:--|
|400|Solicitação inadequada. Verifique os parâmetros de entrada para garantir que eles sejam válidos. O objeto de resposta inclui uma descrição mais detalhada do erro.|
|429|Número excessivo de solicitações.|
|500|Ocorreu um erro. Se o erro persistir, informe-o com o identificador de rastreamento do cliente (X-ClientTraceId) ou o identificador de solicitação (X-RequestId).|
|503|Servidor temporariamente indisponível. Repita a solicitação. Se o erro persistir, informe-o com o identificador de rastreamento do cliente (X-ClientTraceId) ou o identificador de solicitação (X-RequestId).|

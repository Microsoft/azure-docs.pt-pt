---
title: Método de idiomas de API de voz do Translator
titleSuffix: Azure Cognitive Services
description: Use o método de idiomas de API de voz do Translator.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: v-jansko
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 111adad627ffd830d69efc61bc7a06a99fee30f3
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673329"
---
# <a name="translator-speech-api-languages"></a>API de voz de tradutor: Languages

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Voz do Translator continuamente expande a lista de idiomas suportados pelo seus serviços. Utilize esta API para descobrir o conjunto de idiomas atualmente disponíveis para utilização com o serviço de voz do Translator.

Exemplos de código a fim de demonstrar o uso da API para obter os idiomas disponíveis estão disponíveis a partir da [site do Microsoft Translator GitHub](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Notas de implementação

### <a name="get-languages"></a>OBTER /languages

Uma grande variedade de idiomas está disponível para transcrição de voz, Traduza texto transcrito e produzir fala sintetizada da tradução.

Um cliente utiliza o `scope` parâmetro para definir quais conjuntos de idiomas está interessada na consulta.

* **Conversão de voz para texto:** Utilize o parâmetro de consulta `scope=speech` para obter o conjunto de idiomas disponíveis para a transcrição de voz em texto.
* **Tradução de texto:** Utilize o parâmetro de consulta `scope=text` para obter o conjunto de idiomas disponíveis para traduzir texto transcrito.
* **Voz:**  Utilize o parâmetro de consulta `scope=tts` para recuperar o conjunto de idiomas e vozes disponíveis para sintetizar o texto traduzido novamente em voz.

Um cliente pode obter vários conjuntos em simultâneo ao especificar uma lista separada por vírgulas de opções. Por exemplo, `scope=speech,text,tts`.

Uma resposta com êxito é um objeto JSON com uma propriedade para cada pedido de conjunto.

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

Uma vez que um cliente pode utilizar o `scope` deve ser devolvido o parâmetro de consulta para selecionar quais conjuntos de idiomas suportados, uma resposta real só pode incluir um subconjunto de todas as propriedades mostrado acima.

Segue-se o valor fornecido com cada propriedade.

### <a name="speech-to-text-speech"></a>Conversão de voz em texto (conversão de voz)

O valor associado a propriedade de voz em texto, `speech`, é um dicionário de valor (chave) pares. Cada chave identifica um idioma suportado para conversão de voz em texto. A chave é o identificador de que o cliente passa para a API. O valor associado à chave é um objeto com as seguintes propriedades:

* `name`: Nome a apresentar da linguagem.
* `language`: Etiqueta de idioma do idioma escrito associada. Veja "Texto transation" abaixo.
Um exemplo é:

```
{
    "speech": {
        "en-US": { name: "English", language: "en" }
    }
}
```

### <a name="text-translation-text"></a>Tradução de texto (texto)

O valor associado a `text` propriedade também é um dicionário em que cada chave identifica um idioma suportado para a tradução de texto. O valor associado à chave descreve a linguagem:

* `name`: Nome a apresentar da linguagem.
* `dir`: Direcionalidade que é `rtl` para idiomas da direita para esquerda ou `ltr` para idiomas do esquerda para a direita.

Um exemplo é:

```
{
    "text": {
        "en": { name: "English", dir: "ltr" }
    }
}
```

### <a name="text-to-speech-tts"></a>Voz (tts)

O valor associado a propriedade de texto para voz, tts, também é um dicionário em que cada chave identifica uma voz suportada. Atributos de um objeto de voz são:

* `displayName`: Nome a apresentar para a voz.
* `gender`: Sexo de voz (male ou female).
* `locale`: Etiqueta de idioma de voz com subtag do idioma primário e subtag de região.
* `language`: Etiqueta de idioma do idioma escrito associada.
* `languageName`: Nome a apresentar da linguagem.
* `regionName`: Nome a apresentar da região para este idioma.

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
O serviço devolve todos os nomes no idioma do cabeçalho Accept-Language, para todos os idiomas suportados na tradução de texto.

### <a name="response-class-status-200"></a>Classe de resposta (estado 200)
Objeto que descreve o conjunto de idiomas suportados.

Valor de ModelExample:

Langagues {voz (object, opcional), texto (object, opcional), tts (object, opcional)}

### <a name="headers"></a>Cabeçalhos

|Cabeçalho|Descrição|Type|
:--|:--|:--|
X-RequestId|Valor gerado pelo servidor para identificar o pedido e utilizada para fins de resolução de problemas.|cadeia|

### <a name="parameters"></a>Parâmetros

|Parâmetro|Descrição|Tipo de parâmetro|Tipo de Dados|
|:--|:--|:--|:--|
|versão de API    |Versão da API do pedido pelo cliente. Valores permitidos são: `1.0`.|consulta|cadeia|
|scope  |Conjuntos de idiomas suportados ou vozes para devolver ao cliente. Este parâmetro for especificado como uma lista separada por vírgulas de palavras-chave. As palavras-chave seguintes estão disponíveis:<ul><li>`speech`: Fornece o conjunto de idiomas suportados para transcrição de voz.</li><li>`tts`: Fornece o conjunto de vozes suportado para conversão de voz de texto.</li><li>`text`: Fornece o conjunto de idiomas suportados para a tradução de texto.</li></ul>Se não for especificado um valor, o valor de `scope` assume a predefinição `text`.|consulta|cadeia|
|X-ClientTraceId    |Um GUID gerado pelo cliente utilizado para rastrear um pedido. Para facilitar a resolução de problemas relacionados, os clientes devem fornecer um novo valor com cada solicitação e registrá-lo.|cabeçalho|cadeia|
|Accept-Language    |Alguns dos campos na resposta são nomes de idiomas ou regiões. Utilize este parâmetro para definir o idioma em que os nomes são devolvidos. O idioma é especificado ao fornecer uma etiqueta de idioma do BCP 47 bem formada. Selecione uma etiqueta na lista de identificadores de idioma devolvida com o `text` âmbito. Para os idiomas sem suporte, os nomes são fornecidos em inglês.<br/>Por exemplo, utilize o valor `fr` para pedir nomes em francês ou utilizar o valor `zh-Hant` para nomes do pedido em chinês tradicional.|cabeçalho|cadeia|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400|Pedido incorreto. Verifique os parâmetros de entrada para garantir que são válidos. O objeto de resposta inclui uma descrição mais detalhada do erro.|
|429|Demasiados pedidos.|
|500|Ocorreu um erro. Se o erro persistir, relatá-lo com o identificador de rastreio de cliente (X-ClientTraceId) ou o identificador (X-RequestId) do pedido.|
|503|Servidor temporariamente indisponível. Tente novamente o pedido. Se o erro persistir, relatá-lo com o identificador de rastreio de cliente (X-ClientTraceId) ou o identificador (X-RequestId) do pedido.|

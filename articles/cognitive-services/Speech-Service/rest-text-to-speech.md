---
title: REST (referência de API de conversão de texto em fala) – serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como usar a API REST de conversão de texto em fala. Neste artigo, irá aprender sobre as opções de autorização, opções de consulta, como estruturar uma solicitação e receber uma resposta.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: b0a0d788c9fadd13b9a37f541a81945c86b37c29
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559174"
---
# <a name="text-to-speech-rest-api"></a>API REST de conversão de texto em fala

Os serviços de fala permitem que você converta o [texto em fala sintetizada](#convert-text-to-speech) e [obtenha uma lista de vozes com suporte](#get-a-list-of-voices) para uma região usando um conjunto de APIs REST. Cada ponto de extremidade disponível é associado a uma região. É necessária uma chave de assinatura para o ponto de extremidade/região que você planeja usar.

A API REST de conversão de texto em fala dá suporte à neural e a vozes de conversão de texto em fala padrão, cada uma delas com suporte para um idioma e dialeto específicos, identificados por localidade.

* Para obter uma lista completa de vozes, consulte [suporte a idiomas](language-support.md#text-to-speech).
* Para obter informações sobre a disponibilidade regional, consulte [regiões](regions.md#text-to-speech).

> [!IMPORTANT]
> Os custos variam para as vozes padrão, personalizada e neural. Para obter mais informações, consulte [preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Antes de usar essa API, entenda:

* A API REST do texto para discurso requer um cabeçalho de autorização. Isso significa que precisa para concluir uma troca de token para aceder ao serviço. Para obter mais informações, veja [Autenticação](#authentication).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Obter uma lista de vozes

O `voices/list` ponto de extremidade permite obter uma lista completa de vozes para uma região/um ponto de extremidade específico.

### <a name="regions-and-endpoints"></a>Regiões e pontos de extremidade

| Região | Ponto Final |
|--------|----------|
| Leste da Austrália | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Sul do Brasil | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Canadá Central | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| EUA Central | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Ásia Oriental | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| East US | `https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| EUA Leste 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| França Central | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Índia Central | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Leste do Japão | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Coreia do Sul Central | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| EUA Centro-Norte | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Europa do Norte | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| EUA Centro-Sul | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Sudeste Asiático | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Reino Unido Sul | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Europa Ocidental | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| EUA Oeste | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| EUA Oeste 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

### <a name="request-headers"></a>Cabeçalhos do pedido

Esta tabela lista os cabeçalhos obrigatórios e opcionais para solicitações de conversão de texto em fala.

| Cabeçalho | Descrição | Obrigatório / opcional |
|--------|-------------|---------------------|
| `Authorization` | Um token de autorização precedidas pela palavra `Bearer`. Para obter mais informações, veja [Autenticação](#authentication). | Requerido |

### <a name="request-body"></a>Corpo do pedido

Um corpo não é necessário `GET` para solicitações a esse ponto de extremidade.

### <a name="sample-request"></a>Pedido de exemplo

Essa solicitação requer apenas um cabeçalho de autorização.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Authorization: Bearer [Base64 access_token]
```

### <a name="sample-response"></a>Resposta de amostra

Essa resposta foi truncada para ilustrar a estrutura de uma resposta.

> [!NOTE]
> A disponibilidade de voz varia por região/ponto de extremidade.

```json
[
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
        "ShortName": "ar-EG-Hoda",
        "Gender": "Female",
        "Locale": "ar-EG"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)",
        "ShortName": "ar-SA-Naayf",
        "Gender": "Male",
        "Locale": "ar-SA"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)",
        "ShortName": "bg-BG-Ivan",
        "Gender": "Male",
        "Locale": "bg-BG"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)",
        "ShortName": "ca-ES-HerenaRUS",
        "Gender": "Female",
        "Locale": "ca-ES"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (cs-CZ, Jakub)",
        "ShortName": "cs-CZ-Jakub",
        "Gender": "Male",
        "Locale": "cs-CZ"
    },

    ...

]
```

### <a name="http-status-codes"></a>Códigos de estado HTTP

O código de estado HTTP para cada resposta indica o êxito ou erros comuns.

| Código de estado de HTTP | Descrição | Razão possível |
|------------------|-------------|-----------------|
| 200 | OK | A solicitação foi bem-sucedida. |
| 400 | Pedido Inválido | Um parâmetro necessário está em falta, vazios ou nulos. Em alternativa, o valor transmitido como um parâmetro obrigatório ou opcional é inválido. Um problema comum é um cabeçalho que é demasiado longo. |
| 401 | Não autorizado | O pedido não está autorizado. Certifique-se a chave de subscrição ou o token é válido e na região correto. |
| 429 | Demasiados Pedidos | Excedeu a quota ou a taxa de pedidos permitidos na sua subscrição. |
| 502 | Gateway incorrecto | Problema de rede ou do lado do servidor. Também pode indicar a cabeçalhos inválidos. |


## <a name="convert-text-to-speech"></a>Converter texto em voz

O `v1` ponto de extremidade permite converter conversão de texto em fala usando a [linguagem de marcação de síntese de fala (SSML)](speech-synthesis-markup.md).

### <a name="regions-and-endpoints"></a>Regiões e pontos de extremidade

Estas regiões são suportadas para voz com a API REST. Certifique-se de que seleciona o ponto final que corresponde à sua região de subscrição.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Cabeçalhos do pedido

Esta tabela lista os cabeçalhos obrigatórios e opcionais para solicitações de conversão de texto em fala.

| Cabeçalho | Descrição | Obrigatório / opcional |
|--------|-------------|---------------------|
| `Authorization` | Um token de autorização precedidas pela palavra `Bearer`. Para obter mais informações, veja [Autenticação](#authentication). | Requerido |
| `Content-Type` | Especifica o tipo de conteúdo para o texto fornecido. Aceite o valor: `application/ssml+xml`. | Necessário |
| `X-Microsoft-OutputFormat` | Especifica o formato de saída de áudio. Para obter uma lista completa dos valores aceites, consulte [saídas áudio](#audio-outputs). | Necessário |
| `User-Agent` | O nome da aplicação. O valor fornecido deve ter menos de 255 caracteres. | Requerido |

### <a name="audio-outputs"></a>Saídas de áudio

Esta é uma lista dos formatos de áudio suportados que são enviados em cada pedido como o `X-Microsoft-OutputFormat` cabeçalho. Cada incorpora uma velocidade de transmissão e o tipo de codificação. Os serviços de fala dão suporte a saídas de áudio de 24 kHz, 16 kHz e 8 kHz.

|||
|-|-|
| `raw-16khz-16bit-mono-pcm` | `raw-8khz-8bit-mono-mulaw` |
| `riff-8khz-8bit-mono-alaw` | `riff-8khz-8bit-mono-mulaw` |
| `riff-16khz-16bit-mono-pcm` | `audio-16khz-128kbitrate-mono-mp3` |
| `audio-16khz-64kbitrate-mono-mp3` | `audio-16khz-32kbitrate-mono-mp3` |
| `raw-24khz-16bit-mono-pcm` | `riff-24khz-16bit-mono-pcm` |
| `audio-24khz-160kbitrate-mono-mp3` | `audio-24khz-96kbitrate-mono-mp3` |
| `audio-24khz-48kbitrate-mono-mp3` | |

> [!NOTE]
> Se sua voz selecionado e o formato de saída tiverem taxas de bits diferentes, o áudio é resampled conforme necessário. No entanto, vozes de 24 kHz `audio-16khz-16kbps-mono-siren` não `riff-16khz-16kbps-mono-siren` dão suporte a formatos de saída e.

### <a name="request-body"></a>Corpo do pedido

O corpo de cada `POST` solicitação é enviado como [linguagem de marcação de síntese de fala (SSML)](speech-synthesis-markup.md). O SSML permite que você escolha a voz e o idioma da fala sintetizada retornada pelo serviço de conversão de texto em fala. Para obter uma lista completa de vozes com suporte, consulte [suporte a idiomas](language-support.md#text-to-speech).

> [!NOTE]
> Se estiver usando uma voz personalizada, o corpo de uma solicitação poderá ser enviado como texto sem formatação (ASCII ou UTF-8).

### <a name="sample-request"></a>Pedido de exemplo

Este pedido HTTP utiliza SSML para especificar a voz e linguagem. O corpo não pode ter mais de 1000 carateres.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='en-US-JessaRUS'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

Consulte nossos guias de início rápido para obter exemplos específicos de idioma:

* [.NET Core, C#](quickstart-dotnet-text-to-speech.md)
* [Python](quickstart-python-text-to-speech.md)
* [Node.js](quickstart-nodejs-text-to-speech.md)

### <a name="http-status-codes"></a>Códigos de estado HTTP

O código de estado HTTP para cada resposta indica o êxito ou erros comuns.

| Código de estado de HTTP | Descrição | Razão possível |
|------------------|-------------|-----------------|
| 200 | OK | O pedido foi concluída com êxito; o corpo da resposta é um arquivo de áudio. |
| 400 | Pedido Inválido | Um parâmetro necessário está em falta, vazios ou nulos. Em alternativa, o valor transmitido como um parâmetro obrigatório ou opcional é inválido. Um problema comum é um cabeçalho que é demasiado longo. |
| 401 | Não autorizado | O pedido não está autorizado. Certifique-se a chave de subscrição ou o token é válido e na região correto. |
| 413 | Entidade do pedido demasiado grande | A entrada SSML é superior a 1024 carateres. |
| 415 | Tipo de mídia sem suporte | É possível que o incorreto `Content-Type` tenha sido fornecido. `Content-Type`deve ser definido como `application/ssml+xml`. |
| 429 | Demasiados Pedidos | Excedeu a quota ou a taxa de pedidos permitidos na sua subscrição. |
| 502 | Gateway incorrecto | Problema de rede ou do lado do servidor. Também pode indicar a cabeçalhos inválidos. |

Se o estado HTTP é `200 OK`, o corpo da resposta contém um arquivo de áudio no formato solicitado. Este ficheiro pode ser reproduzido como tem transferidos, salva num buffer ou guardado num ficheiro.

## <a name="next-steps"></a>Passos Seguintes

- [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de idioma](how-to-customize-language-model.md)

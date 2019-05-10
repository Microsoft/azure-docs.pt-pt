---
title: Referência da API de texto para voz (REST) - serviços de voz
titleSuffix: Azure Cognitive Services
description: Saiba como utilizar a API REST do texto para voz. Neste artigo, irá aprender sobre as opções de autorização, opções de consulta, como estruturar uma solicitação e receber uma resposta.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 8ebd871c314d3ecbc0c89e6c9081926558b181fd
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65237078"
---
# <a name="text-to-speech-rest-api"></a>API REST de texto para discurso

Os serviços de voz permitem-lhe [converter texto em fala sintetizada](#convert-text-to-speech) e [obter uma lista de suportadas vozes](#get-a-list-of-voices) para uma região com um conjunto de REST APIs. Cada ponto de extremidade disponível está associado uma região. É necessária uma chave de subscrição para o ponto final/região que planeja usar.

A API REST do texto para discurso suporta vozes texto para discurso neurais e padrão, cada um deles suporta um idioma específico e dialect, identificado por localidade.

* Para obter uma lista completa de vozes, consulte [suporte de idiomas](language-support.md#text-to-speech).
* Para obter informações sobre a disponibilidade regional, consulte [regiões](regions.md#text-to-speech).

> [!IMPORTANT]
> Os custos variam para vozes neurais, padrão e personalizados. Para obter mais informações, consulte [preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Antes de utilizar esta API, compreenda:

* A API REST do texto para discurso requer um cabeçalho de autorização. Isso significa que precisa para concluir uma troca de token para aceder ao serviço. Para obter mais informações, veja [Autenticação](#authentication).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Obter uma lista de vozes

O `voices/list` ponto final permite-lhe obter uma lista completa de vozes para um região/ponto de extremidade específico.

### <a name="regions-and-endpoints"></a>Regiões e pontos de extremidade

| Região | Ponto Final |
|--------|----------|
| Leste da Austrália | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Sul do Brasil | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Canadá Central | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| EUA Central | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Ásia Oriental | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| EUA Leste | `https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
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
| E.U.A. Oeste 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

### <a name="request-headers"></a>Cabeçalhos do pedido

Esta tabela lista os cabeçalhos obrigatórios e opcionais para pedidos de texto para voz.

| Cabeçalho | Descrição | Obrigatório / opcional |
|--------|-------------|---------------------|
| `Authorization` | Um token de autorização precedidas pela palavra `Bearer`. Para obter mais informações, veja [Autenticação](#authentication). | Necessário |

### <a name="request-body"></a>Corpo do pedido

Um corpo não é necessário para `GET` pedidos para este ponto final.

### <a name="sample-request"></a>Pedido de exemplo

Este pedido requer apenas um cabeçalho de autorização.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Authorization: Bearer [Base64 access_token]
```

### <a name="sample-response"></a>Resposta de amostra

Esta resposta foi truncada para ilustrar a estrutura de uma resposta.

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
| 200 | OK | O pedido foi concluída com êxito. |
| 400 | Pedido Inválido | Um parâmetro necessário está em falta, vazios ou nulos. Em alternativa, o valor transmitido como um parâmetro obrigatório ou opcional é inválido. Um problema comum é um cabeçalho que é demasiado longo. |
| 401 | Não autorizado | O pedido não está autorizado. Certifique-se a chave de subscrição ou o token é válido e na região correto. |
| 429 | Demasiados Pedidos | Excedeu a quota ou a taxa de pedidos permitidos na sua subscrição. |
| 502 | Gateway incorrecto | Problema de rede ou do lado do servidor. Também pode indicar a cabeçalhos inválidos. |


## <a name="convert-text-to-speech"></a>Converter texto em voz

O `v1` ponto final permite-lhe converter o texto para discurso usando [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md).

### <a name="regions-and-endpoints"></a>Regiões e pontos de extremidade

Estas regiões são suportadas para voz com a API REST. Certifique-se de que seleciona o ponto final que corresponde à sua região de subscrição.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Cabeçalhos do pedido

Esta tabela lista os cabeçalhos obrigatórios e opcionais para pedidos de texto para voz.

| Cabeçalho | Descrição | Obrigatório / opcional |
|--------|-------------|---------------------|
| `Authorization` | Um token de autorização precedidas pela palavra `Bearer`. Para obter mais informações, veja [Autenticação](#authentication). | Necessário |
| `Content-Type` | Especifica o tipo de conteúdo para o texto fornecido. Aceite o valor: `application/ssml+xml`. | Necessário |
| `X-Microsoft-OutputFormat` | Especifica o formato de saída de áudio. Para obter uma lista completa dos valores aceites, consulte [saídas áudio](#audio-outputs). | Necessário |
| `User-Agent` | O nome da aplicação. O valor fornecido tem de ter menos de 255 carateres. | Necessário |

### <a name="audio-outputs"></a>Saídas de áudio

Esta é uma lista dos formatos de áudio suportados que são enviados em cada pedido como o `X-Microsoft-OutputFormat` cabeçalho. Cada incorpora uma velocidade de transmissão e o tipo de codificação. Os serviços de voz suporta KHz 24, 16 KHz, e saídas de áudio de 8 KHz.

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
> Se sua voz selecionado e o formato de saída tiverem taxas de bits diferentes, o áudio é resampled conforme necessário. No entanto, não suportam vozes de 24khz `audio-16khz-16kbps-mono-siren` e `riff-16khz-16kbps-mono-siren` formatos de saída.

### <a name="request-body"></a>Corpo do pedido

O corpo de cada `POST` solicitação é enviada como [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md). SSML permite-lhe escolher a voz e idioma de fala sintetizada, devolvido pelo serviço de texto para voz. Para obter uma lista completa de vozes suportados, consulte [suporte de idiomas](language-support.md#text-to-speech).

> [!NOTE]
> Se utilizar uma voz personalizada, o corpo de um pedido pode ser enviado como texto simples (ASCII ou UTF-8).

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

Veja os nossos inícios rápidos para obter exemplos específicos de idiomas:

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
| 429 | Demasiados Pedidos | Excedeu a quota ou a taxa de pedidos permitidos na sua subscrição. |
| 502 | Gateway incorrecto | Problema de rede ou do lado do servidor. Também pode indicar a cabeçalhos inválidos. |

Se o estado HTTP é `200 OK`, o corpo da resposta contém um arquivo de áudio no formato solicitado. Este ficheiro pode ser reproduzido como tem transferidos, salva num buffer ou guardado num ficheiro.

## <a name="next-steps"></a>Passos Seguintes

- [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de idioma](how-to-customize-language-model.md)

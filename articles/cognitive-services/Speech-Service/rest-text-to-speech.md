---
title: Referência a API de texto a fala (REST) - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Aprenda a usar a API REST text-to-speech. Neste artigo, você vai aprender sobre opções de autorização, opções de consulta, como estruturar um pedido e receber uma resposta.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: dapine
ms.openlocfilehash: 977c6ec9aa1cd6a8b8a545d123c5308bb8c51651
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409010"
---
# <a name="text-to-speech-rest-api"></a>API REST de conversão de texto em voz

O serviço de Fala permite converter [texto em discurso sintetizado](#convert-text-to-speech) e [obter uma lista de vozes apoiadas](#get-a-list-of-voices) para uma região usando um conjunto de APIs REST. Cada ponto final disponível está associado a uma região. É necessária uma chave de subscrição para o ponto final/região que planeia utilizar.

O text-to-speech REST API suporta vozes neurais e padrão de texto-a-fala, cada uma das quais apoia uma linguagem e dialeto específicos, identificados pelo local.

* Para obter uma lista completa de vozes, consulte o [suporte linguístico.](language-support.md#text-to-speech)
* Para obter informações sobre disponibilidade regional, consulte [regiões.](regions.md#text-to-speech)

> [!IMPORTANT]
> Os custos variam para vozes padrão, personalizadas e neurais. Para mais informações, consulte [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Antes de usar esta API, entenda:

* O texto-a-falar REST API requer um cabeçalho de autorização. Isto significa que você precisa completar uma troca simbólica para aceder ao serviço. Para obter mais informações, veja [Autenticação](#authentication).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Obtenha uma lista de vozes

O `voices/list` ponto final permite-lhe obter uma lista completa de vozes para uma região/ponto final específico.

### <a name="regions-and-endpoints"></a>Regiões e pontos finais

| Região | Ponto Final |
|--------|----------|
| Leste da Austrália | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Sul do Brasil | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Canadá Central | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| E.U.A. Central | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Ásia Leste | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| E.U.A. Leste | `https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| E.U.A. Leste 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| França Central | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Índia Central | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Leste do Japão | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Coreia do Sul Central | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| E.U.A. Centro-Norte | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Europa do Norte | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| E.U.A. Centro-Sul | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Ásia Sudeste | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Sul do Reino Unido | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Europa ocidental | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| E.U.A. Oeste | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| E.U.A.Oeste 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

### <a name="request-headers"></a>Cabeçalhos do pedido

Esta tabela lista seletiva e cabeçalhos opcionais para pedidos de texto-a-fala.

| Cabeçalho | Descrição | Obrigatório / Opcional |
|--------|-------------|---------------------|
| `Authorization` | Um símbolo de autorização precedido pela palavra `Bearer`. Para obter mais informações, veja [Autenticação](#authentication). | Necessário |

### <a name="request-body"></a>Corpo do pedido

Um corpo não é `GET` necessário para pedidos para este ponto final.

### <a name="sample-request"></a>Pedido de amostra

Este pedido requer apenas um cabeçalho de autorização.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Authorization: Bearer [Base64 access_token]
```

### <a name="sample-response"></a>Resposta de amostra

Esta resposta foi truncada para ilustrar a estrutura de uma resposta.

> [!NOTE]
> A disponibilidade de voz varia por região/ponto final.

```json
[
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
        "ShortName": "ar-EG-Hoda",
        "Gender": "Female",
        "Locale": "ar-EG",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)",
        "ShortName": "ar-SA-Naayf",
        "Gender": "Male",
        "Locale": "ar-SA",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)",
        "ShortName": "bg-BG-Ivan",
        "Gender": "Male",
        "Locale": "bg-BG",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)",
        "ShortName": "ca-ES-HerenaRUS",
        "Gender": "Female",
        "Locale": "ca-ES",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (zh-CN, XiaoxiaoNeural)",
        "ShortName": "zh-CN-XiaoxiaoNeural",
        "Gender": "Female",
        "Locale": "zh-CN",
        "SampleRateHertz": "24000",
        "VoiceType": "Neural"
    },

    ...
]
```

### <a name="http-status-codes"></a>Códigos de estado HTTP

O código de estado HTTP para cada resposta indica sucesso ou erros comuns.

| Código de estado de HTTP | Descrição | Possível razão |
|------------------|-------------|-----------------|
| 200 | OK | O pedido foi bem sucedido. |
| 400 | Pedido Incorreto | Falta um parâmetro necessário, vazio ou nulo. Ou, o valor passado para um parâmetro necessário ou opcional é inválido. Uma questão comum é um cabeçalho que é muito longo. |
| 401 | Não autorizado | O pedido não está autorizado. Verifique se a sua chave de subscrição ou ficha é válida e na região correta. |
| 429 | Muitos pedidos | Excedeu a quota ou taxa de pedidos permitidos para a sua subscrição. |
| 502 | Bad Gateway    | Problema de rede ou servidor. Também pode indicar cabeçalhos inválidos. |


## <a name="convert-text-to-speech"></a>Converter texto em voz

O `v1` ponto final permite-lhe converter texto-a-fala utilizando a linguagem de marcação da síntese da [fala (SSML)](speech-synthesis-markup.md).

### <a name="regions-and-endpoints"></a>Regiões e pontos finais

Estas regiões são apoiadas para o texto-a-voz utilizando a API REST. Certifique-se de que seleciona o ponto final que corresponde à sua região de subscrição.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Cabeçalhos do pedido

Esta tabela lista seletiva e cabeçalhos opcionais para pedidos de texto-a-fala.

| Cabeçalho | Descrição | Obrigatório / Opcional |
|--------|-------------|---------------------|
| `Authorization` | Um símbolo de autorização precedido pela palavra `Bearer`. Para obter mais informações, veja [Autenticação](#authentication). | Necessário |
| `Content-Type` | Especifica o tipo de conteúdo para o texto fornecido. Valor aceite: `application/ssml+xml`. | Necessário |
| `X-Microsoft-OutputFormat` | Especifica o formato de saída de áudio. Para obter uma lista completa dos valores aceites, consulte [as saídas de áudio](#audio-outputs). | Necessário |
| `User-Agent` | O nome da aplicação. O valor fornecido deve ser inferior a 255 caracteres. | Necessário |

### <a name="audio-outputs"></a>Saídas de áudio

Esta é uma lista de formatos áudio suportados `X-Microsoft-OutputFormat` que são enviados em cada pedido como cabeçalho. Cada um incorpora um tipo de bitrate e codificação. O serviço de Fala suporta saídas de áudio de 24 kHz, 16 kHz e 8 kHz.

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
> Se o seu formato de voz e saída selecionado tiver taxas de bit diferentes, o áudio é reamostrado conforme necessário. No entanto, as vozes `audio-16khz-16kbps-mono-siren` `riff-16khz-16kbps-mono-siren` de 24 kHz não suportam e formatos de saída.

### <a name="request-body"></a>Corpo do pedido

O corpo `POST` de cada pedido é enviado como Linguagem de Marcação da [Síntese da Fala (SSML)](speech-synthesis-markup.md). O SSML permite-lhe escolher a voz e a linguagem do discurso sintetizado devolvido pelo serviço de texto à fala. Para obter uma lista completa de vozes apoiadas, consulte o [suporte linguístico.](language-support.md#text-to-speech)

> [!NOTE]
> Se utilizar uma voz personalizada, o corpo de um pedido pode ser enviado como texto simples (ASCII ou UTF-8).

### <a name="sample-request"></a>Pedido de amostra

Este pedido HTTP utiliza o SSML para especificar a voz e a linguagem. Se o comprimento do corpo for longo e o áudio resultante exceder 10 minutos - é truncado até 10 minutos. Por outras palavras, o comprimento do áudio não pode exceder 10 minutos.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='en-US-AriaRUS'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

Consulte os nossos quickstarts para exemplos específicos da linguagem:

* [.NET Core, C #](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=dotnetcore)
* [Python](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-python)
* [Node.js](quickstart-nodejs-text-to-speech.md)

### <a name="http-status-codes"></a>Códigos de estado HTTP

O código de estado HTTP para cada resposta indica sucesso ou erros comuns.

| Código de estado de HTTP | Descrição | Possível razão |
|------------------|-------------|-----------------|
| 200 | OK | O pedido foi bem sucedido; o corpo de resposta é um ficheiro áudio. |
| 400 | Pedido Incorreto | Falta um parâmetro necessário, vazio ou nulo. Ou, o valor passado para um parâmetro necessário ou opcional é inválido. Uma questão comum é um cabeçalho que é muito longo. |
| 401 | Não autorizado | O pedido não está autorizado. Verifique se a sua chave de subscrição ou ficha é válida e na região correta. |
| 413 | Entidade de Pedido muito grande | A entrada SSML é superior a 1024 caracteres. |
| 415 | Tipo de mídia não suportado | É possível que `Content-Type` o mal tenha sido dado. `Content-Type`deve ser `application/ssml+xml`definido para . |
| 429 | Muitos pedidos | Excedeu a quota ou taxa de pedidos permitidos para a sua subscrição. |
| 502 | Bad Gateway    | Problema de rede ou servidor. Também pode indicar cabeçalhos inválidos. |

Se o estado `200 OK`HTTP for, o corpo da resposta contém um ficheiro áudio no formato solicitado. Este ficheiro pode ser reproduzido à medida que é transferido, guardado para um tampão, ou guardado para um ficheiro.

## <a name="next-steps"></a>Passos seguintes

- [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services)
- [Síntese assíncrona para áudio de forma longa](quickstarts/text-to-speech/async-synthesis-long-form-audio.md)
- [Introdução à Voz Personalizada](how-to-custom-voice.md)

---
title: Referência de API text-to-speech (REST) - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como utilizar a API DE REPOUSO TEXT-to-speech. Neste artigo, você vai aprender sobre opções de autorização, opções de consulta, como estruturar um pedido e receber uma resposta.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.author: trbye
ms.custom: references_regions
ms.openlocfilehash: 9bee5fa6b76aad9305947ebe460f37c399340038
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98033479"
---
# <a name="text-to-speech-rest-api"></a>API REST de conversão de texto em voz

O serviço Discurso [permite-lhe converter texto em discurso sintetizado](#convert-text-to-speech) e [obter uma lista de vozes apoiadas](#get-a-list-of-voices) para uma região usando um conjunto de APIs REST. Cada ponto final disponível está associado a uma região. É necessária uma chave de subscrição para o ponto final/região que pretende utilizar.

A API de texto-a-discurso suporta vozes neurais e padrão de texto para a fala, cada uma das quais suporta uma linguagem e dialeto específicos, identificados pelo local.

* Para obter uma lista completa de vozes, consulte [o suporte linguístico.](language-support.md#text-to-speech)
* Para obter informações sobre a disponibilidade regional, consulte [as regiões.](regions.md#text-to-speech)

> [!IMPORTANT]
> Os custos variam para vozes normais, personalizadas e neurais. Para mais informações, consulte [a Fixação de Preços.](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)

Antes de utilizar esta API, compreenda:

* A API DE REPOUSO text-to-speech requer um cabeçalho de autorização. Isto significa que você precisa completar uma troca simbólica para aceder ao serviço. Para obter mais informações, veja [Autenticação](#authentication).

> [!TIP]
> Veja [este artigo](sovereign-clouds.md) para o Governo Azure e para os pontos finais da Azure China.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Obtenha uma lista de vozes

O `voices/list` ponto final permite-lhe obter uma lista completa de vozes para uma região/ponto final específico.

### <a name="regions-and-endpoints"></a>Regiões e pontos finais

| Região | Ponto final |
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
| Sudeste Asiático | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Sul do Reino Unido | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Europa Ocidental | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| E.U.A. Oeste | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| E.U.A. Oeste 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

> [!TIP]
> [As vozes na pré-visualização](language-support.md#neural-voices-in-preview) só estão disponíveis nestas três regiões: Leste dos EUA, Europa Ocidental e Sudeste Asiático.

### <a name="request-headers"></a>Cabeçalhos do pedido

Esta tabela lista os cabeçalhos necessários e opcionais para pedidos de texto-a-voz.

| Cabeçalho | Descrição | Obrigatório / Opcional |
|--------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | A chave de subscrição do serviço de discurso. | Ou este cabeçalho ou `Authorization` é necessário. |
| `Authorization` | Um sinal de autorização precedido pela palavra `Bearer` . Para obter mais informações, veja [Autenticação](#authentication). | Ou este cabeçalho ou `Ocp-Apim-Subscription-Key` é necessário. |



### <a name="request-body"></a>Corpo do pedido

Um corpo não é necessário para `GET` pedidos para este ponto final.

### <a name="sample-request"></a>Pedido de amostra

Este pedido requer apenas um cabeçalho de autorização.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
```

### <a name="sample-response"></a>Resposta de amostra

Esta resposta foi truncada para ilustrar a estrutura de uma resposta.

> [!NOTE]
> A disponibilidade de voz varia por região/ponto final.

```json
[
  {
    "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
    "DisplayName": "Hoda",
    "LocalName": "هدى",
    "ShortName": "ar-EG-Hoda",
    "Gender": "Female",
    "Locale": "ar-EG",
    "SampleRateHertz": "16000",
    "VoiceType": "Standard",
    "Status": "GA"
  },

...
      
    {
    "Name": "Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)",
    "DisplayName": "Aria",
    "LocalName": "Aria",
    "ShortName": "en-US-AriaNeural",
    "Gender": "Female",
    "Locale": "en-US",
    "StyleList": [
      "chat",
      "customerservice",
      "newscast-casual",
      "newscast-formal",
      "cheerful",
      "empathetic"
    ],
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "GA"
  },
  
  ...
    
     {
    "Name": "Microsoft Server Speech Text to Speech Voice (ga-IE, OrlaNeural)",
    "DisplayName": "Orla",
    "LocalName": "Orla",
    "ShortName": "ga-IE-OrlaNeural",
    "Gender": "Female",
    "Locale": "ga-IE",
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "Preview"
  },
  
  ...
    
   {
    "Name": "Microsoft Server Speech Text to Speech Voice (zh-CN, YunxiNeural)",
    "DisplayName": "Yunxi",
    "LocalName": "云希",
    "ShortName": "zh-CN-YunxiNeural",
    "Gender": "Male",
    "Locale": "zh-CN",
    "StyleList": [
      "Calm",
      "Fearful",
      "Cheerful",
      "Disgruntled",
      "Serious",
      "Angry",
      "Sad",
      "Depressed",
      "Embarrassed"
    ],
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "Preview"
  },

    ...
]
```

### <a name="http-status-codes"></a>Códigos de estado HTTP

O código de estado HTTP para cada resposta indica sucesso ou erros comuns.

| Código de estado de HTTP | Descrição | Motivo possível |
|------------------|-------------|-----------------|
| 200 | OK | O pedido foi bem sucedido. |
| 400 | Pedido Incorreto | Falta um parâmetro necessário, vazio ou nulo. Ou, o valor passado para um parâmetro necessário ou opcional é inválido. Uma questão comum é um cabeçalho que é demasiado longo. |
| 401 | Não autorizado | O pedido não está autorizado. Verifique se a chave de subscrição ou o token são válidos e na região correta. |
| 429 | Muitos pedidos | Excedeu a quota ou taxa de pedidos permitidos para a sua subscrição. |
| 502 | Bad Gateway    | Problema do lado da rede ou do servidor. Pode também indicar cabeçalhos inválidos. |


## <a name="convert-text-to-speech"></a>Converter texto em voz

O `v1` ponto final permite converter texto-a-discurso utilizando a linguagem de [marcação da síntese de fala (SSML)](speech-synthesis-markup.md).

### <a name="regions-and-endpoints"></a>Regiões e pontos finais

Estas regiões são apoiadas por texto-a-voz utilizando a API REST. Certifique-se de que seleciona o ponto final que corresponde à sua região de subscrição.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Cabeçalhos do pedido

Esta tabela lista os cabeçalhos necessários e opcionais para pedidos de texto-a-voz.

| Cabeçalho | Descrição | Obrigatório / Opcional |
|--------|-------------|---------------------|
| `Authorization` | Um sinal de autorização precedido pela palavra `Bearer` . Para obter mais informações, veja [Autenticação](#authentication). | Obrigatório |
| `Content-Type` | Especifica o tipo de conteúdo para o texto fornecido. Valor aceite: `application/ssml+xml` . | Obrigatório |
| `X-Microsoft-OutputFormat` | Especifica o formato de saída de áudio. Para obter uma lista completa dos valores aceites, consulte [as saídas de áudio](#audio-outputs). | Obrigatório |
| `User-Agent` | O nome da aplicação. O valor fornecido deve ser inferior a 255 caracteres. | Obrigatório |

### <a name="audio-outputs"></a>Saídas de áudio

Esta é uma lista de formatos áudio suportados que são enviados em cada pedido como `X-Microsoft-OutputFormat` cabeçalho. Cada um incorpora um tipo de bitrate e codificação. O serviço Discurso suporta saídas de áudio de 24 kHz, 16 kHz e 8 kHz.

```output
raw-16khz-16bit-mono-pcm            raw-8khz-8bit-mono-mulaw
riff-8khz-8bit-mono-alaw            riff-8khz-8bit-mono-mulaw
riff-16khz-16bit-mono-pcm           audio-16khz-128kbitrate-mono-mp3
audio-16khz-64kbitrate-mono-mp3     audio-16khz-32kbitrate-mono-mp3
raw-24khz-16bit-mono-pcm            riff-24khz-16bit-mono-pcm
audio-24khz-160kbitrate-mono-mp3    audio-24khz-96kbitrate-mono-mp3
audio-24khz-48kbitrate-mono-mp3     ogg-24khz-16bit-mono-opus
```

> [!NOTE]
> Se o seu formato de voz e saída selecionado tiver taxas de bits diferentes, o áudio é resmaltado se necessário. ogg-24khz-16bit-mono-opus pode ser descodificado com [opus codec](https://opus-codec.org/downloads/)

### <a name="request-body"></a>Corpo do pedido

O corpo de cada pedido é enviado como Linguagem de Marcação de `POST` [Síntese de Fala (SSML)](speech-synthesis-markup.md). O SSML permite-lhe escolher a voz e a linguagem do discurso sintetizado devolvido pelo serviço de texto-a-voz. Para obter uma lista completa de vozes apoiadas, consulte [o suporte linguístico](language-support.md#text-to-speech).

> [!NOTE]
> Se utilizar uma voz personalizada, o corpo de um pedido pode ser enviado como texto simples (ASCII ou UTF-8).

### <a name="sample-request"></a>Pedido de amostra

Este pedido HTTP utiliza SSML para especificar a voz e o idioma. Se o comprimento do corpo for longo e o áudio resultante exceder 10 minutos - é truncado a 10 minutos. Por outras palavras, o comprimento do áudio não pode exceder 10 minutos.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-24khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='en-US-AriaNeural'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

### <a name="http-status-codes"></a>Códigos de estado HTTP

O código de estado HTTP para cada resposta indica sucesso ou erros comuns.

| Código de estado de HTTP | Descrição | Motivo possível |
|------------------|-------------|-----------------|
| 200 | OK | O pedido foi bem sucedido; o corpo de resposta é um ficheiro áudio. |
| 400 | Pedido Incorreto | Falta um parâmetro necessário, vazio ou nulo. Ou, o valor passado para um parâmetro necessário ou opcional é inválido. Uma questão comum é um cabeçalho que é demasiado longo. |
| 401 | Não autorizado | O pedido não está autorizado. Verifique se a chave de subscrição ou o token são válidos e na região correta. |
| 415 | Tipo de mídia não suportado | É possível que o erro `Content-Type` tenha sido fornecido. `Content-Type` deve ser definido para `application/ssml+xml` . |
| 429 | Muitos pedidos | Excedeu a quota ou taxa de pedidos permitidos para a sua subscrição. |
| 502 | Bad Gateway    | Problema do lado da rede ou do servidor. Pode também indicar cabeçalhos inválidos. |

Se o estado HTTP `200 OK` for, o corpo da resposta contém um ficheiro áudio no formato solicitado. Este ficheiro pode ser reproduzido à medida que é transferido, guardado para um tampão, ou guardado num ficheiro.

## <a name="next-steps"></a>Passos seguintes

- [Criar uma conta do Azure gratuita](https://azure.microsoft.com/free/cognitive-services/)
- [Síntese assíncronea para áudio de forma longa](quickstarts/text-to-speech/async-synthesis-long-form-audio.md)
- [Introdução à Voz Personalizada](how-to-custom-voice.md)

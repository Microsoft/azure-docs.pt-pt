---
title: REST (referência de API de conversão de texto em fala) – serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como usar a API REST de conversão de texto em fala. Neste artigo, você aprenderá sobre as opções de autorização, opções de consulta, como estruturar uma solicitação e receber uma resposta.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 9fa0157bd458d2de028cab8ff9c836761e99562f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481209"
---
# <a name="text-to-speech-rest-api"></a>API REST de conversão de texto em fala

Os serviços de fala permitem que você [converta o texto em fala sintetizada](#convert-text-to-speech) e [obtenha uma lista de vozes com suporte](#get-a-list-of-voices) para uma região usando um conjunto de APIs REST. Cada ponto de extremidade disponível é associado a uma região. É necessária uma chave de assinatura para o ponto de extremidade/região que você planeja usar.

A API REST de conversão de texto em fala dá suporte à neural e a vozes de conversão de texto em fala padrão, cada uma delas com suporte para um idioma e dialeto específicos, identificados por localidade.

* Para obter uma lista completa de vozes, consulte [suporte a idiomas](language-support.md#text-to-speech).
* Para obter informações sobre a disponibilidade regional, consulte [regiões](regions.md#text-to-speech).

> [!IMPORTANT]
> Os custos variam para as vozes padrão, personalizada e neural. Para obter mais informações, consulte [preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Antes de usar essa API, entenda:

* A API REST de conversão de texto em fala requer um cabeçalho de autorização. Isso significa que você precisa concluir uma troca de token para acessar o serviço. Para obter mais informações, veja [Autenticação](#authentication).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Obter uma lista de vozes

O ponto de extremidade `voices/list` permite obter uma lista completa de vozes para uma região/um ponto de extremidade específico.

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
| Este do Japão | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Coreia do Sul Central | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| E.U.A. Centro-Norte | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Europa do Norte | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| EUA Centro-Sul | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Sudeste Asiático | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Sul do Reino Unido | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Europa Ocidental | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Oeste dos E.U.A. | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| EUA Oeste 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

### <a name="request-headers"></a>Cabeçalhos de solicitação

Esta tabela lista os cabeçalhos obrigatórios e opcionais para solicitações de conversão de texto em fala.

| Cabeçalho | Descrição | Obrigatório/opcional |
|--------|-------------|---------------------|
| `Authorization` | Um token de autorização precedido pela palavra `Bearer`. Para obter mais informações, veja [Autenticação](#authentication). | Necessário |

### <a name="request-body"></a>Corpo do pedido

Um corpo não é necessário para solicitações de `GET` para esse ponto de extremidade.

### <a name="sample-request"></a>Exemplo de solicitação

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

### <a name="http-status-codes"></a>Códigos de status HTTP

O código de status HTTP para cada resposta indica erros de êxito ou comuns.

| Código de estado de HTTP | Descrição | Possível motivo |
|------------------|-------------|-----------------|
| 200 | OK | A solicitação foi bem-sucedida. |
| 400 | Pedido Incorreto | Um parâmetro necessário está ausente, vazio ou é nulo. Ou, o valor passado para um parâmetro obrigatório ou opcional é inválido. Um problema comum é um cabeçalho muito longo. |
| 401 | Não autorizado | A solicitação não é autorizada. Verifique se sua chave de assinatura ou token é válido e na região correta. |
| 429 | Número excessivo de solicitações | Você excedeu a cota ou a taxa de solicitações permitidas para sua assinatura. |
| 502 | Gateway inadequado | Problema de rede ou do servidor. Também pode indicar cabeçalhos inválidos. |


## <a name="convert-text-to-speech"></a>Converter texto em voz

O ponto de extremidade `v1` permite que você converta conversão de texto em fala usando a [linguagem de marcação de síntese de fala (SSML)](speech-synthesis-markup.md).

### <a name="regions-and-endpoints"></a>Regiões e pontos de extremidade

Essas regiões têm suporte para conversão de texto em fala usando a API REST. Certifique-se de selecionar o ponto de extremidade que corresponde à sua região de assinatura.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Cabeçalhos de solicitação

Esta tabela lista os cabeçalhos obrigatórios e opcionais para solicitações de conversão de texto em fala.

| Cabeçalho | Descrição | Obrigatório/opcional |
|--------|-------------|---------------------|
| `Authorization` | Um token de autorização precedido pela palavra `Bearer`. Para obter mais informações, veja [Autenticação](#authentication). | Necessário |
| `Content-Type` | Especifica o tipo de conteúdo para o texto fornecido. Valor aceito: `application/ssml+xml`. | Necessário |
| `X-Microsoft-OutputFormat` | Especifica o formato de saída de áudio. Para obter uma lista completa de valores aceitos, consulte [saídas de áudio](#audio-outputs). | Necessário |
| `User-Agent` | O nome do aplicativo. O valor fornecido deve ter menos de 255 caracteres. | Necessário |

### <a name="audio-outputs"></a>Saídas de áudio

Esta é uma lista de formatos de áudio com suporte que são enviados em cada solicitação como o cabeçalho de `X-Microsoft-OutputFormat`. Cada incorpora um tipo de taxa de bits e de codificação. Os serviços de fala dão suporte a saídas de áudio de 24 kHz, 16 kHz e 8 kHz.

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
> Se o formato de voz e saída selecionado tiver taxas de bits diferentes, o áudio será reamostrado conforme necessário. No entanto, as vozes de 24 kHz não dão suporte a `audio-16khz-16kbps-mono-siren` e `riff-16khz-16kbps-mono-siren` formatos de saída.

### <a name="request-body"></a>Corpo do pedido

O corpo de cada solicitação de `POST` é enviado como [linguagem de marcação de síntese de fala (SSML)](speech-synthesis-markup.md). O SSML permite que você escolha a voz e o idioma da fala sintetizada retornada pelo serviço de conversão de texto em fala. Para obter uma lista completa de vozes com suporte, consulte [suporte a idiomas](language-support.md#text-to-speech).

> [!NOTE]
> Se estiver usando uma voz personalizada, o corpo de uma solicitação poderá ser enviado como texto sem formatação (ASCII ou UTF-8).

### <a name="sample-request"></a>Exemplo de solicitação

Essa solicitação HTTP usa SSML para especificar a voz e o idioma. O corpo não pode exceder 1.000 caracteres.

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

* [.NET Core,C#](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=dotnetcore)
* [Python](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-python)
* [Node.js](quickstart-nodejs-text-to-speech.md)

### <a name="http-status-codes"></a>Códigos de status HTTP

O código de status HTTP para cada resposta indica erros de êxito ou comuns.

| Código de estado de HTTP | Descrição | Possível motivo |
|------------------|-------------|-----------------|
| 200 | OK | A solicitação foi bem-sucedida; o corpo da resposta é um arquivo de áudio. |
| 400 | Pedido Incorreto | Um parâmetro necessário está ausente, vazio ou é nulo. Ou, o valor passado para um parâmetro obrigatório ou opcional é inválido. Um problema comum é um cabeçalho muito longo. |
| 401 | Não autorizado | A solicitação não é autorizada. Verifique se sua chave de assinatura ou token é válido e na região correta. |
| 413 | Entidade de solicitação muito grande | A entrada SSML tem mais de 1024 caracteres. |
| 415 | Tipo de mídia sem suporte | É possível que o `Content-Type` errado seja fornecido. `Content-Type` deve ser definido como `application/ssml+xml`. |
| 429 | Número excessivo de solicitações | Você excedeu a cota ou a taxa de solicitações permitidas para sua assinatura. |
| 502 | Gateway inadequado | Problema de rede ou do servidor. Também pode indicar cabeçalhos inválidos. |

Se o status HTTP for `200 OK`, o corpo da resposta conterá um arquivo de áudio no formato solicitado. Esse arquivo pode ser reproduzido à medida que é transferido, salvo em um buffer ou salvo em um arquivo.

## <a name="next-steps"></a>Passos seguintes

- [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de idioma](how-to-customize-language-model.md)

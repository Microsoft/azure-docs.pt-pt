---
title: API de Conversão de Texto em Fala do serviço de fala da Microsoft | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Use a API de texto para fala para fornecer conversão de texto para fala em tempo real em uma variedade de vozes e idiomas
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ee9b0b47fb88cba948bc06db6eb83fe9c076fe40
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966861"
---
# <a name="bing-text-to-speech-api"></a>API de conversão de texto em fala do Bing

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

## <a name="Introduction"></a>Introdução

Com a API de texto para fala do Bing, seu aplicativo pode enviar solicitações HTTP para um servidor de nuvem, em que o texto é resumido instantaneamente na fala de som humano e retornado como um arquivo de áudio. Essa API pode ser usada em muitos contextos diferentes para fornecer conversão em tempo real de texto em fala em uma variedade de diferentes vozes e idiomas.

## <a name="VoiceSynReq"></a>Solicitação de síntese de voz

### <a name="Subscription"></a>Token de autorização

Cada solicitação de síntese de voz requer um token de acesso JWT (token Web JSON). O token de acesso JWT é transmitido no cabeçalho da solicitação de fala. O token tem um tempo de expiração de 10 minutos. Para obter informações sobre a assinatura e a obtenção de chaves de API que são usadas para recuperar Tokens de acesso JWT válidos, consulte [cognitiva Services Subscription](https://azure.microsoft.com/try/cognitive-services/).

A chave de API é passada para o serviço de token. Por exemplo:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

As informações de cabeçalho necessárias para acesso de token são as seguintes.

Name| Formato | Descrição
----|----|----
OCP-Apim-Subscription-Key | ASCII | Chave de subscrição

O serviço de token retorna o token de acesso `text/plain`JWT como. Em seguida, o JWT é passado `Base64 access_token` como um para o ponto de extremidade de fala como um cabeçalho de `Bearer`autorização prefixado com a cadeia de caracteres. Por exemplo:

`Authorization: Bearer [Base64 access_token]`

Os clientes devem usar o ponto de extremidade a seguir para acessar o serviço de conversão de texto em fala:

`https://speech.platform.bing.com/synthesize`

>[!NOTE]
>Até que você tenha adquirido um token de acesso com sua chave de assinatura, conforme descrito anteriormente `403 Forbidden` , esse link gera um erro de resposta.

### <a name="Http"></a>Cabeçalhos HTTP

A tabela a seguir mostra os cabeçalhos HTTP que são usados para solicitações de síntese de voz.

Cabeçalho |Value |Comentários
----|----|----
Tipo de conteúdo | aplicativo/SSML + XML | O tipo de conteúdo de entrada.
X-Microsoft-OutputFormat | **1.** SSML-16kHz-16 bits-mono-TTS <br> **2.** RAW-16kHz-16 bits-mono-PCM <br>**3.** Audio-16kHz-16kbps-mono-Siren <br> **4.** riff-16kHz-16kbps-mono-Siren <br> **5.** riff-16kHz-16 bits-mono-PCM <br> **6.** Audio-16kHz-128kbitrate-mono-mp3 <br> **7.** Audio-16kHz-64kbitrate-mono-mp3 <br> **8.** Audio-16kHz-32kbitrate-mono-mp3 | O formato de áudio de saída.
X-Search-AppId | Um GUID (somente Hex, sem traços) | Uma ID que identifica exclusivamente o aplicativo cliente. Pode ser a ID da loja para aplicativos. Se um não estiver disponível, a ID poderá ser gerada pelo usuário para um aplicativo.
X-Search-ClientID | Um GUID (somente Hex, sem traços) | Uma ID que identifica exclusivamente uma instância do aplicativo para cada instalação.
Agente do usuário | Nome da aplicação | O nome do aplicativo é necessário e deve ter menos de 255 caracteres.
Autorização | Token de autorização |  Consulte a seção <a href="#Subscription">token de autorização</a> .

### <a name="InputParam"></a>Parâmetros de entrada

As solicitações para a API de texto para fala do Bing são feitas usando chamadas HTTP POST. Os cabeçalhos são especificados na seção anterior. O corpo contém uma entrada de linguagem de marcação de síntese de fala (SSML) que representa o texto a ser sintetizado. Para obter uma descrição da marcação usada para controlar aspectos de fala, como o idioma e o sexo do palestrante, consulte a [especificação do W3C do SSML](https://www.w3.org/TR/speech-synthesis/).

>[!NOTE]
>O tamanho máximo da entrada SSML com suporte é de 1.024 caracteres, incluindo todas as marcas.

###  <a name="SampleVoiceOR"></a>Exemplo: solicitação de saída de voz

Um exemplo de uma solicitação de saída de voz é o seguinte:

```HTTP
POST /synthesize
HTTP/1.1
Host: speech.platform.bing.com

X-Microsoft-OutputFormat: riff-8khz-8bit-mono-mulaw
Content-Type: application/ssml+xml
Host: speech.platform.bing.com
Content-Length: 197
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female' name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>Microsoft Bing Voice Output API</voice></speak>
```

## <a name="VoiceOutResponse"></a>Resposta de saída de voz

A API de conversão de texto em fala do Bing usa HTTP POST para enviar o áudio de volta para o cliente. A resposta da API contém o fluxo de áudio e o codec e corresponde ao formato de saída solicitado. O áudio retornado para uma determinada solicitação não deve exceder 15 segundos.

### <a name="SuccessfulRecResponse"></a>Exemplo: resposta de síntese bem-sucedida

O código a seguir é um exemplo de uma resposta JSON para uma solicitação de síntese de voz bem-sucedida. Os comentários e a formatação do código são apenas para fins deste exemplo e são omitidos da resposta real.

```HTTP
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

### <a name="RecFailure"></a>Exemplo: falha de síntese

O código de exemplo a seguir mostra uma resposta JSON para uma falha de consulta de síntese de voz:

```HTTP
HTTP/1.1 400 XML parser error
Content-Type: text/xml
Content-Length: 0
```

### <a name="ErrorResponse"></a>Respostas de erro

Erro | Descrição
----|----
HTTP/400 solicitação inadequada | Um parâmetro necessário está ausente, vazio ou nulo ou o valor passado para um parâmetro obrigatório ou opcional é inválido. Um motivo para obter a resposta "inválida" é passar um valor de cadeia de caracteres que seja maior que o comprimento permitido. Uma breve descrição do parâmetro problemático está incluída.
HTTP/401 não autorizado | O pedido não está autorizado.
HTTP/413 RequestEntityTooLarge  | A entrada SSML é maior do que o que tem suporte.
HTTP/502 BadGateway | Há um problema relacionado à rede ou um problema no lado do servidor.

Um exemplo de uma resposta de erro é o seguinte:

```HTTP
HTTP/1.0 400 Bad Request
Content-Length: XXX
Content-Type: text/plain; charset=UTF-8

Voice name not supported
```

## <a name="ChangeSSML"></a>Alterando a saída de voz via SSML

O texto para Speech API da Microsoft dá suporte a SSML 1,0, conforme definido na [versão 1,0 da linguagem de marcação de síntese de fala (SSML)](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/)do W3C. Esta seção mostra exemplos de como alterar determinadas características da saída de voz gerada, como taxa de fala, pronúncia etc., usando marcas SSML.

1. Adicionando quebra

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)'> Welcome to use Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.</voice> </speak>
   ```

2. Taxa de alteração de fala

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody rate="+30.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

3. Pronúncia

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'> <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme></voice> </speak>
   ```

4. Volume de alterações

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody volume="+20.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

5. Alterar o argumento de venda

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Welcome to use <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

6. Alterar contorno de Prosody

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody contour="(80%,+20%) (90%,+30%)" >Good morning.</prosody></voice> </speak>
   ```

> [!NOTE]
> Observe que os dados de áudio têm que ser 8K ou 16K WAV arquivados no seguinte formato: **Código CRC** (CRC-32): 4 bytes (DWORD) com intervalo válido 0x00000000 ~ 0xFFFFFFFF; **Sinalizador de formato de áudio**: 4 bytes (DWORD) com intervalo válido 0x00000000 ~ 0xFFFFFFFF; **Contagem de amostras**: 4 bytes (DWORD) com intervalo válido 0x00000000 ~ 0x7FFFFFFF; **Tamanho do corpo binário**: 4 bytes (DWORD) com intervalo válido 0x00000000 ~ 0x7FFFFFFF; **Corpo binário**: n bytes.

## <a name="SampleApp"></a>Aplicativo de exemplo

Para obter detalhes de implementação, consulte o [aplicativo de exemplo de conversão de texto em fala do Visual C#.net](https://github.com/Microsoft/Cognitive-Speech-TTS/blob/master/Samples-Http/CSharp/TTSProgram.cs).

## <a name="SupLocales"></a>Localidades e fontes de voz com suporte

A tabela a seguir identifica algumas das localidades com suporte e fontes de voz relacionadas.

Região | Género | Mapeamento de nome de serviço
---------|--------|------------
ar-ex * | Feminino | "Microsoft Server voz texto para voz de voz (ar-por exemplo, Hoda)"
ar SA | Masculino | "Microsoft Server voz texto para voz de voz (ar-SA, Naayf)"
bg-BG | Masculino | "Microsoft Server voz texto em voz voz (bg-BG, Ivan)"
ES de AC | Feminino | "Microsoft Server voz texto em voz voz (AC-ES, HerenaRUS)"
cs-CZ | Masculino | "Microsoft Server voz texto para voz de voz (cs-CZ, Jakub)"
da-DK | Feminino | "Microsoft Server voz texto para voz de voz (da-DK, HelleRUS)"
Alemanha-AT | Masculino | "Microsoft Server voz texto para voz de voz (Alemanha-AT, Michael)"
Alemanha-CH | Masculino | "Microsoft Server voz texto para voz de voz (Alemanha-CH, Karsten)"
de-DE | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (de-DE, Hedda)"
de-DE | Feminino | "Microsoft Server voz texto para voz de voz (de-DE, HeddaRUS)"
de-DE | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (de-DE, Rodrigo, Apollo)"
el GR | Masculino | "Microsoft Server voz texto para voz de voz (el-GR, Stefanos)"
EN-AU | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (en-AU, Catherine)"
EN-AU | Feminino | "Microsoft Server voz texto para voz de voz (en-AU, HayleyRUS)"
en-CA | Feminino | "Microsoft Server voz texto para voz de voz (en-CA, Linda)"
en-CA | Feminino | "Microsoft Server voz texto para voz de voz (en-CA, HeatherRUS)"
en-GB | Feminino | "Microsoft Server voz texto para voz de voz (en-GB, Susan, Apollo)"
en-GB | Feminino | "Microsoft Server voz texto para voz de voz (en-GB, HazelRUS)"
en-GB | Masculino | "Microsoft Server voz texto para voz de voz (en-GB, George, Apollo)"
EN-IE | Masculino | "Microsoft Server voz texto para voz de voz (en-IE, Sean)"
EN-IN | Feminino | "Microsoft Server voz texto para voz de voz (en-IN, Heera, Apollo)"
EN-IN | Feminino | "Microsoft Server voz texto para voz de voz (en-IN, PriyaRUS)"
EN-IN | Masculino | "Microsoft Server voz texto para voz de voz (en-IN, Ravi, Apollo)"
en-US | Feminino | "Microsoft Server voz texto para voz de voz (en-US, ZiraRUS)"
en-US | Feminino | "Microsoft Server voz texto para voz de voz (en-US, JessaRUS)"
en-US | Masculino | "Microsoft Server voz texto para voz de voz (en-US, BenjaminRUS)"
es-ES | Feminino | "Microsoft Server voz texto em voz voz (es-ES, Laura, Apollo)"
es-ES | Feminino | "Microsoft Server voz texto em voz voz (es-ES, HelenaRUS)"
es-ES | Masculino | "Microsoft Server voz texto em voz voz (es-ES, Pablo, Apollo)"
es-MX | Feminino | "Voz de texto em voz de voz do Microsoft Server (es-MX, HildaRUS)"
es-MX | Masculino | "Voz de texto em voz de voz do Microsoft Server (es-MX, Raul, Apollo)"
fi-FI | Feminino | "Microsoft Server voz texto para voz de voz (fi-FI, HeidiRUS)"
fr-CA | Feminino | "Microsoft Server voz texto em voz voz (fr-AC, Caroline)"
fr-CA | Feminino | "Microsoft Server voz texto em voz voz (fr-AC, HarmonieRUS)"
FR-CH | Masculino | "Microsoft Server voz texto em voz voz (fr-CH, Guillaume)"
FR-FR | Feminino | "Microsoft Server voz texto em voz voz (fr-FR, Julie, Apollo)"
FR-FR | Feminino | "Microsoft Server voz texto em voz voz (fr-FR, HortenseRUS)"
FR-FR | Masculino | "Microsoft Server voz texto em voz voz (fr-FR, Paul, Apollo)"
he-IL| Masculino| "Microsoft Server voz texto para voz de voz (he-IL, Asaf)"
IN | Feminino | "Microsoft Server voz texto para voz de voz (hi-IN, Kalpana, Apollo)"
IN | Feminino | "Microsoft Server voz texto para voz de voz (hi-IN, Kalpana)"
IN | Masculino | "Microsoft Server voz texto para voz de voz (hi-IN, Hemant)"
RH de RH | Masculino | "Microsoft Server voz texto em voz voz (RH-RH, Matej)"
hu-HU | Masculino | "Microsoft Server voz texto para voz de voz (hu-HU, Szabolcs)"
id-ID | Masculino | "Microsoft Server voz texto para voz de voz (id-ID, Andika)"
it-IT | Masculino | "Microsoft Server voz texto para voz de voz (it-IT, Cosimo, Apollo)"
it-IT | Feminino | "Microsoft Server voz texto para voz de voz (it-IT, LuciaRUS)"
ja-JP | Feminino | "Microsoft Server voz texto em voz voz (ja-JP, Ayumi, Apollo)"
ja-JP | Masculino | "Microsoft Server voz texto em voz voz (ja-JP, Ichiro, Apollo)"
ja-JP | Feminino | "Microsoft Server voz texto em voz voz (ja-JP, HarukaRUS)"
ko-KR | Feminino | "Microsoft Server voz texto para voz de voz (ko-KR, HeamiRUS)"
MS-MY | Masculino | "Voz de texto em voz de voz do Microsoft Server (ms-MY, Rizwan)"
nb-NO | Feminino | "Voz de texto em voz de voz do Microsoft Server (não-nb, HuldaRUS)"
NL-NL | Feminino | "Microsoft Server voz texto em voz voz (nl-NL, HannaRUS)"
pl-PL | Feminino | "Microsoft Server voz texto em voz voz (pl-PL, PaulinaRUS)"
pt-BR | Feminino | "Microsoft Server voz texto em voz voz (pt-BR, HeloisaRUS)"
pt-BR | Masculino | "Microsoft Server voz texto em voz voz (pt-BR, Daniel, Apollo)"
pt-PT | Feminino | "Microsoft Server voz texto em voz voz (pt-PT, HeliaRUS)"
RO-RO | Masculino | "Microsoft Server voz texto para voz de voz (ro-RO, Andrei)"
ru-RU | Feminino | "Microsoft Server voz texto em voz voz (ru-RU, Irina, Apollo)"
ru-RU | Masculino | "Microsoft Server voz texto em voz voz (ru-RU, Pavel, Apollo)"
ru-RU | Feminino | "Microsoft Server voz texto em voz voz (ru-RU, EkaterinaRUS)"
sk SK | Masculino | "Microsoft Server voz texto em voz voz (sk-SK, Filip)"
IS SL | Masculino | "Microsoft Server voz texto em voz voz (sl-SI, Lado)"
SV-SE | Feminino | "Microsoft Server voz texto para voz de voz (sv-SE, HedvigRUS)"
es-IN | Masculino | "Microsoft Server voz texto para voz de voz (dados-IN, Valluvar)"
th-TH | Masculino | "Microsoft Server voz texto em voz voz (th-TH, Pattara)"
tr-TR | Feminino | "Microsoft Server voz texto em voz voz (tr-TR, SedaRUS)"
vi-VN | Masculino | "Voz de texto em voz de voz do Microsoft Server (vi-VN, um)"
zh-CN | Feminino | "Microsoft Server voz texto para voz de voz (zh-CN, HuihuiRUS)"
zh-CN | Feminino | "Microsoft Server voz texto para voz de voz (zh-CN, Yaoyao, Apollo)"
zh-CN | Masculino | "Microsoft Server voz texto para voz de voz (zh-CN, Kangkang, Apollo)"
zh-HK | Feminino | "Microsoft Server voz texto para voz de voz (zh-HK Tracy, Apollo)"
zh-HK | Feminino | "Microsoft Server voz texto para voz de voz (zh-HK TracyRUS)"
zh-HK | Masculino | "Microsoft Server voz texto para voz de voz (zh-HK Danny, Apollo)"
zh-TW | Feminino | "Microsoft Server voz texto para voz de voz (zh-TW, Yating, Apollo)"
zh-TW | Feminino | "Microsoft Server voz texto para voz de voz (zh-TW, HanHanRUS)"
zh-TW | Masculino | "Microsoft Server voz texto para voz de voz (zh-TW, Zhiwei, Apollo)"

 \* ar-ex dá suporte a Modern padrão árabe (MSA).

> [!NOTE]
> Observe que os nomes de serviço anteriores do **Microsoft Server speech conversão de texto em fala voz (CS-CZ, Vit)** e **o microsoft Server Speech conversão de texto em fala Voice (EN-IE, Shaun)** serão preteridos após 3/31/2018, a fim de otimizar os API de fala do Bing técnicas. Atualize seu código com os nomes atualizados.

## <a name="TrouNSupport"></a>Solução de problemas e suporte

Poste todas as perguntas e problemas no fórum do [fala do Bing serviço](https://social.msdn.microsoft.com/Forums/en-US/home?forum=SpeechService) do MSDN. Inclua detalhes completos, como:

* Um exemplo da cadeia de caracteres de solicitação completa.
* Se aplicável, a saída completa de uma solicitação com falha, que inclui IDs de log.
* A porcentagem de solicitações que estão falhando.

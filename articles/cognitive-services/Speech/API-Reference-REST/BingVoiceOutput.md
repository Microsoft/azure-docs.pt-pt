---
title: Texto em voz API do serviço de voz da Microsoft | Documentos da Microsoft
titlesuffix: Azure Cognitive Services
description: Utilize o texto em voz, API para fornecer a conversão de texto para voz em tempo real de várias vozes e linguagens
services: cognitive-services
author: priyaravi20
manager: yanbo
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: priyar
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 8f7de542610731f88104591dc27baac1406a9514
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57548409"
---
# <a name="bing-text-to-speech-api"></a>API do Bing texto em voz

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

## <a name="Introduction"></a>Introdução

Com o texto em voz API do Bing, a aplicação pode enviar solicitações HTTP para um servidor na nuvem, em que o texto de forma instantânea é sintetizado em voz humano e retornado como um arquivo de áudio. Esta API pode ser usada em vários contextos diferentes para fornecer a conversão de texto para voz em tempo real numa variedade de diferentes vozes e linguagens.

## <a name="VoiceSynReq"></a>Pedido de síntese de voz

### <a name="Subscription"></a>Token de autorização

Cada solicitação de síntese de voz exige um token de acesso do JSON Web Token (JWT). O token de acesso do JWT é transmitido no cabeçalho do pedido de voz. O token tem uma hora de expiração de 10 minutos. Para obter informações sobre subscrever e obter chaves de API que são usadas para recuperar os tokens de acesso do JWT válidos, consulte [subscrição de serviços cognitivos](https://azure.microsoft.com/try/cognitive-services/).

A chave de API é passada para o serviço de token. Por exemplo:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

Segue-se as informações de cabeçalho necessário para acesso de token.

Name| Formato | Descrição
----|----|----
OCP-Apim-Subscription-Key | ASCII | Chave de subscrição

O serviço de token devolve o token de acesso do JWT como `text/plain`. Em seguida, o JWT é passado como um `Base64 access_token` para o ponto final de voz como um cabeçalho de autorização com a cadeia de prefixo `Bearer`. Por exemplo:

`Authorization: Bearer [Base64 access_token]`

Os clientes tem de utilizar o seguinte ponto de extremidade para aceder ao serviço de texto para voz:

`https://speech.platform.bing.com/synthesize`

>[!NOTE]
>Até que tiver adquirido um token de acesso com a sua chave de subscrição, conforme descrito anteriormente, esta ligação gera um `403 Forbidden` erro de resposta.

### <a name="Http"></a>Cabeçalhos HTTP

A tabela seguinte mostra os cabeçalhos HTTP que são utilizados para pedidos de síntese de voz.

Cabeçalho |Value |Comentários
----|----|----
Content-Type | aplicação/ssml + xml | O tipo de conteúdo de entrada.
X-Microsoft-OutputFormat | **1.** ssml-16khz-16bit-mono-tts <br> **2.** brutos-16 khz-16 bits-mono-pcm <br>**3.** áudio-16 khz-16 kbps-mono-siren <br> **4.** riff-16khz-16kbps-mono-siren <br> **5.** riff-16khz-16bit-mono-pcm <br> **6.** áudio-16 khz-128kbitrate-mono-mp3 <br> **7.** áudio-16 khz-64kbitrate-mono-mp3 <br> **8.** áudio-16 khz-32kbitrate-mono-mp3 | O formato de áudio de saída.
X-Search-AppId | Um GUID (hex apenas, sem traços) | Um ID que identifica exclusivamente o aplicativo cliente. Isso pode ser o ID de loja de aplicações. Se não estiver disponível, o ID pode ser gerado para um aplicativo de usuário.
X-Search-ClientID | Um GUID (hex apenas, sem traços) | Um ID que identifica exclusivamente uma instância de aplicação para cada instalação.
Agente de utilizador | Nome da aplicação | O nome da aplicação é necessário e tem de ter menos de 255 carateres.
Autorização | Token de autorização |  Consulte a <a href="#Subscription">token de autorização</a> secção.

### <a name="InputParam"></a>Parâmetros de entrada

Pedidos para o API do Bing texto em voz são feitos através de chamadas HTTP POST. Os cabeçalhos estão especificados na secção anterior. O corpo contém uma entrada de Speech Synthesis Markup Language (SSML) que representa o texto a ser sintetizado. Para obter uma descrição da marcação utilizada para controlar os aspectos de voz, como o idioma e o género do falante, consulte a [especificação do W3C SSML](https://www.w3.org/TR/speech-synthesis/).

>[!NOTE]
>O tamanho máximo da entrada SSML suportada é 1024 carateres, incluindo todas as etiquetas.

###  <a name="SampleVoiceOR"></a>Exemplo: pedido de saída de voz

Segue-se um exemplo de um pedido de saída de voz:

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

O API do Bing texto em voz utiliza HTTP POST para enviar áudio volta ao cliente. A resposta da API contém o fluxo de áudio e o codec e corresponder o formato de saída pedida. O áudio devolvido para uma determinada solicitação não pode exceder os 15 segundos.

### <a name="SuccessfulRecResponse"></a>Exemplo: resposta de síntese bem-sucedida

O código a seguir é um exemplo de uma resposta JSON para um pedido de síntese de voz concluída com êxito. Os comentários e a formatação do código são para efeitos deste exemplo apenas e são omitidos da resposta real.

```HTTP
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

### <a name="RecFailure"></a>Exemplo: falha de síntese

O código de exemplo seguinte mostra uma resposta JSON a uma falha de consulta de síntese de voz:

```HTTP
HTTP/1.1 400 XML parser error
Content-Type: text/xml
Content-Length: 0
```

### <a name="ErrorResponse"></a>Respostas de erro

Erro | Descrição
----|----
Pedido HTTP/400 inválido | Um parâmetro necessário está em falta, vazios ou nulos ou o valor transmitido como um parâmetro obrigatório ou opcional é inválido. Uma das razões para obter a resposta "inválida" é passar um valor de cadeia de caracteres que excede o comprimento permitido. Uma breve descrição do parâmetro problemático está incluída.
HTTP/Erro 401 não autorizado | O pedido não está autorizado.
HTTP/413 RequestEntityTooLarge  | A entrada SSML é maior do que o que é suportado.
/ Erros de HTTP 502 BadGateway | Existe um problema relacionado com a rede ou um problema do lado do servidor.

Um exemplo de uma resposta de erro é o seguinte:

```HTTP
HTTP/1.0 400 Bad Request
Content-Length: XXX
Content-Type: text/plain; charset=UTF-8

Voice name not supported
```

## <a name="ChangeSSML"></a>Alterar a saída de voz por meio de SSML

API de voz de Microsoft suporta SSML 1.0, conforme definido no W3C [Speech Synthesis Markup Language (SSML) versão 1.0](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/). Esta secção mostra exemplos de alterar a determinadas características da saída gerada de voz, como fala de velocidade, pronúncia etc., através de etiquetas SSML.

1. Adicionar a garantia de reparação

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

6. Contour prosody de alteração

  ```
  <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody contour="(80%,+20%) (90%,+30%)" >Good morning.</prosody></voice> </speak>
  ```

> [!NOTE]
> Tenha em atenção os dados de áudio tem de ser k de 8 ou 16 wav de k arquivado no seguinte formato: **Código CRC** (CRC 32): 4 bytes (DWORD) com o intervalo válido 0x00000000 ~ 0xFFFFFFFF; **Sinalizador de formato de áudio**: 4 bytes (DWORD) com o intervalo válido 0x00000000 ~ 0xFFFFFFFF; **Contagem de amostras**: 4 bytes (DWORD) com o intervalo válido 0x00000000 ~ 0x7FFFFFFF; **Tamanho do corpo binário**: 4 bytes (DWORD) com o intervalo válido 0x00000000 ~ 0x7FFFFFFF; **Corpo binário**: n bytes.

## <a name="SampleApp"></a>Exemplo de aplicação

Para obter detalhes de implementação, consulte a [aplicação de exemplo de texto para discurso de .NET do Visual C](https://github.com/Microsoft/Cognitive-Speech-TTS/blob/master/Samples-Http/CSharp/TTSProgram.cs).

## <a name="SupLocales"></a>Regiões suportadas e tipos de voz

A tabela seguinte identifica algumas das regiões suportadas e tipos de voz relacionados.

Região | Género | Mapeamento de nome de serviço
---------|--------|------------
ar-por exemplo * | Feminino | "Microsoft Server voz texto para voz de voz (ar-por exemplo, Hoda)"
ar SA | Masculino | "Microsoft Server voz texto para voz de voz (ar-SA, Naayf)"
bg-BG | Masculino | "Microsoft Server voz texto em voz voz (bg-BG, Ivan)"
ES de AC | Feminino | "Microsoft Server voz texto em voz voz (AC-ES, HerenaRUS)"
cs-CZ | Masculino | "Microsoft Server voz texto para voz de voz (cs-CZ, Jakub)"
da-DK | Feminino | "Microsoft Server voz texto para voz de voz (da-DK, HelleRUS)"
Alemanha-AT | Masculino | "Microsoft Server voz texto para voz de voz (Alemanha-AT, Michael)"
Alemanha-CH | Masculino | "Microsoft Server voz texto para voz de voz (Alemanha-CH, Karsten)"
de-DE | Feminino | "Microsoft Server voz texto para voz de voz (de-DE, Hedda)"
de-DE | Feminino | "Microsoft Server voz texto para voz de voz (de-DE, HeddaRUS)"
de-DE | Masculino | "Microsoft Server voz texto para voz de voz (de-DE, Stefan, Apollo)"
el GR | Masculino | "Microsoft Server voz texto para voz de voz (el-GR, Stefanos)"
EN-AU | Feminino | "Microsoft Server voz texto para voz de voz (en-AU, Catherine)"
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
 * ar-por exemplo, oferece suporte a Árabe padrão modernos (MSA).

> [!NOTE]
> Tenha em atenção que os nomes de serviço anteriores **voz de servidor texto para voz de voz da Microsoft (cs-CZ, Vit)** e **Microsoft Server voz texto para voz de voz (en-IE, Shaun)** serão preteridas após 3/31/2018 no ordem para otimizar os recursos da API de voz do Bing. Atualize o seu código com os nomes de atualizado.

## <a name="TrouNSupport"></a>Resolução de problemas e suporte

Publicar todas as perguntas e questões para o [serviço de voz do Bing](https://social.msdn.microsoft.com/Forums/en-US/home?forum=SpeechService) fórum do MSDN. Inclua detalhes completos, como:

* Um exemplo da cadeia de caracteres completos do pedido.
* Se aplicável, a saída completa de uma solicitação com falha, o que inclui sessão IDs.
* A percentagem de pedidos que estão a falhar.

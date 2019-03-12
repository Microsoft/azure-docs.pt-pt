---
title: APIs REST - serviços de voz dos serviços de voz
titleSuffix: Azure Cognitive Services
description: Saiba como utilizar as APIs de REST de voz em texto e texto para voz. Neste artigo, irá aprender sobre as opções de autorização, opções de consulta, como estruturar uma solicitação e receber uma resposta.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 83e613616c37272ed2fdd42288678a57c520b8fa
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57532461"
---
# <a name="speech-service-rest-apis"></a>APIs REST do serviço de voz

Como alternativa para o [SDK de voz](speech-sdk.md), o serviço de voz permite-lhe converter voz em texto e voz com um conjunto de REST APIs. Cada ponto de final de acesso está associado uma região. Seu aplicativo requer uma chave de subscrição para o ponto final que pretende utilizar.

Antes de utilizar as APIs REST, compreenda:
* Os pedidos de voz em texto com a API REST só podem conter 10 segundos de áudio gravado.
* A API de REST de voz em texto devolve apenas os resultados finais. Resultados parciais não são fornecidos.
* A API REST do texto para discurso requer um cabeçalho de autorização. Isso significa que precisa para concluir uma troca de token para aceder ao serviço. Para obter mais informações, veja [Autenticação](#authentication).

## <a name="authentication"></a>Autenticação

Cada pedido para qualquer API REST de voz em texto ou texto para discurso requer um cabeçalho de autorização. Esta tabela mostra quais cabeçalhos são suportados para cada serviço:

| Cabeçalhos de autorização suportados | Conversão de voz em texto | Conversão de texto em voz |
|------------------------|----------------|----------------|
| OCP-Apim-Subscription-Key | Sim | Não |
| Autorização: Portador | Sim | Sim |

Ao utilizar o `Ocp-Apim-Subscription-Key` cabeçalho, só é necessário para fornecer a chave de subscrição. Por exemplo:

```
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

Ao utilizar o `Authorization: Bearer` cabeçalho, for necessário para fazer um pedido para o `issueToken` ponto final. Neste pedido, trocar a chave de subscrição para um token de acesso que é válido durante 10 minutos. As próximas seções, aprenderá como obter um token, utilizam um token e um token de atualização.

### <a name="how-to-get-an-access-token"></a>Como obter um token de acesso

Para obter um token de acesso, terá de fazer um pedido para o `issueToken` ponto final a utilizar o `Ocp-Apim-Subscription-Key` e a chave de subscrição.

Essas regiões e pontos de extremidade são suportados:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

Utilize estes exemplos para criar o seu pedido de token de acesso.

#### <a name="http-sample"></a>Exemplo HTTP

Neste exemplo é uma solicitação HTTP simples para obter um token. Substitua `YOUR_SUBSCRIPTION_KEY` com a sua chave de subscrição do serviço de voz. Se a sua subscrição não está na região E.U.A. oeste, substitua o `Host` cabeçalho com o nome de anfitrião de sua região.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

O corpo da resposta contém o token de acesso no formato JSON Web Token (JWT).

#### <a name="powershell-sample"></a>Exemplo do PowerShell

Neste exemplo é um script do PowerShell simple para obter um token de acesso. Substitua `YOUR_SUBSCRIPTION_KEY` com a sua chave de subscrição do serviço de voz. Certifique-se utilizar o ponto final correto para a região que corresponde à sua subscrição. Neste exemplo está definido para E.U.A. oeste.

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken
 -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

#### <a name="curl-sample"></a>exemplo de cURL

cURL é uma ferramenta da linha de comandos disponível no Linux (e no subsistema Windows para Linux). Este comando cURL ilustra como obter um token de acesso. Substitua `YOUR_SUBSCRIPTION_KEY` com a sua chave de subscrição do serviço de voz. Certifique-se utilizar o ponto final correto para a região que corresponde à sua subscrição. Neste exemplo está definido para E.U.A. oeste.

```cli
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>Exemplo C#

Isso C# classe ilustra como obter um token de acesso. Passe a chave de subscrição do serviço de voz ao instanciar a classe. Se a sua subscrição não está na região E.U.A. oeste, altere o valor de `FetchTokenUri` de acordo com a região para a sua subscrição.

```cs
/*
    * This class demonstrates how to get a valid access token.
    */
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchTokenAsync(FetchTokenUri, subscriptionKey).Result;
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private async Task<string> FetchTokenAsync(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="how-to-use-an-access-token"></a>Como utilizar um token de acesso

O token de acesso deve ser enviado para o serviço como o `Authorization: Bearer <TOKEN>` cabeçalho. Cada token de acesso é válido durante 10 minutos. Pode obter um novo token em qualquer altura, no entanto, para minimizar o tráfego de rede e a latência, recomendamos que utilize o mesmo token para nove minutos.

Este é um pedido HTTP de exemplo para a API REST do texto para voz:

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.tts.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    Hello, world!
</voice></speak>
```

### <a name="how-to-renew-an-access-token-using-c"></a>Como renovar um através do token de acessoC#

Isso C# código é uma substituição completa para a classe apresentada anteriormente. O `Authentication` classe obtém automaticamente um novo token de acesso em nove minutos, uso um timer. Esta abordagem garante que um token válido está sempre disponível enquanto seu programa está em execução.

> [!NOTE]
> Em vez de usar um timer, pode armazenar um carimbo de quando o último token foi obtido. Em seguida, pode pedir um novo apenas se for encontra prestes a expirar. Esta abordagem evita solicitar novos tokens desnecessariamente e poderá ser mais adequada para programas que efetuam pedidos de voz raros.

Como antes, certifique-se de que o `FetchTokenUri` valor corresponde à sua região de subscrição. Passe a chave de subscrição ao instanciar a classe.

```cs
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;
    private Timer accessTokenRenewer;

    //Access token expires every 10 minutes. Renew it every 9 minutes.
    private const int RefreshTokenDuration = 9;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchToken(FetchTokenUri, subscriptionKey).Result;

        // renew the token on set duration.
        accessTokenRenewer = new Timer(new TimerCallback(OnTokenExpiredCallback),
                                        this,
                                        TimeSpan.FromMinutes(RefreshTokenDuration),
                                        TimeSpan.FromMilliseconds(-1));
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private void RenewAccessToken()
    {
        this.token = FetchToken(FetchTokenUri, this.subscriptionKey).Result;
        Console.WriteLine("Renewed token.");
    }

    private void OnTokenExpiredCallback(object stateInfo)
    {
        try
        {
            RenewAccessToken();
        }
        catch (Exception ex)
        {
            Console.WriteLine(string.Format("Failed renewing access token. Details: {0}", ex.Message));
        }
        finally
        {
            try
            {
                accessTokenRenewer.Change(TimeSpan.FromMinutes(RefreshTokenDuration), TimeSpan.FromMilliseconds(-1));
            }
            catch (Exception ex)
            {
                Console.WriteLine(string.Format("Failed to reschedule the timer to renew access token. Details: {0}", ex.Message));
            }
        }
    }

    private async Task<string> FetchToken(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

## <a name="speech-to-text-api"></a>API de voz em texto

A API de REST de voz em texto apenas suporta discursos de curtos. Pedidos podem conter até 10 segundos de áudio com uma duração total de 14 segundos. A API REST devolve apenas os resultados finais, resultados não parciais ou provisórias.

Se enviar áudio mais tempo é um requisito para a sua aplicação, considere utilizar o [SDK de voz](speech-sdk.md) ou [transcrição do batch](batch-transcription.md).

### <a name="regions-and-endpoints"></a>Regiões e pontos de extremidade

Estas regiões são suportadas para transcrição de voz em texto com a API REST. Certifique-se de que seleciona o ponto final que corresponde à sua região de subscrição.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="query-parameters"></a>Parâmetros de consulta

Esses parâmetros podem ser incluídos na cadeia de consulta da solicitação REST.

| Parâmetro | Descrição | Obrigatório / opcional |
|-----------|-------------|---------------------|
| `language` | Identifica o idioma falado que está a ser reconhecido. Ver [idiomas suportados](language-support.md#speech-to-text). | Necessário |
| `format` | Especifica o formato de resultado. Aceite os valores são `simple` e `detailed`. Os resultados da simples incluem `RecognitionStatus`, `DisplayText`, `Offset`, e `Duration`. Respostas detalhadas incluem vários resultados com valores de confiança e quatro diferentes representações. A predefinição é `simple`. | Opcional |
| `profanity` | Especifica como lidar com linguagem inapropriada nos resultados de reconhecimento. Aceite os valores são `masked`, que substitui a linguagem inapropriada por asteriscos, `removed`, linguagem inapropriada todos os que remover o resultado ou `raw`, que inclui a linguagem inapropriada no resultado. A predefinição é `masked`. | Opcional |

### <a name="request-headers"></a>Cabeçalhos do pedido

Esta tabela lista os cabeçalhos obrigatórios e opcionais para pedidos de voz em texto.

|Cabeçalho| Descrição | Obrigatório / opcional |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | A chave de subscrição do serviço de voz. | Este cabeçalho de qualquer um dos ou `Authorization` é necessária. |
| `Authorization` | Um token de autorização precedidas pela palavra `Bearer`. Para obter mais informações, veja [Autenticação](#authentication). | Este cabeçalho de qualquer um dos ou `Ocp-Apim-Subscription-Key` é necessária. |
| `Content-type` | Descreve o formato e o codec de dados de áudio fornecidos. Aceite os valores são `audio/wav; codecs=audio/pcm; samplerate=16000` e `audio/ogg; codecs=opus`. | Necessário |
| `Transfer-Encoding` | Especifica que os dados em segmentos de áudio são enviados, em vez de um único arquivo. Utilize este cabeçalho apenas se a segmentação de dados de áudio. | Opcional |
| `Expect` | Se utilizar a transferência em partes, enviar `Expect: 100-continue`. O serviço de voz reconhece o pedido inicial e awaits dados adicionais.| Necessário se o envio de dados de áudio em partes. |
| `Accept` | Se for fornecido, tem de ser `application/json`. O serviço de voz fornece resultados em JSON. Algumas estruturas de solicitação da Web, forneça um valor incompatível predefinido se não especificar um, portanto, é recomendável sempre incluir `Accept`. | Opcional mas recomendado. |

### <a name="audio-formats"></a>Formatos de áudio

Áudio é enviado no corpo do HTTP `POST` pedido. Tem de ser um dos formatos nesta tabela:

| Formato | Codec | Velocidade de transmissão | Taxa de exemplo |
|--------|-------|---------|-------------|
| WAV | PCM | 16-bit | 16 kHz, mono |
| OGG | OPUS | 16-bit | 16 kHz, mono |

>[!NOTE]
>Os formatos acima são suportados através da REST API e WebSocket no serviço de voz. O [SDK de voz](speech-sdk.md) atualmente apenas suporta o WAV formatar com o codec PCM.

### <a name="sample-request"></a>Pedido de exemplo

Este é um pedido HTTP comuns. O exemplo abaixo inclui o nome de anfitrião e cabeçalhos necessários. É importante observar que o serviço espera também os dados de áudio, o que não estão incluídos neste exemplo. Como mencionado anteriormente, segmentação recomenda, no entanto, não é necessário.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="http-status-codes"></a>Códigos de estado HTTP

O código de estado HTTP para cada resposta indica o êxito ou erros comuns.

| Código de estado de HTTP | Descrição | Razão possível |
|------------------|-------------|-----------------|
| 100 | Continuar | O pedido inicial foi aceite. Continue com a enviar o resto dos dados. (Utilizado com a transferência em partes). |
| 200 | OK | O pedido foi concluída com êxito; o corpo da resposta é um objeto JSON. |
| 400 | Pedido incorreto | Código de idioma fornecido ou não é um idioma suportado; arquivo de áudio inválido. |
| 401 | Não autorizado | Chave de subscrição ou autorização token é inválido na região especificada ou ponto final inválido. |
| 403 | Proibido | Chave de subscrição ou autorização em falta token. |

### <a name="chunked-transfer"></a>Transferência em partes

A transferência (`Transfer-Encoding: chunked`) pode ajudar a reduzir a latência de reconhecimento, pois permite que o serviço de voz para começar a processar o ficheiro de áudio, enquanto que está a ser transmitido. A API REST não fornece resultados parciais ou provisórias. Esta opção destina-se exclusivamente para melhorar a capacidade de resposta.

Este exemplo de código mostra como enviar áudio em blocos. Apenas o primeiro segmento deve conter cabeçalho do arquivo de áudio. `request` está ligado um objeto HTTPWebRequest para o ponto final REST adequado. `audioFile` é o caminho para um arquivo de áudio no disco.

```csharp

    HttpWebRequest request = null;
    request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
    request.SendChunked = true;
    request.Accept = @"application/json;text/xml";
    request.Method = "POST";
    request.ProtocolVersion = HttpVersion.Version11;
    request.Host = host;
    request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
    request.Headers["Ocp-Apim-Subscription-Key"] = args[1];
    request.AllowWriteStreamBuffering = false;

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```

### <a name="response-parameters"></a>Parâmetros de resposta

Os resultados são fornecidos como JSON. O `simple` formato inclui estes campos de nível superior.

| Parâmetro | Descrição  |
|-----------|--------------|
|`RecognitionStatus`|Estado, tais como `Success` para reconhecimento bem-sucedido. Consulte a tabela seguinte.|
|`DisplayText`|O texto reconhecido depois de capitalização, pontuação, normalização do texto inversa (conversão de texto falado forms mais curto, por exemplo, 200 para "duzentos" ou "Dr. Smith"para"doutor smith") e a máscara de linguagem inapropriada. Apresentar apenas com êxito.|
|`Offset`|O tempo (em unidades de 100 nanossegundos), no qual a voz reconhecido começa no fluxo de áudio.|
|`Duration`|A duração (em unidades de 100 nanossegundos) do voz reconhecido no fluxo de áudio.|

O `RecognitionStatus` campo pode conter estes valores:

| Estado | Descrição |
|--------|-------------|
| `Success` | O reconhecimento foi concluída com êxito e o `DisplayText` campo está presente. |
| `NoMatch` | Conversão de voz foi detetada no fluxo de áudio, mas sem palavras do idioma de destino foram correspondidas. Normalmente, significa que o idioma de reconhecimento é um idioma diferente daquele que o utilizador está a falar. |
| `InitialSilenceTimeout` | O início do fluxo de áudio continha apenas silêncio e o serviço excedeu o tempo aguardar por voz. |
| `BabbleTimeout` | O início do fluxo de áudio continha apenas ruído e o serviço excedeu o tempo aguardar por voz. |
| `Error` | O serviço de reconhecimento de obteve um erro interno e não foi possível continuar. Tente novamente se possível. |

> [!NOTE]
> Se o áudio consiste apenas em linguagem inapropriada e o `profanity` parâmetro de consulta está definido como `remove`, o serviço não devolve um resultado de conversão de voz.

O `detailed` formato inclui os mesmos dados que o `simple` formatar, juntamente com `NBest`, uma lista de alternativas interpretações sobre o mesmo resultado de reconhecimento de voz. Esses resultados estão ordenados do maior probabilidade de menos provável que a primeira entrada é o mesmo como o resultado do reconhecimento principal.  Ao utilizar o `detailed` formato, `DisplayText` é fornecido como `Display` para cada resultado no `NBest` lista.

Cada objeto no `NBest` lista inclui:

| Parâmetro | Descrição |
|-----------|-------------|
| `Confidence` | A pontuação de confiança da entrada de 0,0 (sem confiança) para 1,0 (confiança total) |
| `Lexical` | O formulário léxico do texto reconhecido: as palavras reais reconhecidas. |
| `ITN` | O formulário ("canonical") normalizado de texto de inverso do texto reconhecido, com o telefone números, números, abreviaturas ("doutor smith" para "dr smith") e outras transformações aplicadas. |
| `MaskedITN` | O formulário ITN com a máscara de linguagem inapropriada aplicada, se solicitado. |
| `Display` | O formulário de apresentação do texto reconhecido, com a pontuação e capitalização adicionado. Este parâmetro é o mesmo que `DisplayText` fornecida quando o formato está definido como `simple`. |

### <a name="sample-responses"></a>Respostas de exemplo

Esta é uma resposta típica para `simple` reconhecimento.

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Esta é uma resposta típica para `detailed` reconhecimento.

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="text-to-speech-api"></a>API de texto para discurso

A API REST do texto para discurso suporta vozes texto para discurso neurais e padrão, cada um deles suporta um idioma específico e dialect, identificado por localidade.

* Para obter uma lista completa de vozes, consulte [suporte de idiomas](language-support.md#text-to-speech).
* Para obter informações sobre a disponibilidade regional, consulte [regiões](regions.md#text-to-speech).

> [!IMPORTANT]
> Os custos variam para vozes neurais, padrão e personalizados. Para obter mais informações, consulte [preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="request-headers"></a>Cabeçalhos do pedido

Esta tabela lista os cabeçalhos obrigatórios e opcionais para pedidos de voz em texto.

| Cabeçalho | Descrição | Obrigatório / opcional |
|--------|-------------|---------------------|
| `Authorization` | Um token de autorização precedidas pela palavra `Bearer`. Para obter mais informações, consulte [autenticação](#authentication). | Necessário |
| `Content-Type` | Especifica o tipo de conteúdo para o texto fornecido. Aceite o valor: `application/ssml+xml`. | Necessário |
| `X-Microsoft-OutputFormat` | Especifica o formato de saída de áudio. Para obter uma lista completa dos valores aceites, consulte [saídas áudio](#audio-outputs). | Necessário |
| `User-Agent` | O nome da aplicação. Tem de ter menos de 255 carateres. | Necessário |

### <a name="audio-outputs"></a>Saídas de áudio

Esta é uma lista dos formatos de áudio suportados que são enviados em cada pedido como o `X-Microsoft-OutputFormat` cabeçalho. Cada incorpora uma velocidade de transmissão e o tipo de codificação. O serviço de voz suporta 24 KHz, 16 KHz e saídas de áudio de 8 KHz.

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
    name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

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

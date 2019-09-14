---
title: Fala do Bing protocolo WebSocket | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Documentação do protocolo para Fala do Bing com base em WebSockets
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e7f51d49624d5019bec058a2d12f6ca2f1366938
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966876"
---
# <a name="bing-speech-websocket-protocol"></a>Fala do Bing protocolo WebSocket

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Fala do Bing é uma plataforma baseada em nuvem que apresenta os algoritmos mais avançados disponíveis para converter áudio falado em texto. O protocolo Fala do Bing define a [configuração de conexão](#connection-establishment) entre aplicativos cliente e o serviço e as mensagens de reconhecimento de fala trocadas entre as contrapartes ([mensagens originadas pelo cliente](#client-originated-messages) e [mensagens originadas pelo serviço](#service-originated-messages) ). Além disso, [mensagens de telemetria](#telemetry-schema) e [tratamento de erros](#error-handling) são descritos.

## <a name="connection-establishment"></a>Estabelecimento de conexão

O protocolo de serviço de fala segue a especificação padrão do WebSocket [IETF RFC 6455](https://tools.ietf.org/html/rfc6455). Uma conexão WebSocket começa como uma solicitação HTTP que contém cabeçalhos HTTP que indicam o desejo do cliente de atualizar a conexão para um WebSocket em vez de usar a semântica HTTP. O servidor indica sua disposição para participar da conexão WebSocket, retornando uma resposta `101 Switching Protocols` http. Após a troca desse Handshake, o cliente e o serviço mantêm o Soquete aberto e começam a usar um protocolo baseado em mensagem para enviar e receber informações.

Para iniciar o handshake do WebSocket, o aplicativo cliente envia uma solicitação HTTPS GET para o serviço. Ele inclui cabeçalhos de atualização do WebSocket padrão junto com outros cabeçalhos que são específicos para a fala.

```HTTP
GET /speech/recognition/interactive/cognitiveservices/v1 HTTP/1.1
Host: speech.platform.bing.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Sec-WebSocket-Version: 13
Authorization: t=EwCIAgALBAAUWkziSCJKS1VkhugDegv7L0eAAJqBYKKTzpPZOeGk7RfZmdBhYY28jl&p=
X-ConnectionId: A140CAF92F71469FA41C72C7B5849253
Origin: https://speech.platform.bing.com
```

O serviço responde com:

```HTTP
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: upgrade
Sec-WebSocket-Key: 2PTTXbeeBXlrrUNsY15n01d/Pcc=
Set-Cookie: SpeechServiceToken=AAAAABAAWTC8ncb8COL; expires=Wed, 17 Aug 2016 15:39:06 GMT; domain=bing.com; path="/"
Date: Wed, 17 Aug 2016 15:03:52 GMT
```

Todas as solicitações de fala exigem a criptografia [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) . Não há suporte para o uso de solicitações de fala não criptografadas. Há suporte para a seguinte versão de TLS:

* TLS 1,2

### <a name="connection-identifier"></a>Identificador de conexão

O serviço de fala requer que todos os clientes incluam uma ID exclusiva para identificar a conexão. Os clientes *devem* incluir o cabeçalho *X-ConnectionID* quando iniciarem um handshake do WebSocket. O cabeçalho *X-ConnectionID* deve ser um valor de UUID ( [identificador universal exclusivo](https://en.wikipedia.org/wiki/Universally_unique_identifier) ). Solicitações de atualização de WebSocket que não incluem o *X-ConnectionID*, não especificam um valor para o cabeçalho *x-ConnectionID* ou não incluem um valor UUID válido são rejeitadas pelo serviço com uma resposta http `400 Bad Request` .

### <a name="authorization"></a>Autorização

Além dos cabeçalhos de handshake WebSocket padrão, as solicitações de fala exigem um cabeçalho de *autorização* . As solicitações de conexão sem esse cabeçalho são rejeitadas pelo serviço com `403 Forbidden` uma resposta http.

O cabeçalho *Authorization* deve conter um token de acesso JSON Web token (JWT).

Para obter informações sobre como assinar e obter chaves de API que são usadas para recuperar Tokens de acesso JWT válidos, consulte a página de [assinatura de serviços cognitivas](https://azure.microsoft.com/try/cognitive-services/) .

A chave de API é passada para o serviço de token. Por exemplo:

``` HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

As informações de cabeçalho a seguir são necessárias para acesso ao token.

| Name | Formato | Descrição |
|----|----|----|
| OCP-Apim-Subscription-Key | ASCII | Chave de subscrição |

O serviço de token retorna o token de acesso `text/plain`JWT como. Em seguida, o JWT é passado `Base64 access_token` como um para o handshake como um cabeçalho de *autorização* prefixado `Bearer`com a cadeia de caracteres. Por exemplo:

`Authorization: Bearer [Base64 access_token]`

### <a name="cookies"></a>Arar

Os clientes *devem* oferecer suporte a cookies http, conforme especificado no [RFC 6265](https://tools.ietf.org/html/rfc6265).

### <a name="http-redirection"></a>Redirecionamento de HTTP

Os clientes *devem* oferecer suporte aos mecanismos de redirecionamento padrão especificados pela [especificação do protocolo http](https://www.w3.org/Protocols/rfc2616/rfc2616.html).

### <a name="speech-endpoints"></a>Pontos de extremidade de fala

Os clientes *devem* usar um ponto de extremidade apropriado do serviço de fala. O ponto de extremidade é baseado no modo de reconhecimento e no idioma. A tabela mostra alguns exemplos.

| Modo | Path | URI de serviço |
| -----|-----|-----|
| Interativo | /speech/recognition/interactive/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| Conversação | /speech/recognition/conversation/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US |
| Comandos | /speech/recognition/dictation/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR |

Para obter mais informações, consulte a página [URI de serviço](../GetStarted/GetStartedREST.md#service-uri) .

### <a name="report-connection-problems"></a>Relatar problemas de conexão

Os clientes devem relatar imediatamente todos os problemas encontrados ao fazer uma conexão. O protocolo de mensagem para relatar conexões com falha é descrito em [telemetria de falha de conexão](#connection-failure-telemetry).

### <a name="connection-duration-limitations"></a>Limitações de duração da conexão

Quando comparado com conexões HTTP de serviço Web típicas, as conexões WebSocket duram *muito* tempo. O serviço de fala impõe limitações na duração das conexões WebSocket com o serviço:

 * A duração máxima de qualquer conexão de WebSocket ativa é de 10 minutos. Uma conexão estará ativa se o serviço ou o cliente enviar mensagens WebSocket por essa conexão. O serviço encerra a conexão sem aviso quando o limite é atingido. Os clientes devem desenvolver cenários de usuário que não exigem que a conexão permaneça ativa em ou próximo do tempo de vida máximo da conexão.

 * A duração máxima de qualquer conexão de WebSocket inativa é de 180 segundos. Uma conexão ficará inativa se nem o serviço nem o cliente enviou uma mensagem WebSocket pela conexão. Após o tempo de vida máximo inativo ser atingido, o serviço encerra a conexão de WebSocket inativa.

## <a name="message-types"></a>Tipos de mensagem

Depois que uma conexão WebSocket é estabelecida entre o cliente e o serviço, o cliente e o serviço podem enviar mensagens. Esta seção descreve o formato dessas mensagens WebSocket.

O [IETF RFC 6455](https://tools.ietf.org/html/rfc6455) especifica que as mensagens WebSocket podem transmitir dados usando um texto ou uma codificação binária. As duas codificações usam diferentes formatos durante a transmissão. Cada formato é otimizado para codificação, transmissão e decodificação eficientes da carga da mensagem.

### <a name="text-websocket-messages"></a>Mensagens de WebSocket de texto

Mensagens de WebSocket de texto carregam uma carga de informações textuais que consiste em uma seção de cabeçalhos e um corpo separado pelo conhecido par de novas linhas de retorno de carro usado para mensagens HTTP. E, como mensagens HTTP, mensagens de WebSocket de texto especificam cabeçalhos em *nome:* formato de valor separado por um par de novas linhas de retorno de carro único. Qualquer texto incluído em uma mensagem de WebSocket de texto *deve* usar a codificação [UTF-8](https://tools.ietf.org/html/rfc3629) .

Mensagens de WebSocket de texto devem especificar um caminho de mensagem no *caminho*do cabeçalho. O valor desse cabeçalho deve ser um dos tipos de mensagem do protocolo de fala definidos mais adiante neste documento.

### <a name="binary-websocket-messages"></a>Mensagens de WebSocket binárias

Mensagens de WebSocket binárias carregam uma carga binária. No protocolo de serviço de fala, o áudio é transmitido para e recebido do serviço usando mensagens de WebSocket binárias. Todas as outras mensagens são mensagens de WebSocket de texto.

Como mensagens de WebSocket de texto, mensagens de WebSocket binárias consistem em um cabeçalho e uma seção de corpo. Os primeiros 2 bytes da mensagem de WebSocket binária especificam, em ordem [big-endian](https://en.wikipedia.org/wiki/Endianness) , o tamanho inteiro de 16 bits da seção de cabeçalho. O tamanho mínimo da seção de cabeçalho é 0 bytes. O tamanho máximo é de 8.192 bytes. O texto nos cabeçalhos de mensagens do WebSocket binários *deve* usar [a codificação US-ASCII](https://tools.ietf.org/html/rfc20) .

Os cabeçalhos em uma mensagem de WebSocket binária são codificados no mesmo formato que as mensagens de WebSocket de texto. O formato *nome: valor* é separado por um par de nova linha de retorno de carro único. Mensagens de WebSocket binárias devem especificar um caminho de mensagem no *caminho*do cabeçalho. O valor desse cabeçalho deve ser um dos tipos de mensagem do protocolo de fala definidos mais adiante neste documento.

As mensagens de texto e WebSocket binários são usadas no protocolo de serviço de fala.

## <a name="client-originated-messages"></a>Mensagens originadas pelo cliente

Depois que a conexão é estabelecida, o cliente e o serviço podem começar a enviar mensagens. Esta seção descreve o formato e a carga de mensagens que os aplicativos cliente enviam ao serviço de fala. A seção [mensagens originadas pelo serviço](#service-originated-messages) apresenta as mensagens originadas no serviço de fala e são enviadas para os aplicativos cliente.

As mensagens principais enviadas pelo cliente para os serviços são `speech.config`, `audio`e `telemetry` mensagens. Antes de considerarmos cada mensagem em detalhes, os cabeçalhos de mensagens necessários comuns para todas essas mensagens são descritos.

### <a name="required-message-headers"></a>Cabeçalhos de mensagem necessários

Os cabeçalhos a seguir são necessários para todas as mensagens originadas pelo cliente.

| Cabeçalho | Value |
|----|----|
| Path | O caminho da mensagem conforme especificado neste documento |
| X-RequestId | UUID no formato "no-Dash" |
| X-carimbo de data/hora | Carimbo de data/hora de relógio UTC do cliente no formato ISO 8601 |

#### <a name="x-requestid-header"></a>Cabeçalho X-RequestId

As solicitações originadas pelo cliente são identificadas exclusivamente pelo cabeçalho da mensagem *X-RequestId* . Esse cabeçalho é necessário para todas as mensagens originadas pelo cliente. O valor do cabeçalho *X-RequestId* deve ser um UUID no formulário "no-Dash", por exemplo, *123e4567e89b12d3a456426655440000*. Ele *não pode* estar no formato canônico *123e4567-e89b-12d3-A456-426655440000*. Solicitações sem um cabeçalho *X-RequestId* ou com um valor de cabeçalho que usa o formato incorreto para UUIDs fazem com que o serviço encerre a conexão WebSocket.

#### <a name="x-timestamp-header"></a>Cabeçalho X-timestamp

Cada mensagem enviada para o serviço de fala por um aplicativo cliente *deve* incluir um cabeçalho *X-timestamp* . O valor desse cabeçalho é a hora em que o cliente envia a mensagem. Solicitações sem um cabeçalho *X-timestamp* ou com um valor de cabeçalho que usa o formato incorreto fazem com que o serviço encerre a conexão WebSocket.

O valor do cabeçalho *X-timestamp* deve estar no formato ' YYYY'-'MM'-'dd'T'HH ': ' mm ': ' ss '. ' fffffffZ ', em que ' fffffff ' é uma fração de um segundo. Por exemplo, ' 12,5 ' significa ' 12 + 5/10 segundos ' e ' 12,526 ' significa ' 12 mais 526/1000 segundos '. Esse formato está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e, ao contrário do cabeçalho de *Data* http padrão, pode fornecer resolução de milissegundos. Os aplicativos cliente podem arredondar carimbos de data/hora para o milissegundo mais próximo. Os aplicativos cliente precisam garantir que o relógio do dispositivo acompanhe com precisão o tempo usando um [servidor protocolo NTP (NTP)](https://en.wikipedia.org/wiki/Network_Time_Protocol).

### <a name="message-speechconfig"></a>Mensagem`speech.config`

O serviço de fala precisa saber as características do seu aplicativo para fornecer o melhor reconhecimento de fala possível. Os dados das características necessárias incluem informações sobre o dispositivo e o sistema operacional que alimentam seu aplicativo. Você fornece essas informações na `speech.config` mensagem.

Os clientes *devem* enviar `speech.config` uma mensagem imediatamente depois de estabelecerem a conexão com o serviço de fala e `audio` antes de enviar qualquer mensagem. Você precisa enviar uma `speech.config` mensagem apenas uma vez por conexão.

| Campo | Descrição |
|----|----|
| Codificação de mensagem WebSocket | Text |
| Body | A carga como uma estrutura JSON |

#### <a name="required-message-headers"></a>Cabeçalhos de mensagem necessários

| Nome do cabeçalho | Value |
|----|----|
| Path | `speech.config` |
| X-carimbo de data/hora | Carimbo de data/hora de relógio UTC do cliente no formato ISO 8601 |
| Tipo de conteúdo | application/json; charset=utf-8 |

Assim como ocorre com todas as mensagens originadas pelo cliente no protocolo de serviço `speech.config` de fala, a mensagem *deve* incluir um cabeçalho *X-timestamp* que registra a hora UTC do cliente quando a mensagem foi enviada ao serviço. A `speech.config` mensagem *não requer um* cabeçalho *X-RequestId* porque essa mensagem não está associada a uma solicitação de fala específica.

#### <a name="message-payload"></a>Carga da mensagem
A carga da `speech.config` mensagem é uma estrutura JSON que contém informações sobre o aplicativo. O exemplo a seguir mostra essas informações. As informações de contexto de cliente e dispositivo são incluídas no elemento de *contexto* da estrutura JSON.

```JSON
{
  "context": {
    "system": {
      "version": "2.0.12341",
    },
    "os": {
      "platform": "Linux",
      "name": "Debian",
      "version": "2.14324324"
    },
    "device": {
      "manufacturer": "Contoso",
      "model": "Fabrikan",
      "version": "7.341"
      }
    },
  }
}
```

##### <a name="system-element"></a>Elemento System

O elemento System. Version da `speech.config` mensagem contém a versão do software do SDK de fala usado pelo dispositivo ou aplicativo cliente. O valor está no formato *Major. secundária. Build. Branch*. Você pode omitir o componente de *ramificação* se ele não for aplicável.

##### <a name="os-element"></a>Elemento do sistema operacional

| Campo | Descrição | Utilização |
|-|-|-|
| os. Platform | A plataforma do sistema operacional que hospeda o aplicativo, por exemplo, Windows, Android, iOS ou Linux |Requerido |
| os.name | O nome do produto do sistema operacional, por exemplo, Debian ou Windows 10 | Requerido |
| sistema operacional. versão | A versão do sistema operacional no formato *principal. secundária. Build. Branch* | Requerido |

##### <a name="device-element"></a>Elemento de dispositivo

| Campo | Descrição | Utilização |
|-|-|-|
| dispositivo. fabricante | O fabricante do hardware do dispositivo | Requerido |
| dispositivo. modelo | O modelo do dispositivo | Requerido |
| dispositivo. versão | A versão do software do dispositivo fornecida pelo fabricante do dispositivo. Esse valor especifica uma versão do dispositivo que pode ser rastreada pelo fabricante. | Requerido |

### <a name="message-audio"></a>Mensagem`audio`

Os aplicativos cliente habilitados para fala enviam áudio para o serviço de fala convertendo o fluxo de áudio em uma série de partes de áudio. Cada bloco de áudio transporta um segmento do áudio falado que deve ser transcrita pelo serviço. O tamanho máximo de uma única parte de áudio é de 8.192 bytes. As mensagens de fluxo de áudio são *mensagens de WebSocket binárias*.

Os clientes usam `audio` a mensagem para enviar uma parte de áudio para o serviço. Os clientes lêem áudio do microfone em partes e enviam essas partes para o serviço de fala para transcrição. A primeira `audio` mensagem deve conter um cabeçalho bem formado que especifica corretamente que o áudio está em conformidade com um dos formatos de codificação com suporte no serviço. Mensagens `audio` adicionais contêm apenas os dados de fluxo de áudio binários lidos do microfone.

Os clientes podem, opcionalmente `audio` , enviar uma mensagem com um corpo de comprimento zero. Essa mensagem informa ao serviço que o cliente sabe que o usuário parou de falar, o expressão foi concluído e o microfone está desligado.

O serviço de fala usa `audio` a primeira mensagem que contém um identificador de solicitação exclusivo para sinalizar o início de um novo ciclo de solicitação/resposta ou *Ativar*. Depois que o serviço recebe `audio` uma mensagem com um novo identificador de solicitação, ele descarta todas as mensagens enfileiradas ou não enviadas associadas a nenhuma rodada anterior.

| Campo | Descrição |
|-------------|----------------|
| Codificação de mensagem WebSocket | Binary |
| Body | Os dados binários da parte de áudio. O tamanho máximo é de 8.192 bytes. |

#### <a name="required-message-headers"></a>Cabeçalhos de mensagem necessários

Os cabeçalhos a seguir são necessários para `audio` todas as mensagens.

| Cabeçalho         |  Value     |
| ------------- | ---------------- |
| Path | `audio` |
| X-RequestId | UUID no formato "no-Dash" |
| X-carimbo de data/hora | Carimbo de data/hora de relógio UTC do cliente no formato ISO 8601 |
| Tipo de conteúdo | O tipo de conteúdo de áudio. O tipo deve ser *Audio/x-WAV* (PCM) ou *Audio/Silk* (Silk). |

#### <a name="supported-audio-encodings"></a>Codificações de áudio com suporte

Esta seção descreve os codecs de áudio com suporte no serviço de fala.

##### <a name="pcm"></a>PCM

O serviço de fala aceita áudio PCM (modulação de código de pulso) não compactado. O áudio é enviado para o serviço no formato [WAV](https://en.wikipedia.org/wiki/WAV) , portanto, a primeira parte de áudio *deve* conter um cabeçalho riff ( [formato de arquivo de intercâmbio de recursos](https://en.wikipedia.org/wiki/Resource_Interchange_File_Format) ) válido. Se um cliente iniciar uma rodada com uma parte de áudio que não *inclua um* cabeçalho riff válido, o serviço rejeitará a solicitação e encerrará a conexão WebSocket.

O áudio PCM *deve* ser amostrado em 16 kHz com 16 bits por amostra e um canal (*riff-16kHz-16 bits-mono-PCM*). O serviço de fala não dá suporte a fluxos de áudio estéreo e rejeita fluxos de áudio que não usam a taxa de bits especificada, a taxa de amostragem ou o número de canais.

##### <a name="opus"></a>Opus

O Opus é um codec de áudio aberto, livre de royalties e altamente versátil. O serviço de fala dá suporte a Opus em uma `32000` taxa `16000`de bits constante de ou. No momento `OGG` `audio/ogg` , só há suporte para o contêiner para Opus que é especificado pelo tipo MIME.

Para usar o Opus, modifique o [exemplo de JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/master/samples/browser/Sample.html#L101) e `RecognizerSetup` altere o método a ser retornado.

```javascript
return SDK.CreateRecognizerWithCustomAudioSource(
            recognizerConfig,
            authentication,
            new SDK.MicAudioSource(
                     new SDK.OpusRecorder(
                     {
                         mimeType: "audio/ogg",
                         bitsPerSecond: 32000
                     }
              )
          ));
```

#### <a name="detect-end-of-speech"></a>Detectar fim da fala

Os seres humanos não sinalizam explicitamente quando terminam de falar. Qualquer aplicativo que aceita a fala como entrada tem duas opções para lidar com o fim da fala em um fluxo de áudio: detecção de fim de fala de serviço e detecção de fim de fala do cliente. Dessas duas opções, a detecção de fim de fala de serviço geralmente fornece uma melhor experiência do usuário.

##### <a name="service-end-of-speech-detection"></a>Detecção de fim de fala de serviço

Para criar a experiência de fala prática ideal, os aplicativos permitem que o serviço detecte quando o usuário terminou de falar. Os clientes enviam áudio do microfone como partes de *áudio* até que o serviço detecte silêncio e `speech.endDetected` responda de volta com uma mensagem.

##### <a name="client-end-of-speech-detection"></a>Detecção de fim de fala do cliente

Os aplicativos cliente que permitem ao usuário sinalizar o fim da fala de alguma forma também podem fornecer o serviço que está sinalizando. Por exemplo, um aplicativo cliente pode ter um botão "parar" ou "mudo" que o usuário pode pressionar. Para sinalizar o fim da fala, os aplicativos cliente enviam uma mensagem de bloco de *áudio* com um corpo de comprimento zero. O serviço de fala interpreta essa mensagem como o fim do fluxo de áudio de entrada.

### <a name="message-telemetry"></a>Mensagem`telemetry`

Os aplicativos cliente *devem* reconhecer o final de cada vez enviando telemetria sobre a opção para o serviço de fala. A confirmação de encerramento permite que o serviço de fala garanta que todas as mensagens necessárias para a conclusão da solicitação e sua resposta foram recebidas corretamente pelo cliente. A confirmação de encerramento também permite que o serviço de fala Verifique se os aplicativos cliente estão sendo executados conforme o esperado. Essas informações são inestimávels se você precisar de ajuda para solucionar problemas de seu aplicativo habilitado para fala.

Os clientes devem reconhecer o fim de uma rodada enviando uma `telemetry` mensagem logo depois de receber `turn.end` uma mensagem. Os clientes devem tentar reconhecer o `turn.end` assim que possível. Se um aplicativo cliente falhar ao reconhecer o encerramento, o serviço de fala poderá encerrar a conexão com um erro. Os clientes devem enviar apenas `telemetry` uma mensagem para cada solicitação e resposta identificadas pelo valor *X-RequestId* .

| Campo | Descrição |
| ------------- | ---------------- |
| Codificação de mensagem WebSocket | Text |
| Path | `telemetry` |
| X-carimbo de data/hora | Carimbo de data/hora de relógio UTC do cliente no formato ISO 8601 |
| Tipo de conteúdo | `application/json` |
| Body | Uma estrutura JSON que contém informações de cliente sobre a opção |

O esquema para o corpo da `telemetry` mensagem é definido na seção esquema de [telemetria](#telemetry-schema) .

#### <a name="telemetry-for-interrupted-connections"></a>Telemetria para conexões interrompidas

Se a conexão de rede falhar por algum motivo durante uma vez e o cliente *não receber* uma `turn.end` mensagem do serviço, o cliente enviará uma `telemetry` mensagem. Essa mensagem descreve a solicitação com falha na próxima vez que o cliente fizer uma conexão com o serviço. Os clientes não precisam tentar uma conexão imediatamente para enviar a `telemetry` mensagem. A mensagem pode ser armazenada em buffer no cliente e enviada por uma conexão futura solicitada pelo usuário. A `telemetry` mensagem para a solicitação com falha *deve* usar o valor *X-RequestId* da solicitação com falha. Ele pode ser enviado para o serviço assim que uma conexão é estabelecida, sem aguardar para enviar ou receber outras mensagens.

## <a name="service-originated-messages"></a>Mensagens originadas no serviço

Esta seção descreve as mensagens originadas no serviço de fala e são enviadas ao cliente. O serviço de fala mantém um registro dos recursos do cliente e gera as mensagens exigidas por cada cliente, portanto, nem todos os clientes recebem todas as mensagens descritas aqui. Para fins de brevidade, as mensagens são referenciadas pelo valor do cabeçalho do *caminho* . Por exemplo, nos referimos a uma mensagem de texto WebSocket com `speech.hypothesis` o valor *path* como uma mensagem Speech. hipótese.

### <a name="message-speechstartdetected"></a>Mensagem`speech.startDetected`

A `speech.startDetected` mensagem indica que o serviço de fala detectou a fala no fluxo de áudio.

| Campo | Descrição |
| ------------- | ---------------- |
| Codificação de mensagem WebSocket | Text |
| Path | `speech.startDetected` |
| Tipo de conteúdo | application/json; charset=utf-8 |
| Body | A estrutura JSON que contém informações sobre as condições quando o início da fala foi detectado. O campo de *deslocamento* nessa estrutura especifica o deslocamento (em unidades 100-nanossegundos) quando a fala foi detectada no fluxo de áudio, em relação ao início do fluxo. |

#### <a name="sample-message"></a>Mensagem de exemplo

```HTML
Path: speech.startDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 100000
}
```

### <a name="message-speechhypothesis"></a>Mensagem`speech.hypothesis`

Durante o reconhecimento de fala, o serviço de fala gera periodicamente as mesmas informações sobre as palavras que o serviço reconheceu. O serviço de fala envia essas percursos para o cliente aproximadamente a cada 300 milissegundos. O `speech.hypothesis` é adequado *apenas* para aprimorar a experiência de fala do usuário. Você não deve assumir nenhuma dependência do conteúdo ou exatidão do texto nessas mensagens.

 A `speech.hypothesis` mensagem é aplicável a esses clientes que têm algum recurso de renderização de texto e desejam fornecer comentários quase em tempo real sobre o reconhecimento em andamento para a pessoa que está falando.

| Campo | Descrição |
| ------------- | ---------------- |
| Codificação de mensagem WebSocket | Text |
| Path | `speech.hypothesis` |
| X-RequestId | UUID no formato "no-Dash" |
| Tipo de conteúdo | application/json |
| Body | A estrutura JSON de hipótese de fala |

#### <a name="sample-message"></a>Mensagem de exemplo

```HTML
Path: speech.hypothesis
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Text": "this is a speech hypothesis",
  "Offset": 0,
  "Duration": 23600000
}
```

O elemento *offset* especifica o deslocamento (em unidades 100-nanossegundos) quando a frase foi reconhecida, em relação ao início do fluxo de áudio.

O elemento *Duration* especifica a duração (em unidades de 100 a nanossegundos) dessa frase de fala.

Os clientes não devem fazer suposições sobre a frequência, o tempo ou o texto contido em uma hipótese de fala ou a consistência do texto em duas hipóteses de fala. As mesmas são apenas instantâneos no processo de transcrição no serviço. Eles não representam uma acumulação estável de transcrição. Por exemplo, uma primeira hipótese de fala pode conter as palavras "diversão divertida", e a segunda hipótese pode conter as palavras "localizar engraçado". O serviço de fala não executa nenhum pós-processamento (por exemplo, maiúsculas e minúsculas, pontuação) no texto na hipótese de fala.

### <a name="message-speechphrase"></a>Mensagem`speech.phrase`

Quando o serviço de fala determina que ele tem informações suficientes para produzir um resultado de reconhecimento que não será alterado, `speech.phrase` o serviço produz uma mensagem. O serviço de fala produz esses resultados depois de detectar que o usuário concluiu uma frase ou frase.

| Campo | Descrição |
| ------------- | ---------------- |
| Codificação de mensagem WebSocket | Text |
| Path | `speech.phrase` |
| Tipo de conteúdo | application/json |
| Body | A estrutura JSON de frase de fala |

O esquema JSON de frase de fala inclui os seguintes `RecognitionStatus`campos `DisplayText`: `Offset`,, `Duration`e. Para obter mais informações sobre esses campos, consulte [respostas de transcrição](../concepts.md#transcription-responses).

#### <a name="sample-message"></a>Mensagem de exemplo

```HTML
Path: speech.phrase
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": 0,
  "Duration": 12300000
}
```

### <a name="message-speechenddetected"></a>Mensagem`speech.endDetected`

A `speech.endDetected` mensagem Especifica que o aplicativo cliente deve parar de transmitir áudio para o serviço.

| Campo | Descrição |
| ------------- | ---------------- |
| Codificação de mensagem WebSocket | Text |
| Path | `speech.endDetected` |
| Body | A estrutura JSON que contém o deslocamento quando o fim da fala foi detectado. O deslocamento é representado no deslocamento das unidades 100-nanossegundos a partir do início do áudio usado para reconhecimento. |
| Tipo de conteúdo | application/json; charset=utf-8 |

#### <a name="sample-message"></a>Mensagem de exemplo

```HTML
Path: speech.endDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 0
}
```

O elemento *offset* especifica o deslocamento (em unidades 100-nanossegundos) quando a frase foi reconhecida, em relação ao início do fluxo de áudio.

### <a name="message-turnstart"></a>Mensagem`turn.start`

O `turn.start` sinaliza o início de uma rodada da perspectiva do serviço. A `turn.start` mensagem é sempre a primeira mensagem de resposta recebida para qualquer solicitação. Se você não receber uma `turn.start` mensagem, assuma que o estado da conexão de serviço é inválido.

| Campo | Descrição |
| ------------- | ---------------- |
| Codificação de mensagem WebSocket | Text |
| Path | `turn.start` |
| Tipo de conteúdo | application/json; charset=utf-8 |
| Body | Estrutura JSON |

#### <a name="sample-message"></a>Mensagem de exemplo

```HTML
Path: turn.start
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "context": {
    "serviceTag": "7B33613B91714B32817815DC89633AFA"
  }
}
```

O corpo da `turn.start` mensagem é uma estrutura JSON que contém o contexto para o início da rodada. O elemento *Context* contém uma propriedade *serviceTag* . Esta propriedade especifica um valor de marca que o serviço associou com a rodada. Esse valor pode ser usado pela Microsoft se você precisar de ajuda para solucionar falhas em seu aplicativo.

### <a name="message-turnend"></a>Mensagem`turn.end`

O `turn.end` sinaliza o fim de uma rodada da perspectiva do serviço. A `turn.end` mensagem é sempre a última mensagem de resposta recebida para qualquer solicitação. Os clientes podem usar o recebimento dessa mensagem como um sinal para as atividades de limpeza e fazer a transição para um estado ocioso. Se você não receber uma `turn.end` mensagem, assuma que o estado da conexão de serviço é inválido. Nesses casos, feche a conexão existente com o serviço e reconecte-se.

| Campo | Descrição |
| ------------- | ---------------- |
| Codificação de mensagem WebSocket | Text |
| Path | `turn.end` |
| Body | Nenhum |

#### <a name="sample-message"></a>Mensagem de exemplo

```HTML
Path: turn.end
X-RequestId: 123e4567e89b12d3a456426655440000
```

## <a name="telemetry-schema"></a>Esquema de telemetria

O corpo da mensagem de *telemetria* é uma estrutura JSON que contém informações de cliente sobre uma conexão ou tentativa. A estrutura é composta de carimbos de data/hora do cliente que registram quando ocorrem eventos de cliente. Cada carimbo de data/hora deve estar no formato ISO 8601, conforme descrito na seção intitulada "X-timestamp Header". As mensagens de *telemetria* que não especificam todos os campos obrigatórios na estrutura JSON ou que não usam o formato de carimbo de data/hora correto podem fazer com que o serviço encerre a conexão com o cliente. Os clientes *devem* fornecer valores válidos para todos os campos obrigatórios. Os clientes *devem* fornecer valores para campos opcionais sempre que apropriado. Os valores mostrados nos exemplos nesta seção são apenas para fins ilustrativos.

O esquema de telemetria é dividido nas seguintes partes: carimbos e métricas de tempo de mensagem recebidos. O formato e o uso de cada parte são especificados nas seções a seguir.

### <a name="received-message-time-stamps"></a>Carimbos de data/hora da mensagem recebida

Os clientes devem incluir valores de tempo de recebimento para todas as mensagens recebidas após a conexão bem-sucedida com o serviço. Esses valores devem registrar a hora em que o cliente *recebeu* cada mensagem da rede. O valor não deve gravar nenhum outro horário. Por exemplo, o cliente não deve registrar a hora quando ele foi *aprovado* na mensagem. Os carimbos de data/hora da mensagem recebida são especificados em uma matriz de pares *nome: valor* . O nome do par especifica o valor do *caminho* da mensagem. O valor do par especifica a hora do cliente quando a mensagem foi recebida. Ou, se mais de uma mensagem do nome especificado tiver sido recebida, o valor do par será uma matriz de carimbos de data/hora que indica quando essas mensagens foram recebidas.

```JSON
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.172Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ]
```

Os clientes *devem* reconhecer o recebimento de todas as mensagens enviadas pelo serviço, incluindo carimbos de data/hora para essas mensagens no corpo JSON. Se um cliente não conseguir reconhecer o recebimento de uma mensagem, o serviço poderá encerrar a conexão.

### <a name="metrics"></a>Métricas

Os clientes devem incluir informações sobre eventos que ocorreram durante o tempo de vida de uma solicitação. Há suporte para as seguintes métricas `Connection`: `Microphone`, e `ListeningTrigger`.

### <a name="metric-connection"></a>Medidas`Connection`

A `Connection` métrica especifica detalhes sobre tentativas de conexão pelo cliente. A métrica deve incluir carimbos de data/hora de quando a conexão WebSocket foi iniciada e concluída. A `Connection` métrica é necessária *apenas para a primeira vez de uma conexão*. As ativações subsequentes não são necessárias para incluir essas informações. Se um cliente fizer várias tentativas de conexão antes que uma conexão seja estabelecida, as informações sobre *todas* as tentativas de conexão deverão ser incluídas. Para obter mais informações, consulte [telemetria de falha de conexão](#connection-failure-telemetry).

| Campo | Descrição | Utilização |
| ----- | ----------- | ----- |
| Name | `Connection` | Requerido |
| ID | O valor do identificador de conexão que foi usado no cabeçalho *X-ConnectionID* para esta solicitação de conexão | Requerido |
| Start | A hora em que o cliente enviou a solicitação de conexão | Requerido |
| fim | A hora em que o cliente recebeu a notificação de que a conexão foi estabelecida com êxito ou, em casos de erro, rejeitado, recusado ou com falha | Requerido |
| Erro | Uma descrição do erro que ocorreu, se houver. Se a conexão tiver sido bem-sucedida, os clientes deverão omitir esse campo. O comprimento máximo desse campo é de 50 caracteres. | Necessário para casos de erro, omitido caso contrário |

A descrição do erro deve ter no máximo 50 caracteres e, idealmente, deve ser um dos valores listados na tabela a seguir. Se a condição de erro não corresponder a um desses valores, os clientes poderão usar uma descrição sucinta da condição de erro usando [CamelCasing](https://en.wikipedia.org/wiki/Camel_case) sem espaço em branco. A capacidade de enviar uma mensagem de *telemetria* requer uma conexão com o serviço, portanto, somente condições de erro transitórias ou temporárias podem ser relatadas na mensagem de *telemetria* . Condições de erro que bloqueiam *permanentemente* um cliente de estabelecer uma conexão com o serviço impedem que o cliente envie qualquer mensagem para o serviço, incluindo mensagens de *telemetria* .

| Erro | Utilização |
| ----- | ----- |
| DNSfailure | O cliente não pôde se conectar ao serviço devido a uma falha de DNS na pilha de rede. |
| Nonetwork | O cliente tentou uma conexão, mas a pilha de rede relatou que nenhuma rede física estava disponível. |
| Noauthorização | Falha na conexão do cliente ao tentar adquirir um token de autorização para a conexão. |
| Noresources | O cliente ficou sem algum recurso local (por exemplo, memória) ao tentar fazer uma conexão. |
| Proibido | O cliente não pôde se conectar ao serviço porque o serviço retornou um código `403 Forbidden` de status HTTP na solicitação de atualização do WebSocket. |
| Não autorizado | O cliente não pôde se conectar ao serviço porque o serviço retornou um código `401 Unauthorized` de status HTTP na solicitação de atualização do WebSocket. |
| BadRequest | O cliente não pôde se conectar ao serviço porque o serviço retornou um código `400 Bad Request` de status HTTP na solicitação de atualização do WebSocket. |
| ServerUnavailable | O cliente não pôde se conectar ao serviço porque o serviço retornou um código `503 Server Unavailable` de status HTTP na solicitação de atualização do WebSocket. |
| ServerError | O cliente não pôde se conectar ao serviço porque o serviço retornou um `HTTP 500` código de status de erro interno na solicitação de atualização do WebSocket. |
| Tempo limite | A solicitação de conexão do cliente atingiu o tempo limite sem uma resposta do serviço. O campo *final* contém a hora em que o cliente esgotou o tempo limite e parou de esperar pela conexão. |
| ClientError | O cliente encerrou a conexão devido a algum erro interno do cliente. |

### <a name="metric-microphone"></a>Medidas`Microphone`

A `Microphone` métrica é necessária para todos os folheios de fala. Essa métrica mede o tempo no cliente durante o qual a entrada de áudio está sendo usada ativamente para uma solicitação de fala.

Use os exemplos a seguir como diretrizes para registrar valores de hora de `Microphone` início para a métrica em seu aplicativo cliente:

* Um aplicativo cliente requer que um usuário precise pressionar um botão físico para iniciar o microfone. Depois que o botão é pressionado, o aplicativo cliente lê a entrada do microfone e a envia para o serviço de fala. O valor *inicial* da `Microphone` métrica registra o tempo após o envio do botão quando o microfone é inicializado e está pronto para fornecer entrada. O valor *final* da `Microphone` métrica registra a hora em que o aplicativo cliente parou de transmitir áudio para o serviço depois de receber `speech.endDetected` a mensagem do serviço.

* Um aplicativo cliente usa uma palavra-chave spotter que é "sempre" escutando. Somente depois que a palavra-chave spotter detecta uma frase de gatilho falada, o aplicativo cliente coleta a entrada do microfone e a envia para o serviço de fala. O valor *inicial* da `Microphone` métrica registra a hora em que a palavra-chave spotter notificou o cliente a começar a usar a entrada do microfone. O valor *final* da `Microphone` métrica registra a hora em que o aplicativo cliente parou de transmitir áudio para o serviço depois de receber `speech.endDetected` a mensagem do serviço.

* Um aplicativo cliente tem acesso a um fluxo de áudio constante e executa detecção de silêncio/fala no fluxo de áudio em um *módulo de detecção de fala*. O valor *inicial* da `Microphone` métrica registra a hora em que o *módulo detecção de fala* notificou o cliente a começar a usar a entrada do fluxo de áudio. O valor *final* da `Microphone` métrica registra a hora em que o aplicativo cliente parou de transmitir áudio para o serviço depois de receber `speech.endDetected` a mensagem do serviço.

* Um aplicativo cliente está processando a segunda vez de uma solicitação de troca múltipla e é informado por uma mensagem de resposta de serviço para ativar o microfone a fim de coletar entrada para a segunda rodada. O valor *inicial* da `Microphone` métrica registra a hora em que o aplicativo cliente habilita o microfone e começa a usar a entrada dessa fonte de áudio. O valor *final* da `Microphone` métrica registra a hora em que o aplicativo cliente parou de transmitir áudio para o serviço depois de receber `speech.endDetected` a mensagem do serviço.

O valor de hora de término `Microphone` da métrica registra a hora em que o aplicativo cliente parou de transmitir a entrada de áudio. Na maioria das situações, esse evento ocorre logo após o cliente ter `speech.endDetected` recebido a mensagem do serviço. Os aplicativos cliente podem verificar se estão adequadamente em conformidade com o protocolo, garantindo que o valor `Microphone` de hora de término da métrica ocorra depois do valor do `speech.endDetected` tempo de recebimento da mensagem. E, como geralmente há um atraso entre o final de uma vez e o início de outra rodada, os clientes podem verificar a conformidade do protocolo garantindo que a hora de `Microphone` início da métrica para qualquer um dos subseqüentes registra corretamente o tempo quando o o cliente *começou* a usar o microfone para transmitir a entrada de áudio para o serviço.

| Campo | Descrição | Utilização |
| ----- | ----------- | ----- |
| Name | Phone | Requerido |
| Start | A hora em que o cliente começou a usar a entrada de áudio do microfone ou outro fluxo de áudio ou recebeu um gatilho da palavra-chave spotter | Requerido |
| fim | A hora em que o cliente parou de usar o microfone ou o fluxo de áudio | Requerido |
| Erro | Uma descrição do erro que ocorreu, se houver. Se as operações do microfone tiverem sido bem-sucedidas, os clientes deverão omitir esse campo. O comprimento máximo desse campo é de 50 caracteres. | Necessário para casos de erro, omitido caso contrário |

### <a name="metric-listeningtrigger"></a>Medidas`ListeningTrigger`
A `ListeningTrigger` métrica mede a hora em que o usuário executa a ação que inicia a entrada de fala. A `ListeningTrigger` métrica é opcional, mas os clientes que podem fornecer essa métrica são incentivados a fazer isso.

Use os exemplos a seguir como diretrizes para registrar valores de hora de *início* e `ListeningTrigger` de *término* para a métrica em seu aplicativo cliente.

* Um aplicativo cliente requer que um usuário precise pressionar um botão físico para iniciar o microfone. O valor *inicial* dessa métrica registra a hora do envio por push do botão. O valor *final* registra a hora de término do envio do botão.

* Um aplicativo cliente usa uma palavra-chave spotter que é "sempre" escutando. Depois que a palavra-chave spotter detecta uma frase de gatilho falada, o aplicativo cliente lê a entrada do microfone e a envia para o serviço de fala. O valor *inicial* dessa métrica registra a hora em que a palavra-chave spotter recebeu o áudio que foi detectado como a frase do gatilho. O valor *final* registra a hora em que a última palavra da frase do gatilho foi falada pelo usuário.

* Um aplicativo cliente tem acesso a um fluxo de áudio constante e executa detecção de silêncio/fala no fluxo de áudio em um *módulo de detecção de fala*. O valor *inicial* dessa métrica registra a hora em que o *módulo detecção de fala* recebeu áudio que foi detectado como fala. O valor *final* registra a hora em que o *módulo detecção de fala* detectou a fala.

* Um aplicativo cliente está processando a segunda vez de uma solicitação de troca múltipla e é informado por uma mensagem de resposta de serviço para ativar o microfone a fim de coletar entrada para a segunda rodada. O aplicativo cliente *não* deve incluir uma `ListeningTrigger` métrica para essa rodada.

| Campo | Descrição | Utilização |
| ----- | ----------- | ----- |
| Name | ListeningTrigger | Opcional |
| Start | A hora em que o gatilho de escuta do cliente foi iniciado | Requerido |
| fim | A hora em que o gatilho de escuta de cliente foi concluído | Requerido |
| Erro | Uma descrição do erro que ocorreu, se houver. Se a operação do gatilho foi bem-sucedida, os clientes devem omitir esse campo. O comprimento máximo desse campo é de 50 caracteres. | Necessário para casos de erro, omitido caso contrário |

#### <a name="sample-message"></a>Mensagem de exemplo

O exemplo a seguir mostra uma mensagem de telemetria com partes ReceivedMessages e métricas:

```HTML
Path: telemetry
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000
X-Timestamp: 2016-08-16T15:03:54.183Z

{
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.171Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ],
  "Metrics": [
    {
      "Name": "Connection",
      "Id": "A140CAF92F71469FA41C72C7B5849253",
      "Start": "2016-08-16T15:03:47.921Z",
      "End": "2016-08-16T15:03:48.000Z",
    },
    {
      "Name": "ListeningTrigger",
      "Start": "2016-08-16T15:03:48.776Z",
      "End": "2016-08-16T15:03:48.777Z",
    },
    {
      "Name": "Microphone",
      "Start": "2016-08-16T15:03:47.921Z",
      "End": "2016-08-16T15:03:51.921Z",
    },
  ],
}
```

## <a name="error-handling"></a>Processamento de erros

Esta seção descreve os tipos de mensagens de erro e condições que seu aplicativo precisa manipular.

### <a name="http-status-codes"></a>Códigos de estado HTTP

Durante a solicitação de atualização do WebSocket, o `400 Bad Request`serviço de fala pode retornar qualquer um dos códigos de status HTTP padrão, como, etc. Seu aplicativo deve lidar corretamente com essas condições de erro.

#### <a name="authorization-errors"></a>Erros de autorização

Se a autorização incorreta for fornecida durante a atualização do WebSocket, o serviço `403 Forbidden` de fala retornará um código de status http. Entre as condições que podem disparar esse código de erro estão:

* Cabeçalho de *autorização* ausente

* Token de autorização inválido

* Token de autorização expirado

A `403 Forbidden` mensagem de erro não indica um problema com o serviço de fala. Essa mensagem de erro indica um problema com o aplicativo cliente.

### <a name="protocol-violation-errors"></a>Erros de violação de protocolo

Se o serviço de fala detectar quaisquer violações de protocolo de um cliente, o serviço encerrará a conexão WebSocket depois de retornar um *código de status* e um *motivo* para o encerramento. Os aplicativos cliente podem usar essas informações para solucionar problemas e corrigir as violações.

#### <a name="incorrect-message-format"></a>Formato de mensagem incorreto

Se um cliente envia uma mensagem de texto ou binária ao serviço que não está codificado no formato correto fornecido nesta especificação, o serviço fecha a conexão com um código de status de *dados de carga inválido 1007* .

O serviço retorna esse código de status por vários motivos, conforme mostrado nos exemplos a seguir:

* "Formato de mensagem incorreto. A mensagem binária tem um prefixo de tamanho de cabeçalho inválido. " O cliente enviou uma mensagem binária que tem um prefixo de tamanho de cabeçalho inválido.

* "Formato de mensagem incorreto. A mensagem binária tem tamanho de cabeçalho inválido. " O cliente enviou uma mensagem binária que especificou um tamanho de cabeçalho inválido.

* "Formato de mensagem incorreto. Falha na decodificação de cabeçalhos de mensagens binárias em UTF-8. " O cliente enviou uma mensagem binária que contém cabeçalhos que não foram codificados corretamente em UTF-8.

* "Formato de mensagem incorreto. A mensagem de texto não contém dados. " O cliente enviou uma mensagem de texto que não contém nenhum dado de corpo.

* "Formato de mensagem incorreto. Falha na decodificação de mensagem de texto em UTF-8. " O cliente enviou uma mensagem de texto que não foi codificada corretamente em UTF-8.

* "Formato de mensagem incorreto. A mensagem de texto não contém nenhum separador de cabeçalho. " O cliente enviou uma mensagem de texto que não continha um separador de cabeçalho ou usou o separador de cabeçalho incorreto.

#### <a name="missing-or-empty-headers"></a>Cabeçalhos ausentes ou vazios

Se um cliente enviar uma mensagem que não tem os cabeçalhos necessários *X-RequestId* ou *Path*, o serviço fechará a conexão com um código de status de *erro de protocolo 1002* . A mensagem é "cabeçalho ausente/vazio. {Nome do cabeçalho}. "

#### <a name="requestid-values"></a>Valores RequestId

Se um cliente enviar uma mensagem que especifica um cabeçalho *X-RequestId* com um formato incorreto, o serviço fechará a conexão e retornará um status de *erro de protocolo 1002* . A mensagem é "solicitação inválida. O valor do cabeçalho X-RequestId não foi especificado no formato UUID no-Dash. "

#### <a name="audio-encoding-errors"></a>Erros de codificação de áudio

Se um cliente envia uma parte de áudio que inicia uma rodada e o formato ou a codificação de áudio não está de acordo com a especificação necessária, o serviço fecha a conexão e retorna um código de status de *dados de carga inválido 1007* . A mensagem indica a origem do erro de codificação de formato.

#### <a name="requestid-reuse"></a>Reutilização de RequestId

Após a conclusão de uma ativação, se um cliente enviar uma mensagem que reutiliza o identificador de solicitação dessa vez, o serviço fechará a conexão e retornará um código de status de *erro de protocolo 1002* . A mensagem é "solicitação inválida. A reutilização de identificadores de solicitação não é permitida. "

## <a name="connection-failure-telemetry"></a>Telemetria de falha de conexão

Para garantir a melhor experiência possível do usuário, os clientes devem informar o serviço de fala dos carimbos de data/hora para pontos de verificação importantes em uma conexão usando a mensagem de *telemetria* . É igualmente importante que os clientes informem o serviço de conexões que foram tentadas, mas que falharam.

Para cada tentativa de conexão que falhou, os clientes criam uma mensagem de *telemetria* com um valor de cabeçalho *X-RequestId* exclusivo. Como o cliente não pôde estabelecer uma conexão, o campo *ReceivedMessages* no corpo JSON pode ser omitido. Somente a `Connection` entrada no campo de *métricas* é incluída. Essa entrada inclui os carimbos de data e hora de início e término, bem como a condição de erro que foi encontrada.

### <a name="connection-retries-in-telemetry"></a>Tentativas de conexão na telemetria

Os clientes devem distinguir *repetições* de *várias tentativas de conexão* pelo evento que dispara a tentativa de conexão. As tentativas de conexão executadas programaticamente sem nenhuma entrada do usuário são repetições. Várias tentativas de conexão que são executadas em resposta à entrada do usuário são várias tentativas de conexão. Os clientes fornecem a cada conexão disparada pelo usuário a tentativa de uma única mensagem *X-RequestId* e *telemetria* . Os clientes reutilizam o *X-RequestId* para tentativas programáticas. Se várias repetições forem feitas para uma única tentativa de conexão, cada tentativa de repetição será incluída `Connection` como uma entrada na mensagem de *telemetria* .

Por exemplo, suponha que um usuário fala o gatilho de palavra-chave para iniciar uma conexão e a primeira tentativa de conexão falha devido a erros de DNS. No entanto, uma segunda tentativa feita por meio de programação pelo cliente é realizada com sucesso. Como o cliente repetiu a conexão sem a necessidade de entrada adicional do usuário, o cliente usa uma única mensagem de *telemetria* com várias `Connection` entradas para descrever a conexão.

Como outro exemplo, suponha que um usuário fala o gatilho de palavra-chave para iniciar uma conexão e essa tentativa de conexão falha após três tentativas. Em seguida, o cliente abre, para de tentar se conectar ao serviço e informa ao usuário que algo deu errado. O usuário então fala o gatilho de palavra-chave novamente. Desta vez, suponha que o cliente se conecte ao serviço. Após a conexão, o cliente envia imediatamente uma mensagem de *telemetria* para o serviço `Connection` que contém três entradas que descrevem as falhas de conexão. Depois de receber `turn.end` a mensagem, o cliente envia outra mensagem de *telemetria* que descreve a conexão bem-sucedida.

## <a name="error-message-reference"></a>Referência de mensagem de erro

### <a name="http-status-codes"></a>Códigos de estado HTTP

| Código de estado de HTTP | Descrição | Resolução de problemas |
| - | - | - |
| 400 solicitação inadequada | O cliente enviou uma solicitação de conexão WebSocket que estava incorreta. | Verifique se você forneceu todos os parâmetros e cabeçalhos HTTP necessários e se os valores estão corretos. |
| 401 não autorizado | O cliente não incluiu as informações de autorização necessárias. | Verifique se você está enviando o cabeçalho de *autorização* na conexão WebSocket. |
| 403 Proibido | O cliente enviou informações de autorização, mas ele era inválido. | Verifique se você não está enviando um valor expirado ou inválido no cabeçalho de *autorização* . |
| 404 Não Encontrado | O cliente tentou acessar um caminho de URL que não tem suporte. | Verifique se você está usando a URL correta para a conexão WebSocket. |
| Erro do servidor 500 | O serviço encontrou um erro interno e não pôde atender à solicitação. | Na maioria dos casos, esse erro é transitório. Repita a solicitação. |
| 503 Serviço Indisponível | O serviço não estava disponível para lidar com a solicitação. | Na maioria dos casos, esse erro é transitório. Repita a solicitação. |

### <a name="websocket-error-codes"></a>Códigos de erro de WebSocket

| Código WebSocketsStatus | Descrição | Resolução de problemas |
| - | - | - |
| Fechamento normal de 1000 | O serviço fechou a conexão WebSocket sem erro. | Se o fechamento do WebSocket for inesperado, leia novamente a documentação para garantir que você saiba como e quando o serviço pode encerrar a conexão WebSocket. |
| Erro de protocolo 1002 | Falha do cliente ao aderir aos requisitos de protocolo. | Verifique se você entendeu a documentação do protocolo e se está claro sobre os requisitos. Leia a documentação anterior sobre os motivos de erro para ver se você está violando os requisitos de protocolo. |
| 1007 dados de carga inválidos | O cliente enviou uma carga inválida em uma mensagem de protocolo. | Verifique a última mensagem que você enviou para o serviço para erros. Leia a documentação anterior sobre erros de carga. |
| Erro do servidor 1011 | O serviço encontrou um erro interno e não pôde atender à solicitação. | Na maioria dos casos, esse erro é transitório. Repita a solicitação. |

## <a name="related-topics"></a>Tópicos relacionados

Veja um [SDK do JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript) que é uma implementação do protocolo de serviço de fala baseado em WebSocket.

---
title: Protocolo de WebSocket de voz do Bing | Documentos da Microsoft
titlesuffix: Azure Cognitive Services
description: Documentação do protocolo de voz do Bing com base em WebSockets
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d6601f57d87b518b2061df64174818432b822755
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076195"
---
# <a name="bing-speech-websocket-protocol"></a>Protocolo WebSocket de voz do Bing

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Voz do Bing é uma plataforma com base na cloud que inclui os algoritmos mais avançados disponíveis para converter áudio falado em texto. O protocolo de voz do Bing define a [programa de configuração de ligação](#connection-establishment) entre aplicativos de cliente e o serviço e as mensagens de reconhecimento de voz trocadas entre contrapartes ([cliente originado pela mensagens](#client-originated-messages) e [originado de serviço de mensagens](#service-originated-messages)). Além disso, [mensagens de telemetria](#telemetry-schema) e [tratamento de erros](#error-handling) são descritos.

## <a name="connection-establishment"></a>Estabelecimento da conexão

O protocolo de serviço de voz segue a especificação de padrão de WebSocket [IETF RFC 6455](https://tools.ietf.org/html/rfc6455). Uma conexão WebSocket começa como um pedido HTTP que contém os cabeçalhos HTTP que indicam o desejo do cliente para atualizar a ligação para um WebSocket em vez de utilizar a semântica HTTP. O servidor indica a sua disposição para participar a conexão WebSocket, retornando um HTTP `101 Switching Protocols` resposta. Após a troca deste handshake, o cliente e o serviço mantenha aberto, o soquete e começam a utilizar um protocolo baseado em mensagens para enviar e receber informações.

Para iniciar o handshake do WebSocket, a aplicação cliente envia um pedido HTTPS GET para o serviço. Ele inclui padrão cabeçalhos de atualização de WebSocket, juntamente com outros cabeçalhos que são específicos para voz.

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

Todos os pedidos de voz exigem a [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) encriptação. Não é suportada a utilização de pedidos de voz não encriptada. É suportada a versão do TLS seguinte:

* TLS 1.2

### <a name="connection-identifier"></a>Identificador de ligação

Serviço de voz requer que todos os clientes incluem um ID exclusivo para identificar a ligação. Os clientes *tem* incluem a *X ConnectionId* cabeçalho durante o arranque um handshake WebSocket. O *X ConnectionId* cabeçalho tem de ser um [Identificador exclusivo universalmente](https://en.wikipedia.org/wiki/Universally_unique_identifier) valor (UUID). Solicitações de atualização de WebSocket que não incluem o *X ConnectionId*, não especifique um valor para o *X ConnectionId* cabeçalho, ou não incluem válido valor UUID são rejeitados pelo serviço com um HTTP `400 Bad Request` resposta.

### <a name="authorization"></a>Autorização

Além dos cabeçalhos de handshake do WebSocket padrão, os pedidos de voz solicitar uma *autorização* cabeçalho. Pedidos de ligação sem esse cabeçalho são rejeitados pelo serviço com um HTTP `403 Forbidden` resposta.

O *autorização* cabeçalho tem de conter um token de acesso do JSON Web Token (JWT).

Para obter informações sobre como inscrever-se e obter chaves de API que são usadas para recuperar os tokens de acesso do JWT válidos, consulte a [subscrição dos serviços cognitivos](https://azure.microsoft.com/try/cognitive-services/) página.

A chave de API é passada para o serviço de token. Por exemplo:

``` HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

As seguintes informações de cabeçalho são necessárias para acesso de token.

| Name | Formato | Descrição |
|----|----|----|
| OCP-Apim-Subscription-Key | ASCII | Chave de subscrição |

O serviço de token devolve o token de acesso do JWT como `text/plain`. Em seguida, o JWT é passado como um `Base64 access_token` para o handshake como um *autorização* cabeçalho com a cadeia de prefixo `Bearer`. Por exemplo:

`Authorization: Bearer [Base64 access_token]`

### <a name="cookies"></a>Cookies

Os clientes *tem* suportar cookies HTTP, conforme especificado na [RFC 6265](https://tools.ietf.org/html/rfc6265).

### <a name="http-redirection"></a>Redirecionamento de HTTP

Os clientes *tem* suportam os mecanismos de redirecionamento padrão especificados pela [a especificação de protocolo HTTP](https://www.w3.org/Protocols/rfc2616/rfc2616.html).

### <a name="speech-endpoints"></a>Pontos finais de voz

Os clientes *tem* utilizar um ponto final adequado do serviço de voz. O ponto final baseia-se sobre o modo de reconhecimento e idioma. A tabela mostra alguns exemplos.

| Modo | Caminho | URI de serviço |
| -----|-----|-----|
| Interativo | /speech/recognition/interactive/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| Conversação | /speech/recognition/conversation/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US |
| ditado | /speech/recognition/dictation/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR |

Para obter mais informações, consulte a [URI de serviço](../GetStarted/GetStartedREST.md#service-uri) página.

### <a name="report-connection-problems"></a>Problemas de ligação do relatório

Os clientes imediatamente devem reportar todos os problemas encontrados ao efetuar uma nova ligação. O protocolo de mensagens para ligações com falha de geração de relatórios é descrito em [telemetria de falha de ligação](#connection-failure-telemetry).

### <a name="connection-duration-limitations"></a>Limitações de duração de ligação

Em comparação com ligações de HTTP do serviço web típico, ligações de WebSocket da última uma *longo* tempo. Serviço de voz coloca limitações na duração das ligações de WebSocket para o serviço:

 * A duração máxima para qualquer conexão WebSocket Active Directory é 10 minutos. Uma ligação está ativa se o serviço ou o cliente envia as mensagens WebSocket através dessa ligação. O serviço encerra a conexão sem aviso quando é atingido o limite. Os clientes devem desenvolver cenários de usuário que não requerem a ligação ao permanecem ativos em ou próximo o tempo de vida máximo de ligação.

 * A duração máxima para qualquer ligação inativa do WebSocket é de 180 segundos. Uma ligação está inativa se nem o serviço nem o cliente enviou uma mensagem de WebSocket através da ligação. Após ter sido atingido o tempo de vida máximo inativo, o serviço termina a conexão WebSocket inativa.

## <a name="message-types"></a>Tipos de mensagem

Depois de uma conexão WebSocket é estabelecida entre o cliente e o serviço, o cliente e o serviço podem enviar mensagens. Esta secção descreve o formato dessas mensagens WebSocket.

[IETF RFC 6455](https://tools.ietf.org/html/rfc6455) Especifica que as mensagens WebSocket podem transmitir dados através de uma mensagem de texto ou uma codificação binária. As codificações de duas utilizam diferentes formatos de on-the-wire. Cada formato é otimizado para eficiente de codificação, transmissão e decodificação da carga da mensagem.

### <a name="text-websocket-messages"></a>Mensagens de WebSocket de texto

As mensagens WebSocket do texto implica um payload de informações textuais, que consiste numa seção de cabeçalhos e um corpo separados pelo par de familiar de double-símbolo de retorno de nova linha usado para mensagens HTTP. E, como mensagens HTTP, as mensagens WebSocket do texto especificar cabeçalhos na *name: valor* formato separados por um par de nova linha do retorno de carro único. Qualquer texto incluído numa mensagem SMS WebSocket *tem* utilizar [UTF-8](https://tools.ietf.org/html/rfc3629) codificação.

Mensagens de WebSocket de texto tem de especificar um caminho de mensagem no cabeçalho *caminho*. O valor deste cabeçalho tem de ser um dos tipos de mensagem de protocolo de voz definidos mais adiante neste documento.

### <a name="binary-websocket-messages"></a>Mensagens de WebSocket binárias

Mensagens de WebSocket binárias implica um payload de binário. O protocolo de serviço de voz, áudio é transmitido para e recebido do serviço através da utilização de mensagens binárias do WebSocket. Todas as outras mensagens são mensagens de WebSocket de texto.

Como as mensagens WebSocket de texto, binárias WebSocket mensagens consistem num cabeçalho e uma seção de corpo. Especificar os primeiros 2 bytes de mensagem de WebSocket binário, além [big-endian](https://en.wikipedia.org/wiki/Endianness) ordem, o tamanho de número inteiro de 16 bits da seção do cabeçalho. O tamanho de seção de cabeçalho mínima é 0 byte. O tamanho máximo é de 8.192 bytes. O texto nos cabeçalhos de mensagens binárias do WebSocket *tem* utilizar [US-ASCII](https://tools.ietf.org/html/rfc20) codificação.

Cabeçalhos numa mensagem do WebSocket binário são codificados no mesmo formato como em mensagens de WebSocket de texto. O *: valor de nome* formato é separado por um par de nova linha do retorno de carro único. Mensagens de WebSocket binárias tem de especificar um caminho de mensagem no cabeçalho *caminho*. O valor deste cabeçalho tem de ser um dos tipos de mensagem de protocolo de voz definidos mais adiante neste documento.

Texto e mensagens de WebSocket binárias são utilizadas no protocolo de serviço de voz.

## <a name="client-originated-messages"></a>Mensagens originadas de cliente

Depois da ligação é estabelecida, o cliente e o serviço podem começar a enviar mensagens. Esta secção descreve o formato e o payload de mensagens que aplicações cliente enviam para o serviço de voz. A secção [originado de serviço de mensagens](#service-originated-messages) apresenta as mensagens que têm origem no serviço de voz e são enviadas para as aplicações cliente.

As principais mensagens enviadas pelo cliente para os serviços são `speech.config`, `audio`, e `telemetry` mensagens. Antes de considerá-lo cada mensagem em detalhes, o comum necessária de cabeçalhos de mensagens para todas estas mensagens são descritos.

### <a name="required-message-headers"></a>Cabeçalhos de mensagem necessário

Os seguintes cabeçalhos são necessários para todas as mensagens originadas de cliente.

| Cabeçalho | Value |
|----|----|
| Caminho | O caminho de mensagem conforme especificado neste documento |
| X-RequestId | UUID no formato de "não-dash" |
| X-Timestamp | Carimbo de hora de relógio de cliente UTC no formato ISO 8601 |

#### <a name="x-requestid-header"></a>Cabeçalho X-RequestId

Pedidos de origem do cliente são identificados exclusivamente pelos *X-RequestId* cabeçalho da mensagem. Este cabeçalho é necessário para todas as mensagens originadas de cliente. O *X-RequestId* valor do cabeçalho tem de ser um UUID na forma de "não-dash", por exemplo, *123e4567e89b12d3a456426655440000*. Ele *não é possível* estar no formato canônico *123e4567-e89b-12d3-a456-426655440000*. Pedidos sem uma *X-RequestId* cabeçalho ou com um valor de cabeçalho que utiliza o formato incorreto para o UUIDs não fazer com que o serviço terminar a conexão WebSocket.

#### <a name="x-timestamp-header"></a>Cabeçalho X-Timestamp

Cada mensagem enviada para o serviço de voz por uma aplicação cliente *tem* incluem um *X Timestamp* cabeçalho. O valor para este cabeçalho é o tempo quando o cliente envia a mensagem. Pedidos sem uma *X Timestamp* cabeçalho ou com um valor de cabeçalho que utiliza o formato errado fazer com que o serviço terminar a conexão WebSocket.

O *X Timestamp* o valor de cabeçalho deve ter o formato 'aaaa'-'MM'-'dd' HH': 'mm':'ss '.' fffffffZ "onde 'fffffff' é uma fração de segundo. Por exemplo, "12,5" significa "12 + 5/10 segundos e"12.526"significa" 12 mais segundos 526/1000". Este formato é compatível com [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e, ao contrário do HTTP padrão *data* cabeçalho, ele pode fornecer resolução de milissegundos. Aplicativos cliente podem arredondar os carimbos de data / hora para o milissegundo mais próximo. Para se certificar de que o relógio do dispositivo com precisão controla tempo ao utilizar as aplicações cliente ter uma [servidor de protocolo NTP (Network Time)](https://en.wikipedia.org/wiki/Network_Time_Protocol).

### <a name="message-speechconfig"></a>mensagem `speech.config`

Serviço de voz tem de saber as características da sua aplicação para fornecer o melhor reconhecimento de voz possíveis. Os dados de características necessárias incluem informações sobre o dispositivo e o sistema operacional que capacita a sua aplicação. Fornecer estas informações no `speech.config` mensagem.

Os clientes *tem* enviar um `speech.config` mensagem imediatamente depois de estes estabeleçam a ligação ao serviço de voz e antes de enviar qualquer `audio` mensagens. Terá de enviar um `speech.config` mensagem apenas uma vez por conexão.

| Campo | Descrição |
|----|----|
| Codificação de mensagens do WebSocket | Texto |
| Corpo | O payload como uma estrutura JSON |

#### <a name="required-message-headers"></a>Cabeçalhos de mensagem necessário

| Nome do cabeçalho | Value |
|----|----|
| Caminho | `speech.config` |
| X-Timestamp | Carimbo de hora de relógio de cliente UTC no formato ISO 8601 |
| Content-Type | application/json; charset=utf-8 |

Tal como acontece com todas as mensagens originadas de cliente no protocolo serviço de voz, o `speech.config` mensagem *tem* incluem uma *X Timestamp* cabeçalho que regista o tempo de relógio de cliente UTC quando a mensagem foi enviada para o serviço. O `speech.config` mensagem *não* exigir uma *X-RequestId* cabeçalho porque esta mensagem não está associada a um pedido de voz específico.

#### <a name="message-payload"></a>Payload de mensagem
O payload do `speech.config` mensagem é uma estrutura JSON que contém informações sobre a aplicação. O exemplo seguinte mostra essas informações. Informações de contexto do cliente e o dispositivo estão incluídas nos *contexto* elemento da estrutura JSON.

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

##### <a name="system-element"></a>Elemento de sistema

O elemento de ter do `speech.config` mensagem contém a versão do software SDK utilizados pela aplicação cliente ou dispositivo de voz. O valor está sob a forma *major.minor.build.branch*. Pode omitir os *ramo* componente se não for aplicável.

##### <a name="os-element"></a>Elemento do SO

| Campo | Descrição | Utilização |
|-|-|-|
| os.platform | O sistema operacional plataforma que aloja a aplicação, por exemplo, Windows, Android, iOS ou Linux |Necessário |
| os.name | O nome de produto do sistema operacional, por exemplo, o Debian ou o Windows 10 | Necessário |
| os.version | A versão do sistema operacional na forma *major.minor.build.branch* | Necessário |

##### <a name="device-element"></a>Elemento de dispositivo

| Campo | Descrição | Utilização |
|-|-|-|
| device.manufacturer | O fabricante de hardware do dispositivo | Necessário |
| device.model | O modelo do dispositivo | Necessário |
| device.version | A versão de software do dispositivo fornecida pelo fabricante do dispositivo. Este valor Especifica uma versão do dispositivo que pode ser controlado pelo fabricante. | Necessário |

### <a name="message-audio"></a>mensagem `audio`

Aplicações de cliente com capacidade de voz enviam áudio para o serviço de voz ao converter o fluxo de áudio numa série de segmentos de áudio. Cada parte de áudio acarreta um segmento do áudio falado que está a ser transcrito pelo serviço. O tamanho máximo de um único segmento de áudio é 8.192 bytes. As mensagens de transmissão de áudio são *as mensagens WebSocket binário*.

Os clientes utilizam o `audio` mensagem a enviar um segmento de áudio para o serviço. Os clientes áudio do microfone em segmentos de leitura e enviam esses segmentos para o serviço de voz para transcrição. A primeira `audio` mensagem tem de conter um cabeçalho de bem formado corretamente Especifica que o áudio está em conformidade com um dos formatos de codificação suportados pelo serviço. Adicionais `audio` mensagens contêm somente o áudio binário transmitir dados lidos do microfone.

Os clientes podem, opcionalmente, envie um `audio` mensagem com um corpo de comprimento zero. Esta mensagem indica ao serviço que o cliente sabe que o usuário parado falando, a expressão for concluído e o microfone está desativado.

Serviço de voz utiliza a primeira `audio` mensagem que contém um identificador exclusivo do pedido para sinalizar o início de um novo ciclo de solicitação/resposta ou *ativar*. Depois do serviço recebe um `audio` mensagem com um novo identificador de solicitação, ele descarta todas as mensagens em fila ou mensagens que estão associadas a qualquer ativar anterior.

| Campo | Descrição |
|-------------|----------------|
| Codificação de mensagens do WebSocket | Binário |
| Corpo | Os dados binários para o segmento de áudio. Tamanho máximo é de 8.192 bytes. |

#### <a name="required-message-headers"></a>Cabeçalhos de mensagem necessário

Os seguintes cabeçalhos são necessários para todos os `audio` mensagens.

| Cabeçalho         |  Value     |
| ------------- | ---------------- |
| Caminho | `audio` |
| X-RequestId | UUID no formato de "não-dash" |
| X-Timestamp | Carimbo de hora de relógio de cliente UTC no formato ISO 8601 |
| Content-Type | O tipo de conteúdo de áudio. O tipo tem de ser *áudio/x-wav* (PCM) ou *áudio/silk* (SILK). |

#### <a name="supported-audio-encodings"></a>Codificações suportadas de áudio

Esta secção descreve os codecs de áudio suportados pelo serviço de voz.

##### <a name="pcm"></a>PCM

Serviço de voz aceita áudio de modulação (PCM) de código do pulse descomprimidos. Áudio é enviado para o serviço no [WAV](https://en.wikipedia.org/wiki/WAV) formatar, para que o áudio primeiro colocar partes *tem* contêm válido [formato de ficheiro de intercâmbio de recurso](https://en.wikipedia.org/wiki/Resource_Interchange_File_Format) cabeçalho (RIFF). Se um cliente inicia uma vez com um segmento de áudio que faz *não* incluir um cabeçalho RIFF válido, o serviço rejeita o pedido e encerra a conexão WebSocket.

Áudio do PCM *tem* amostragem em 16 kHz com 16 bits por amostra e um canal (*riff-16khz-16 bits-mono-pcm*). Serviço de voz não suporta fluxos de áudio estéreo e rejeita os fluxos de áudio que não utilizam a taxa de bits especificado, a taxa de exemplo ou o número de canais.

##### <a name="opus"></a>Opus

Opus é um codec de áudio aberto, isenta de royalties e altamente versátil. Serviço de voz Opus oferece suporte a uma taxa de bits constante de `32000` ou `16000`. Apenas os `OGG` contentor para Opus é atualmente suportada especificada pelo `audio/ogg` tipo mime.

Para utilizar Opus, modifique o [exemplo de JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/master/samples/browser/Sample.html#L101) e altere o `RecognizerSetup` método para retornar.

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

#### <a name="detect-end-of-speech"></a>Detectar a fim de voz

Os seres humanos não explicitamente sinalizar quando eles terminaram de fala. Qualquer aplicativo que aceita a voz como entrada tem duas opções para manipular o final de voz numa transmissão de áudio: deteção de final de voz e deteção de fim da voz de cliente do serviço. Estas duas opções, deteção de fim da voz de serviço, normalmente, fornece uma melhor experiência de utilizador.

##### <a name="service-end-of-speech-detection"></a>Deteção do serviço fim da voz

Para criar a experiência de voz automatizada ideal, aplicativos, permitir que o serviço detetar quando o utilizador terminar a falar. Os clientes enviam áudio do microfone como *áudio* segmentos até que o serviço Deteta silêncio e responde com uma `speech.endDetected` mensagem.

##### <a name="client-end-of-speech-detection"></a>Deteção de fim da voz de cliente

Aplicações de cliente que permitem ao usuário final de voz de alguma forma de sinalizar também podem permitir que o serviço que sinal. Por exemplo, uma aplicação cliente pode ter uma "Stop" ou o botão "Mute (mudo)", que o usuário poderá pressionar. Sinalizar final de voz, aplicativos cliente enviam um *áudio* mensagem de segmentos com um corpo de comprimento zero. Serviço de voz interpreta esta mensagem, como o fim do fluxo de áudio de entrada.

### <a name="message-telemetry"></a>mensagem `telemetry`

Aplicações de cliente *tem* reconhece o final de cada vez através do envio de telemetria sobre o ativar para o serviço de voz. Fim de ativar confirmação permite que o serviço de voz para se certificar de que todas as mensagens necessárias para a conclusão do pedido e a respetiva resposta foram corretamente recebidas pelo cliente. Fim de ativar confirmação também permite que o serviço de voz verificar se as aplicações cliente estão a funcionar conforme esperado. Estas informações são inestimáveis se precisar de ajuda para a aplicação com capacidade de voz de resolução de problemas.

Os clientes tem de confirmar o fim de uma vez através do envio de um `telemetry` mensagem logo após a receção um `turn.end` mensagem. Os clientes tentam devem reconhecer a `turn.end` logo que possível. Se um aplicativo cliente não conseguir reconhecer o fim de ativar, o serviço de voz pode terminar a ligação com um erro. Os clientes devem enviar apenas um `telemetry` mensagem para cada solicitação e resposta identificado pela *X-RequestId* valor.

| Campo | Descrição |
| ------------- | ---------------- |
| Codificação de mensagens do WebSocket | Texto |
| Caminho | `telemetry` |
| X-Timestamp | Carimbo de hora de relógio de cliente UTC no formato ISO 8601 |
| Content-Type | `application/json` |
| Corpo | Uma estrutura JSON que contém informações de cliente sobre a mão |

O esquema para o corpo da `telemetry` mensagem é definida no [esquema de telemetria](#telemetry-schema) secção.

#### <a name="telemetry-for-interrupted-connections"></a>Telemetria para ligações interrompidas

Se a ligação de rede falha por algum motivo durante uma folheada e o cliente faz *não* receber um `turn.end` mensagem do serviço, o cliente envia um `telemetry` mensagem. Esta mensagem descreve o pedido falhado da próxima vez que o cliente faz uma ligação ao serviço. Os clientes não precisam de imediatamente estabelecer ligação ao enviar o `telemetry` mensagem. A mensagem pode ser colocado na memória intermédia no cliente e enviada por uma conexão de futura solicitada pelo utilizador. O `telemetry` mensagem para o pedido falhado *tem* utilizar os *X-RequestId* valor no pedido com falha. Poderão ser enviadas para o serviço assim que for estabelecida uma ligação, sem ter de esperar para enviar ou receber para outras mensagens.

## <a name="service-originated-messages"></a>Mensagens originadas de serviço

Esta secção descreve as mensagens que têm origem no serviço de voz e são enviadas para o cliente. Serviço de voz mantém um registo de recursos de cliente e gera as mensagens necessárias por cada cliente, portanto, não que todos os clientes recebem todas as mensagens que são descritas aqui. Para fins de brevidade, as mensagens são referenciadas pelo valor da *caminho* cabeçalho. Por exemplo, fazemos referência a uma mensagem de texto do WebSocket com o *caminho* valor `speech.hypothesis` como uma mensagem de speech.hypothesis.

### <a name="message-speechstartdetected"></a>mensagem `speech.startDetected`

O `speech.startDetected` mensagem indica que o serviço de voz detetado fala no fluxo de áudio.

| Campo | Descrição |
| ------------- | ---------------- |
| Codificação de mensagens do WebSocket | Texto |
| Caminho | `speech.startDetected` |
| Content-Type | application/json; charset=utf-8 |
| Corpo | A estrutura JSON que contém informações sobre as condições de quando foi detetado o início da voz. O *deslocamento* campo nesta estrutura Especifica o deslocamento (em unidades de 100 nanossegundos) quando a conversão de voz foi detetada no fluxo de áudio, em relação ao início da transmissão em fluxo. |

#### <a name="sample-message"></a>Mensagem de exemplo

```HTML
Path: speech.startDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 100000
}
```

### <a name="message-speechhypothesis"></a>mensagem `speech.hypothesis`

Durante o reconhecimento de fala, o serviço de voz periodicamente gera hipóteses sobre as palavras o serviço reconhecido. Serviço de voz envia essas hipóteses para o cliente aproximadamente a cada 300 milissegundos. O `speech.hypothesis` é adequado *apenas* para melhorar a experiência de voz do utilizador. Não tem de efetuar qualquer dependência sobre o conteúdo ou a precisão do texto nessas mensagens.

 O `speech.hypothesis` mensagem é aplicável para os clientes que têm alguma capacidade de processamento de texto e pretendem fornecer comentários de quase em tempo real desse reconhecimento em curso para a pessoa que está a falar.

| Campo | Descrição |
| ------------- | ---------------- |
| Codificação de mensagens do WebSocket | Texto |
| Caminho | `speech.hypothesis` |
| X-RequestId | UUID no formato de "não-dash" |
| Content-Type | application/json |
| Corpo | A estrutura JSON de hipótese de voz |

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

O *deslocamento* elemento Especifica o deslocamento (em unidades de 100 nanossegundos) quando a expressão foi reconhecida, em relação ao início do fluxo de áudio.

O *duração* elemento Especifica a duração (em unidades de 100 nanossegundos) desta frase de voz.

Os clientes não pode fazer suposições sobre a frequência, a temporização ou o texto contido numa hipótese de voz ou a consistência de texto em qualquer hipóteses de voz de dois. Os hipóteses são apenas instantâneos para o processo de transcrição no serviço. Não representam um acúmulo estável de transcrição. Por exemplo, uma primeira hipótese de voz pode conter as palavras "brincar bem", e a segunda hipótese pode conter as palavras "encontrar engraçado." Serviço de voz não executa qualquer processamento posterior (por exemplo, capitalização, pontuação) no texto a hipótese de voz.

### <a name="message-speechphrase"></a>mensagem `speech.phrase`

Quando o serviço de voz determina a que tem informações suficientes para produzir um resultado de reconhecimento que não será alterado, o serviço produz um `speech.phrase` mensagem. Serviço de voz produz esses resultados depois de detetar que o utilizador foi concluída uma frase ou expressão.

| Campo | Descrição |
| ------------- | ---------------- |
| Codificação de mensagens do WebSocket | Texto |
| Caminho | `speech.phrase` |
| Content-Type | application/json |
| Corpo | A frase de voz estrutura JSON |

O esquema JSON de frase de voz contém os seguintes campos: `RecognitionStatus`, `DisplayText`, `Offset`, e `Duration`. Para obter mais informações sobre estes campos, consulte [respostas de transcrição](../concepts.md#transcription-responses).

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

### <a name="message-speechenddetected"></a>mensagem `speech.endDetected`

O `speech.endDetected` mensagem Especifica que a aplicação cliente deverá ser interrompida áudio para o serviço de transmissão em fluxo.

| Campo | Descrição |
| ------------- | ---------------- |
| Codificação de mensagens do WebSocket | Texto |
| Caminho | `speech.endDetected` |
| Corpo | A estrutura JSON que contém o deslocamento quando o final de voz foi detetado. O deslocamento é representado no deslocamento de unidades de 100 nanossegundos desde o início de áudio que é utilizado para reconhecimento. |
| Content-Type | application/json; charset=utf-8 |

#### <a name="sample-message"></a>Mensagem de exemplo

```HTML
Path: speech.endDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 0
}
```

O *deslocamento* elemento Especifica o deslocamento (em unidades de 100 nanossegundos) quando a expressão foi reconhecida, em relação ao início do fluxo de áudio.

### <a name="message-turnstart"></a>mensagem `turn.start`

O `turn.start` sinaliza o início de uma mão da perspectiva do serviço. O `turn.start` mensagem sempre é a primeira mensagem de resposta recebidos para qualquer pedido. Se não receber uma `turn.start` da mensagem, partem do princípio de que o estado da ligação de serviço é inválido.

| Campo | Descrição |
| ------------- | ---------------- |
| Codificação de mensagens do WebSocket | Texto |
| Caminho | `turn.start` |
| Content-Type | application/json; charset=utf-8 |
| Corpo | Estrutura JSON |

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

O corpo do `turn.start` mensagem é uma estrutura JSON que contém o contexto para o início do ativar. O *contexto* elemento contém um *serviceTag* propriedade. Esta propriedade especifica um valor de etiqueta que o serviço está associada a ativar. Este valor pode ser usado pela Microsoft se necessitar de ajuda para resolver problemas de falhas na sua aplicação.

### <a name="message-turnend"></a>mensagem `turn.end`

O `turn.end` sinaliza o término de uma mão da perspectiva do serviço. O `turn.end` mensagem sempre é a última mensagem de resposta recebidos para qualquer pedido. Os clientes podem utilizar a receção desta mensagem como um sinal para atividades de limpeza e fazer a transição para um estado inativo. Se não receber uma `turn.end` da mensagem, partem do princípio de que o estado da ligação de serviço é inválido. Nesses casos, feche a ligação existente para o serviço e voltar a ligar.

| Campo | Descrição |
| ------------- | ---------------- |
| Codificação de mensagens do WebSocket | Texto |
| Caminho | `turn.end` |
| Corpo | Nenhuma |

#### <a name="sample-message"></a>Mensagem de exemplo

```HTML
Path: turn.end
X-RequestId: 123e4567e89b12d3a456426655440000
```

## <a name="telemetry-schema"></a>Esquema de telemetria

O corpo da *telemetria* mensagem é uma estrutura JSON que contém informações de cliente sobre uma folheada ou uma tentativa de ligação. A estrutura é constituída por carimbos de hora do cliente que registam quando ocorrem eventos de cliente. Cada carimbo de data / hora tem de estar no formato ISO 8601 conforme descrito na seção intitulada "Cabeçalho de X-Timestamp". *Telemetria* mensagens que não especifique todos os campos obrigatórios na estrutura JSON ou que não utilize o formato de carimbo de data / hora correta podem fazer com que o serviço para terminar a ligação ao cliente. Os clientes *tem* de indicar valores válidos para campos obrigatórios tudo. Os clientes *deve* fornecer valores para os campos opcionais sempre que adequado. Os valores mostrados nos exemplos nesta secção são apenas para ilustração.

Esquema de telemetria está dividida nas seguintes partes: recebido carimbos de data / hora de mensagem e as métricas. O formato e a utilização de cada parte é especificado nas seções a seguir.

### <a name="received-message-time-stamps"></a>Carimbos de data / hora de mensagem recebida

Os clientes têm de incluir valores de tempo de receção para todas as mensagens que ele recebe após estabelecer ligação ao serviço. Estes valores devem registrar a hora quando o cliente *recebido* cada mensagem a partir da rede. O valor não deve gravar qualquer outra altura. Por exemplo, o cliente não deve registrar a hora quando ele *agia* na mensagem. Carimbos de data / hora da mensagem recebida é especificadas numa matriz de *: valor de nome* pares. Especifica o nome do par do *caminho* valor da mensagem. O valor do par de Especifica o tempo de cliente quando a mensagem foi recebida. Em alternativa, se tiver mais do que uma mensagem, o nome especificado foi recebida, o valor do par de é uma matriz de carimbos de data / hora que indica quando essas mensagens foram recebidas.

```JSON
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.172Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ]
```

Os clientes *tem* confirma o recebimento de todas as mensagens enviadas pelo serviço, incluindo carimbos de data / hora para essas mensagens no corpo do JSON. Se um cliente falhar confirmar a receção de uma mensagem, o serviço pode terminar a ligação.

### <a name="metrics"></a>Métricas

Os clientes devem incluir informações sobre eventos que ocorreram durante a vida útil de um pedido. As métricas seguintes são suportadas: `Connection`, `Microphone`, e `ListeningTrigger`.

### <a name="metric-connection"></a>Métrica `Connection`

O `Connection` métrica Especifica os detalhes sobre as tentativas de ligação pelo cliente. A métrica tem de incluir carimbos de data / hora de quando a conexão WebSocket foi iniciada e concluída. O `Connection` métrica é necessária *apenas para o ativar primeiro de uma conexão*. Se subsequentes não é necessários para incluir esta informação. Se um cliente faz várias tentativas de ligação antes de uma ligação é estabelecida, informações sobre *todos os* as tentativas de ligação devem ser incluídas. Para obter mais informações, consulte [telemetria de falha de ligação](#connection-failure-telemetry).

| Campo | Descrição | Utilização |
| ----- | ----------- | ----- |
| Name | `Connection` | Necessário |
| Id | O valor do identificador de ligação que foi utilizado na *X ConnectionId* cabeçalho para este pedido de ligação | Necessário |
| Iniciar | A hora quando o cliente enviou um pedido de ligação | Necessário |
| Terminar | O tempo quando o cliente recebeu a notificação de que a ligação foi estabelecida com êxito ou, em casos de erro, rejeitado, recusado ou falha | Necessário |
| Erro | Uma descrição do erro que ocorreu, se aplicável. Se a ligação foi concluída com êxito, os clientes devem omitir este campo. O comprimento máximo deste campo é de 50 carateres. | Necessário para casos de erro, caso contrário, omitidos |

A descrição do erro deve ter um máximo de 50 carateres e o ideal é que deve ser um dos valores listados na tabela seguinte. Se a condição de erro não corresponder a um dos seguintes valores, os clientes podem utilizar uma descrição sucinta da condição de erro, utilizando [CamelCasing](https://en.wikipedia.org/wiki/Camel_case) sem espaço em branco. A capacidade de enviar um *telemetria* mensagem requer uma ligação ao serviço, por isso, apenas transitório ou podem ser comunicadas as condições de erro temporário na *telemetria* mensagem. Condições de erro que *permanentemente* bloquear um cliente de estabelecer uma ligação para o serviço de impedir que o cliente enviar qualquer mensagem para o serviço, incluindo *telemetria* mensagens.

| Erro | Utilização |
| ----- | ----- |
| DNSfailure | O cliente não foi possível ligar ao serviço devido a uma falha DNS na pilha de rede. |
| NoNetwork | O cliente tentada uma ligação, mas a pilha de rede reportou que não existe nenhuma rede física estava disponível. |
| NoAuthorization | A ligação de cliente falhou ao tentar adquirir um token de autorização para a ligação. |
| NoResources | O cliente ficou sem algum recurso local (por exemplo, memória) ao tentar estabelecer uma ligação. |
| Proibido | O cliente não foi possível ligar ao serviço, porque o serviço devolveu um HTTP `403 Forbidden` código de estado sobre a solicitação de atualização do WebSocket. |
| Não autorizado | O cliente não foi possível ligar ao serviço, porque o serviço devolveu um HTTP `401 Unauthorized` código de estado sobre a solicitação de atualização do WebSocket. |
| BadRequest | O cliente não foi possível ligar ao serviço, porque o serviço devolveu um HTTP `400 Bad Request` código de estado sobre a solicitação de atualização do WebSocket. |
| ServerUnavailable | O cliente não foi possível ligar ao serviço, porque o serviço devolveu um HTTP `503 Server Unavailable` código de estado sobre a solicitação de atualização do WebSocket. |
| ServerError | O cliente não foi possível ligar ao serviço, porque o serviço devolveu um `HTTP 500` código de estado de erro interno no pedido de atualização de WebSocket. |
| Tempo Limite (excedido) | Pedido de ligação do cliente excedido o tempo limite sem uma resposta do serviço. O *final* campo contém a hora quando o cliente excedeu o tempo e parado a aguardar a ligação. |
| ClientError | O cliente terminada a ligação devido a um erro interno de cliente. |

### <a name="metric-microphone"></a>Métrica `Microphone`

O `Microphone` métrica é necessária para todos os folheio de voz. Esta métrica mede o tempo no cliente durante o qual entrada de áudio está a ser utilizada ativamente para um pedido de voz.

Utilize os exemplos a seguir como diretrizes para gravação *começar* tempo valores para o `Microphone` métrica na aplicação de cliente:

* Uma aplicação de cliente requer que um utilizador deve premir um botão de físico para iniciar o microfone. Após o pressionamento do botão, o aplicativo cliente lê a entrada do microfone e envia-os para o serviço de voz. O *começar* valor para o `Microphone` métrica regista o tempo após o envio de botão, quando o microfone é inicializado e pronto para fornecer comentários. O *final* valor para o `Microphone` métrica regista o tempo quando a aplicação cliente parado a transmissão em fluxo de áudio para o serviço depois que recebeu o `speech.endDetected` mensagem do serviço.

* Uma aplicação cliente utiliza um spotter de palavra-chave que está a escutar "sempre". Apenas depois do spotter de palavra-chave Deteta uma frase de Acionador falado faz o aplicativo cliente recolher a entrada do microfone e enviá-lo para o serviço de voz. O *começar* valor para o `Microphone` métrica regista o tempo quando o spotter de palavra-chave notificado o cliente para começar a utilizar a entrada do microfone. O *final* valor para o `Microphone` métrica regista o tempo quando a aplicação cliente parado a transmissão em fluxo de áudio para o serviço depois que recebeu o `speech.endDetected` mensagem do serviço.

* Uma aplicação cliente tem acesso a um fluxo constante de áudio e executa a deteção de silêncio/voz nesse fluxo de áudio num *módulo de deteção de voz*. O *começar* valor para o `Microphone` métrica regista o tempo quando o *módulo de deteção de voz* notificado o cliente para começar a utilizar a entrada do fluxo de áudio. O *final* valor para o `Microphone` métrica regista o tempo quando a aplicação cliente parado a transmissão em fluxo de áudio para o serviço depois que recebeu o `speech.endDetected` mensagem do serviço.

* Um aplicativo cliente está a processar o segundo mão de um pedido de ativar multi e é informado por uma mensagem de resposta do serviço para ativar o microfone para coletar entradas para a segunda vez. O *começar* valor para o `Microphone` métrica regista o tempo quando a aplicação de cliente permite que o microfone e começa com a entrada dessa origem de áudio de. O *final* valor para o `Microphone` métrica regista o tempo quando a aplicação cliente parado a transmissão em fluxo de áudio para o serviço depois que recebeu o `speech.endDetected` mensagem do serviço.

O *final* tempo valor para o `Microphone` métrica regista o tempo quando a aplicação cliente parado a transmissão em fluxo de entrada de áudio. Na maioria das situações, este evento ocorre logo após o cliente recebido a `speech.endDetected` mensagem do serviço. Aplicações cliente poderão Certifique-se de que eles estão corretamente estando em conformidade com o protocolo, assegurando que o *final* tempo valor para o `Microphone` métrica ocorre mais tarde do que o valor de tempo de receção para o `speech.endDetected` mensagem. E, porque normalmente existe um atraso entre o fim de uma vez e o início da outra vez, os clientes podem verificar conformidade com o protocolo, assegurando que o *começar* tempo do `Microphone` métrica para qualquer ativar subsequente corretamente regista o tempo quando o cliente *iniciado* usando o microfone para entrada de áudio de fluxo para o serviço.

| Campo | Descrição | Utilização |
| ----- | ----------- | ----- |
| Name | Microfone | Necessário |
| Iniciar | A hora quando o cliente à utilização de entrada de áudio do microfone ou outro transmissão de áudio ou recebido um acionador de spotter a palavra-chave | Necessário |
| Terminar | A hora quando o cliente parado com o fluxo de microfone ou de áudio | Necessário |
| Erro | Uma descrição do erro que ocorreu, se aplicável. Se as operações de microfone foram bem-sucedidas, os clientes devem omitir este campo. O comprimento máximo deste campo é de 50 carateres. | Necessário para casos de erro, caso contrário, omitidos |

### <a name="metric-listeningtrigger"></a>Métrica `ListeningTrigger`
O `ListeningTrigger` métrica mede o tempo de quando o usuário executa a ação que inicia a entrada de voz. O `ListeningTrigger` métrica é opcional, mas os clientes que podem fornecer esta métrica são encorajados a fazer isso.

Utilize os exemplos a seguir como diretrizes para gravação *começar* e *final* tempo valores para o `ListeningTrigger` métrica na aplicação de cliente.

* Uma aplicação de cliente requer que um utilizador deve premir um botão de físico para iniciar o microfone. O *iniciar* valor para esta métrica regista o tempo do envio de botão. O *final* valor regista o tempo quando termina, o envio de botão.

* Uma aplicação cliente utiliza um spotter de palavra-chave que está a escutar "sempre". Depois da palavra-chave spotter Deteta uma frase de Acionador falado, a aplicação cliente lê a entrada do microfone e envia-os para o serviço de voz. O *iniciar* valor para esta métrica regista o tempo quando o spotter de palavra-chave recebido áudio que, em seguida, foi detetado como a frase de Acionador. O *final* valor regista o tempo quando a última palavra da frase acionador foi dito pelo usuário.

* Uma aplicação cliente tem acesso a um fluxo constante de áudio e executa a deteção de silêncio/voz nesse fluxo de áudio num *módulo de deteção de voz*. O *começar* valor para esta métrica regista o tempo que o *módulo de deteção de voz* recebido áudio que, em seguida, foi detectado como sendo de voz. O *final* valor regista o tempo quando a *módulo de deteção de voz* detetado voz.

* Um aplicativo cliente está a processar o segundo mão de um pedido de ativar multi e é informado por uma mensagem de resposta do serviço para ativar o microfone para coletar entradas para a segunda vez. O aplicativo cliente deve *não* incluem um `ListeningTrigger` métrica desta vez.

| Campo | Descrição | Utilização |
| ----- | ----------- | ----- |
| Name | ListeningTrigger | Opcional |
| Iniciar | A hora de início o acionador de escuta do cliente | Necessário |
| Terminar | O tempo em que o acionador de escuta do cliente foi concluído | Necessário |
| Erro | Uma descrição do erro que ocorreu, se aplicável. Se a operação de Acionador foi concluída com êxito, os clientes devem omitir este campo. O comprimento máximo deste campo é de 50 carateres. | Necessário para casos de erro, caso contrário, omitidos |

#### <a name="sample-message"></a>Mensagem de exemplo

O exemplo seguinte mostra uma mensagem de telemetria com métricas e ReceivedMessages partes:

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

Esta secção descreve os tipos de mensagens de erro e condições que seu aplicativo precisar gerenciar.

### <a name="http-status-codes"></a>Códigos de estado HTTP

Durante a solicitação de atualização de WebSocket, o serviço de voz pode devolver qualquer um dos códigos de estado HTTP padrão, tais como `400 Bad Request`, etc. A aplicação tem de processar corretamente nestas condições de erro.

#### <a name="authorization-errors"></a>Erros de autorização

Se a autorização incorreta é fornecido durante a atualização de WebSocket, o serviço de voz retorna um HTTP `403 Forbidden` código de estado. Entre as condições que podem acionar este código de erro são:

* Em falta *autorização* cabeçalho

* Token de autorização inválido

* Token de autorização expirada

O `403 Forbidden` mensagem de erro não indica um problema com o serviço de voz. Esta mensagem de erro indica um problema com a aplicação cliente.

### <a name="protocol-violation-errors"></a>Erro de violação do protocolo

Se o serviço de voz detetar quaisquer violações de protocolo de um cliente, o serviço termina a conexão WebSocket após ser devolvido um *código de estado* e *motivo* para a finalização. Aplicações de cliente podem utilizar estas informações para resolver problemas e corrija as violações.

#### <a name="incorrect-message-format"></a>Formato de mensagem incorreto

Se um cliente envia um texto ou mensagem binária para o serviço que não está codificado no formato correto nessa especificação, o serviço fecha a conexão com um *dados de Payload inválido 1007* código de estado.

O serviço retorna este código de estado para uma variedade de motivos, conforme mostrado nos exemplos a seguir:

* "Formato de mensagem incorreto. Mensagem binária tem o prefixo de tamanho de cabeçalho inválido." O cliente enviou uma mensagem binária que tem um prefixo de tamanho de cabeçalho inválido.

* "Formato de mensagem incorreto. Mensagem binária tem um tamanho de cabeçalho inválido." O cliente enviou uma mensagem binária que um tamanho de cabeçalho inválido especificado.

* "Formato de mensagem incorreto. Falha de cabeçalhos de mensagens binária decodificação em UTF-8." O cliente enviou uma mensagem binária que contém os cabeçalhos que não foram corretamente codificados em UTF-8.

* "Formato de mensagem incorreto. Mensagem de texto não contém dados." O cliente enviou uma mensagem de texto que não contém nenhum dado de corpo.

* "Formato de mensagem incorreto. Falha na mensagem de texto decodificação em UTF-8." O cliente enviou uma mensagem de texto que não foi corretamente codificada em UTF-8.

* "Formato de mensagem incorreto. Mensagem de texto não contém nenhum separador de cabeçalho." O cliente enviou uma mensagem de texto que não continha um separador de cabeçalho ou utilizado o separador de cabeçalho errado.

#### <a name="missing-or-empty-headers"></a>Cabeçalhos em falta ou vazios

Se um cliente envia uma mensagem que não tem os cabeçalhos necessários *X-RequestId* ou *caminho*, o serviço fecha a conexão com um *erro de protocolo de 1002* código de estado. A mensagem é "cabeçalho em falta/vazio. {Nome do cabeçalho}."

#### <a name="requestid-values"></a>Valores de RequestId

Se um cliente envia uma mensagem que especifica um *X-RequestId* cabeçalho com um formato incorreto, o serviço fecha a conexão e retorna um *erro de protocolo de 1002* estado. A mensagem é "pedido inválido. O valor de cabeçalho X-RequestId não foi especificado no formato do UUID de não-dash."

#### <a name="audio-encoding-errors"></a>Erros de codificação de áudio

Se um cliente envia um segmento de áudio que inicia uma folheada e o formato de áudio ou codificação não está em conformidade com a especificação necessária, o serviço fecha a conexão e retorna um *dados de Payload inválido 1007* código de estado. A mensagem indica que o formato de origem de erros de codificação.

#### <a name="requestid-reuse"></a>Reutilização de RequestId

Depois de uma vez concluída, se um cliente envia uma mensagem que reutiliza o identificador de pedido a partir dessa vez, o serviço fecha a conexão e retorna um *erro de protocolo de 1002* código de estado. A mensagem é "pedido inválido. Reutilização de identificadores de pedido não é permitida."

## <a name="connection-failure-telemetry"></a>Telemetria de falha de ligação

Para garantir a melhor experiência de utilizador possível, os clientes devem informar o serviço de voz dos carimbos de data / hora para os pontos de verificação importantes dentro de uma ligação ao utilizar o *telemetria* mensagem. É igualmente importante que os clientes informam o serviço de ligações que foram tentada, mas falha.

Para cada tentativa de ligação que falharam, os clientes criam um *telemetria* mensagem com um único *X-RequestId* valor do cabeçalho. Uma vez que o cliente não foi possível estabelecer uma ligação, o *ReceivedMessages* campo no corpo JSON pode ser omitido. Apenas os `Connection` entrada no *métricas* campo está incluído. Esta entrada inclui o início e de carimbos de data / hora de fim, bem como a condição de erro que ocorreu.

### <a name="connection-retries-in-telemetry"></a>Tentativas de ligação na telemetria

Clientes devem distinguir *repetições* partir *várias tentativas de ligação* pelo evento que aciona a tentativa de ligação. Tentativas de ligação que são executadas por meio de programação sem qualquer intervenção do utilizador são as repetições. Várias tentativas de ligação que são executadas em resposta à entrada do usuário são várias tentativas de ligação. Os clientes dar a cada tentativa de ligação de utilizador que acionou um exclusivo *X-RequestId* e *telemetria* mensagem. Os clientes reutilizar os *X-RequestId* para repetições programáticas. Se várias repetições foram feitas por uma tentativa de ligação único, cada tentativa de repetição é incluída como um `Connection` entrada no *telemetria* mensagem.

Por exemplo, suponha que um usuário participa como palestrante o acionador de palavra-chave para iniciar uma ligação e a primeira tentativa de ligação falhar devido a erros DNS. No entanto, uma segunda tentativa é feita por meio de programação pelo cliente é bem sucedida. Uma vez que o cliente repetida a ligação sem exigir a intervenção adicional do usuário, o cliente utiliza um único *telemetria* mensagem com várias `Connection` entradas para descrever a ligação.

Como outro exemplo, suponha que um usuário participa como palestrante o acionador de palavra-chave para iniciar uma ligação e esta tentativa de ligação falha após três tentativas. O cliente, em seguida, dá, paradas tentando se conectar ao serviço e informa o utilizador que algo saiu errado. O utilizador fala, em seguida, o acionador de palavra-chave novamente. Desta vez, vamos supor que o cliente se liga ao serviço. Depois de ligar, o cliente enviará imediatamente uma *telemetria* mensagem para o serviço que contém três `Connection` entradas que descrevem as falhas de ligação. Após a receção a `turn.end` mensagem, o cliente envia outra *telemetria* mensagem que descreve a ligação com êxito.

## <a name="error-message-reference"></a>Referência de mensagem de erro

### <a name="http-status-codes"></a>Códigos de estado HTTP

| Código de estado de HTTP | Descrição | Resolução de problemas |
| - | - | - |
| 400 pedido inválido | O cliente enviou um pedido de ligação de WebSocket que estava incorreto. | Verifique que especificou todos os parâmetros necessários e os cabeçalhos HTTP e que os valores estão corretos. |
| 401 não autorizado | O cliente não incluía as informações de autorização necessário. | Verifique se está a enviar o *autorização* cabeçalho na conexão WebSocket. |
| 403 Proibido | O cliente enviadas informações de autorização, mas era inválida. | Verifique que não está a enviar um valor inválido ou expirado *autorização* cabeçalho. |
| 404 Não Encontrado | O cliente tentado aceder a um caminho de URL que não é suportado. | Verifique que está a utilizar o URL correto para a conexão WebSocket. |
| Erro de servidor 500 | O serviço encontrou um erro interno e não foi possível satisfazer o pedido. | Na maioria dos casos, este erro é transitório. Repita o pedido. |
| 503 Serviço Indisponível | O serviço não estava disponível para processar o pedido. | Na maioria dos casos, este erro é transitório. Repita o pedido. |

### <a name="websocket-error-codes"></a>Códigos de erro do WebSocket

| Código de WebSocketsStatus | Descrição | Resolução de problemas |
| - | - | - |
| Fechamento normal de 1000 | O serviço fechou a ligação de WebSocket sem erros. | Se o fechamento de WebSocket foi inesperado, lidos novamente a documentação para se certificar de que compreende como e quando o serviço pode terminar a conexão WebSocket. |
| Erro de protocolo de 1002 | O cliente não conseguiu cumprir os requisitos de protocolo. | Certifique-se de que compreender a documentação do protocolo e clareza sobre os requisitos. Leia a documentação anterior sobre as razões de erro para ver se está a violar os requisitos do protocolo. |
| 1007 de Payload inválido de dados | O cliente enviou um payload inválido numa mensagem de protocolo. | Verifique a última mensagem enviada para o serviço de erros. Leia a documentação anterior sobre erros de payload. |
| Erro de servidor 1011 | O serviço encontrou um erro interno e não foi possível satisfazer o pedido. | Na maioria dos casos, este erro é transitório. Repita o pedido. |

## <a name="related-topics"></a>Tópicos relacionados

Veja uma [JavaScript SDK](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript) ou seja, uma implementação do protocolo WebSocket com base no serviço de voz.

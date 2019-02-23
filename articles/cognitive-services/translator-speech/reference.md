---
title: Referência de API de voz de Microsoft Translator
titleSuffix: Azure Cognitive Services
description: Documentação de referência para a API de voz do Translator.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: reference
ms.date: 05/18/2018
ms.author: v-jansko
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 3493f6d25461836d8f6e48ce4213b0f5b78b6372
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56675114"
---
# <a name="translator-speech-api"></a>API de Voz do Microsoft Translator

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Este serviço oferece uma API de transmissão em fluxo para transcrição de voz conversacional de um idioma em texto de outro idioma. A API também se integra capacidades de texto para discurso falar de volta o texto traduzido. A API de voz do Translator permite cenários como a tradução em tempo real de conversas, como visto no Skype Translator.

Com a API de voz do Translator, aplicativos cliente transmitir áudio de voz para o serviço e volta recebem um fluxo de resultados com base em texto, que incluem o texto reconhecido no idioma de origem e a tradução exibida no idioma de destino. Os resultados são produzidos ao aplicar o Reconhecimento de Voz Automático (ASR) com tecnologia de redes neurais avançadas para o fluxo de áudio de entrada. Saída brutos do ASR é ainda mais aprimorada por uma nova técnica denominada TrueText a fim de refletir mais de perto a intenção do utilizador. Por exemplo, TrueText remove disfluencies (hmms e coughs) e pontuação adequada de restauro e uso. A capacidade de mascarar ou excluir linguagem obscena também está incluída. Os mecanismos de reconhecimento e tradução foram preparados especificamente para processar vozes em conversa. O serviço de tradução de voz utiliza deteção de silêncio, para determinar o final de uma expressão. Após uma pausa na atividade de voz, o serviço irá transmitir o resultado final da expressão oral. O serviço também pode enviar resultados parciais, que proporcionam reconhecimentos e traduções intermediários para uma expressão em curso. Para obter resultados finais, o serviço fornece a capacidade de sintetizar voz (voz) a partir do texto falado nos idiomas de destino. O áudio de texto em voz é criado no formato especificado pelo cliente. Os formatos WAV e MP3 estão disponíveis.

API de voz do Translator utiliza o protocolo WebSocket forneça um canal de comunicação de full duplex entre o cliente e o servidor. Um aplicativo exigirá estes passos para utilizar o serviço:

## <a name="1-getting-started"></a>1. Introdução
Para aceder à API de texto do Translator terá [Inscreva-se para o Microsoft Azure](translator-speech-how-to-signup.md).

## <a name="2-authentication"></a>2. Authentication

Utilize a chave de subscrição para autenticar. A API de voz do Translator suporta dois modos de autenticação:

* **Usando um token de acesso:** Em seu aplicativo, obter um token de acesso do serviço de token. Utilize a chave de subscrição de API de voz do Translator para obter um token de acesso do serviço de autenticação de serviços cognitivos do Azure. O token de acesso é válido durante 10 minutos. Obter um novo token de acesso a cada 10 minutos e continuar a utilizar o mesmo acesso o token para pedidos repetidos esses 10 minutos.

* **Utilizar diretamente uma chave de subscrição:** Em seu aplicativo, passar a chave de subscrição como um valor na `Ocp-Apim-Subscription-Key` cabeçalho.

Trate a chave de subscrição e o token de acesso como segredos que devem ser ocultados da vista.

## <a name="3-query-languages"></a>3. Linguagens de consulta
**Consulte o recurso de idiomas para o conjunto atual de idiomas suportados.** O [recursos de idiomas](languages-reference.md) expõe o conjunto de idiomas e vozes disponíveis para o reconhecimento de fala, para a tradução de texto e voz. Cada idioma ou voz é fornecida um identificador que a API de voz do Translator utiliza para identificar o mesmo idioma ou voz.

## <a name="4-stream-audio"></a>4. Áudio do Stream
**Abrir uma ligação e iniciar a transmissão em fluxo de áudio para o serviço.** O URL do serviço é `wss://dev.microsofttranslator.com/speech/translate`. Parâmetros e formatos de áudio esperados pelo serviço são descritos abaixo, o `/speech/translate` operação. Um dos parâmetros é utilizado para transmitir o token de acesso do passo 2 acima.

## <a name="5-process-the-results"></a>5. Processar os resultados
**Processe os resultados transmitidos em fluxo a resposta do serviço.** O formato dos resultados parciais, resultados finais e texto para discurso segmentos de áudio são descritas na documentação do `/speech/translate` operação abaixo.

Exemplos de código demonstrar o uso da API de voz do Translator estão disponíveis a partir da [site do Microsoft Translator GitHub](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Notas de implementação

OBTER /speech/translate Establishes uma sessão para a tradução de voz

### <a name="connecting"></a>A ligar
Antes de ligar ao serviço, reveja a lista de parâmetros, tendo em conta mais tarde nesta secção. Um exemplo de solicitação é:

`GET wss://dev.microsofttranslator.com/speech/translate?from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa&api-version=1.0`
`Ocp-Apim-Subscription-Key: {subscription key}`
`X-ClientTraceId: {GUID}`

O pedido Especifica que inglês falado serão transmitidas em fluxo para o serviço e traduzido em italiano. Cada resultado do reconhecimento final irá gerar uma resposta de áudio de texto para voz com a voz female Elsa com o nome. Tenha em atenção que o pedido inclui as credenciais no `Ocp-Apim-Subscription-Key header`. O pedido também segue um procedimento recomendado, definindo um identificador global exclusivo no cabeçalho `X-ClientTraceId`. Um aplicativo cliente deve registar o ID de rastreio para que possa ser utilizado para resolver os problemas quando estes ocorrerem.

### <a name="sending-audio"></a>Envio de áudio
Assim que a ligação é estabelecida, o cliente começa a transmissão em fluxo de áudio para o serviço. O cliente envia áudio em blocos. Cada segmento é transmitido usando uma mensagem do Websocket do tipo binário.

Entrada de áudio está no formato de ficheiro de áudio de forma de onda (WAVE, ou mais comumente conhecido como WAV devido a sua extensão de nome de ficheiro). O aplicativo cliente deve transmitir em fluxo canal único, o áudio do PCM assinado de 16 bits como amostrado em 16 kHz. O primeiro conjunto de bytes transmitidos pelo cliente incluirá o cabeçalho WAV. Um cabeçalho de 44 bytes para um canal único com sessão iniciada stream PCM 16 bits, como amostra em 16 kHz é:

|Desvio|Value|
|:---|:---|
|0 - 3|"RIFF"|
|4 - 7|0|
|8 - 11|"FASE"|
|12 - 15|"fmt"|
|16 - 19|16|
|20 - 21|1|
|22 - 23|1|
|24 - 27|16000|
|28 - 31|32000|
|32 - 33|2|
|34 - 35|16|
|36 - 39|"data"|
|40 - 43|0|

Tenha em atenção que o tamanho total do ficheiro (bytes 4-7) e o tamanho de "dados" (bytes 40-43) estão definidos como zero. Isso está OK para o cenário de transmissão em fluxo em que o tamanho total não é necessariamente conhecido inicialmente.

Depois de enviar o cabeçalho de WAV (RIFF), o cliente envia partes de dados de áudio. O cliente normalmente irão transmitir os segmentos de tamanho fixo que representa um período de tempo fixo (por exemplo, sequência de 100 MS de áudio cada vez).

### <a name="signal-the-end-of-the-utterance"></a>Sinal final a expressão
A API de voz do Translator devolve a transcrição e a tradução do fluxo de áudio, à medida que está enviando o áudio. A transcrição final, a tradução final e o áudio traduzido serão retornados para apenas após o fim da expressão. Em alguns casos poderá querer forçar o final da expressão. Envie segundos 2,5 de silêncio, para forçar o final da expressão.

### <a name="final-result"></a>Resultado final
Um resultado de reconhecimento de voz final é gerado no final de uma expressão. Um resultado é transmitido a partir do serviço para o cliente usando uma mensagem do WebSocket do tipo texto. O conteúdo da mensagem é a serialização do JSON de um objeto com as seguintes propriedades:

* `type`: Constante de cadeia de caracteres para identificar o tipo de resultado. O valor é final para resultados finais.
* `id`: Identificador atribuído para o resultado do reconhecimento de cadeias de caracteres.
* `recognition`: Texto reconhecido no idioma de origem. O texto pode ser uma cadeia vazia no caso de um reconhecimento FALSO.
* `translation`: Texto reconhecido traduzidos no idioma de destino.
* `audioTimeOffset`: Compensação de tempo de início do reconhecimento em tiques (1 escala = 100 nanossegundos). O deslocamento é relativo ao início da transmissão em fluxo.
* `audioTimeSize`: Duração em tiques (100 nanossegundos) desse reconhecimento.
* `audioStreamPosition`: Deslocamento de byte de início do reconhecimento. O deslocamento é relativo ao início da transmissão em fluxo.
* `audioSizeBytes`: Tamanho em bytes desse reconhecimento.

Tenha em atenção que posicionamento desse reconhecimento no fluxo de áudio não está incluído nos resultados por predefinição. O `TimingInfo` funcionalidade tem de ser selecionada pelo cliente (consulte `features` parâmetro).

Um resultado final de exemplo é o seguinte:

```
{
  type: "final"
  id: "23",
  recognition: "what was said",
  translation: "translation of what was said",
  audioStreamPosition: 319680,
  audioSizeBytes: 35840,
  audioTimeOffset: 2731600000,
  audioTimeSize: 21900000
}
```

### <a name="partial-result"></a>Resultado parcial
Resultados de reconhecimento de voz parcial ou de intermediário não são transmitidos para o cliente, por predefinição. O cliente pode utilizar o parâmetro de consulta de funcionalidades para pedi-los.

Um resultado parcial é transmitido a partir do serviço para o cliente usando uma mensagem do WebSocket do tipo texto. O conteúdo da mensagem é a serialização do JSON de um objeto com as seguintes propriedades:

* `type`: Constante de cadeia de caracteres para identificar o tipo de resultado. O valor é parcial para resultados parciais.
* `id`: Identificador atribuído para o resultado do reconhecimento de cadeias de caracteres.
* `recognition`: Texto reconhecido no idioma de origem.
* `translation`: Texto reconhecido traduzidos no idioma de destino.
* `audioTimeOffset`: Compensação de tempo de início do reconhecimento em tiques (1 escala = 100 nanossegundos). O deslocamento é relativo ao início da transmissão em fluxo.
* `audioTimeSize`: Duração em tiques (100 nanossegundos) desse reconhecimento.
* `audioStreamPosition`: Deslocamento de byte de início do reconhecimento. O deslocamento é relativo ao início da transmissão em fluxo.
* `audioSizeBytes`: Tamanho em bytes desse reconhecimento.

Tenha em atenção que posicionamento desse reconhecimento no fluxo de áudio não está incluído nos resultados por predefinição. A funcionalidade de TimingInfo tem de ser selecionada pelo cliente (consulte o parâmetro de recursos).

Um resultado final de exemplo é o seguinte:

```
{
  type: "partial"
  id: "23.2",
  recognition: "what was",
  translation: "translation of what was",
  audioStreamPosition: 319680,
  audioSizeBytes: 25840,
  audioTimeOffset: 2731600000,
  audioTimeSize: 11900000
}
```

### <a name="text-to-speech"></a>Conversão de texto em voz
Quando a funcionalidade de texto para voz está ativada (consulte `features` parâmetro abaixo), um resultado final é seguido de áudio do texto traduzido falado. Dados de áudio são segmentados e enviados do serviço ao cliente como uma seqüência de mensagens Websocket do tipo binário. Um cliente pode detectar o fim do fluxo ao verificar o bit FIN de cada mensagem. A última mensagem binária terá o seu conjunto FIN de bits para um para indicar o final da transmissão em fluxo. O formato da transmissão em fluxo depende do valor da `format` parâmetro.

### <a name="closing-the-connection"></a>Fechar a ligação
Quando uma aplicação de cliente foi concluída a transmissão em fluxo de áudio e recebeu o último resultado final, ele deve fechar a ligação, iniciando o handshake de fechamento do WebSocket. Existem condições que fará com que o servidor para terminar a ligação. Os seguintes códigos de WebSocket fechado podem ser recebidos pelo cliente:

* `1003 - Invalid Message Type`: O servidor está a terminar a ligação porque ele não pode aceitar o tipo de dados que recebeu. Normalmente, isto acontece quando o áudio de entrada não começa com um cabeçalho adequado.
* `1000 - Normal closure`: A ligação foi fechada depois do pedido foi concluído. O servidor fechará a ligação: quando não existem áudio é recebido do cliente por um longo período de tempo; Quando o silêncio é transmitido por um longo período de tempo; Quando uma sessão de atinge a duração máxima permitida (cerca de 90 minutos).
* `1001 - Endpoint Unavailable`: Indica que o servidor deixará de estar disponível. Aplicação cliente pode tentar restabelecer a ligação com um limite no número de tentativas.
* `1011 - Internal Server Error`: A ligação será fechada pelo servidor devido a um erro no servidor.

### <a name="parameters"></a>Parâmetros

|Parâmetro|Value|Descrição|Tipo de parâmetro|Tipo de Dados|
|:---|:---|:---|:---|:---|
|versão de API|1.0|Versão da API do pedido pelo cliente. Valores permitidos são: `1.0`.|consulta   |cadeia|
|de|(vazio)   |Especifica o idioma de conversão de voz recebida. O valor é um dos identificadores de idiomas do `speech` âmbito na resposta da API de idiomas.|consulta|cadeia|
|para|(vazio)|Especifica o idioma para traduzir texto transcrito em. O valor é um dos identificadores de idiomas do `text` âmbito na resposta da API de idiomas.|consulta|cadeia|
|elástica|(vazio)   |Conjunto de funcionalidades selecionadas pelo cliente separados por vírgulas. As funcionalidades disponíveis incluem:<ul><li>`TextToSpeech`: Especifica que o serviço tem de devolver o áudio traduzido da sentença traduzida final.</li><li>`Partial`: Especifica que o serviço tem de devolver resultados de reconhecimento de nível intermediário, enquanto o áudio é transmissão em fluxo para o serviço.</li><li>`TimingInfo`: Especifica que o serviço tem de devolver informações de tempo associadas a cada reconhecimento.</li></ul>Por exemplo, um cliente deve especificar `features=partial,texttospeech` para receber os resultados parciais e voz, mas não existem informações de tempo. Tenha em atenção que os resultados finais são sempre transmitidos ao cliente.|consulta|cadeia|
|Voz|(vazio)|Identifica quais voz para utilizar a opção pela composição de texto para discurso do texto traduzido. O valor é um dos identificadores de voz do âmbito tts na resposta da API de idiomas. Se uma voz não for especificada que o sistema será automaticamente escolha um quando a funcionalidade de texto para voz está ativada.|consulta|cadeia|
|Formato|(vazio)|Especifica o formato do fluxo de áudio texto para discurso retornado pelo serviço. As opções disponíveis são:<ul><li>`audio/wav`: Transmissão de áudio de forma de onda. Cliente deve utilizar o cabeçalho WAV para interpretar corretamente o formato de áudio. Áudio WAV para a voz é de 16 bits, canal único PCM com uma taxa de amostragem de 24kHz ou 16kHz.</li><li>`audio/mp3`: Transmissão de áudio MP3.</li></ul>A predefinição é `audio/wav`.|consulta|cadeia|
|ProfanityAction    |(vazio)    |Especifica a forma como o serviço deve processar profanities reconhecidos na conversão de voz. Ações válidas são:<ul><li>`NoAction`: Profanities são deixados como está.</li><li>`Marked`: Profanities são substituídos por um marcador. Consulte `ProfanityMarker` parâmetro.</li><li>`Deleted`: Profanities são eliminados. Por exemplo, se a palavra `"jackass"` é tratado como uma linguagem inapropriada, a frase `"He is a jackass."` irá tornar-se `"He is a .".`</li></ul>A predefinição é marcada.|consulta|cadeia|
|ProfanityMarker|(vazio)    |Especifica como detetados profanities são processadas quando `ProfanityAction` está definido como `Marked`. As opções válidas são:<ul><li>`Asterisk`: Profanities são substituídos pela cadeia de caracteres `***`. Por exemplo, se a palavra `"jackass"` é tratado como uma linguagem inapropriada, a frase `"He is a jackass."` irá tornar-se `"He is a ***.".`</li><li>`Tag`: Linguagem inapropriada são rodeado por uma marca XML de palavras ofensivas. Por exemplo, se a palavra `"jackass"` é tratado como uma linguagem inapropriada, a frase `"He is a jackass."` irá tornar-se `"He is a <profanity>jackass</profanity>."`.</li></ul>A predefinição é `Asterisk`.|consulta|cadeia|
|Autorização|(vazio)  |Especifica o valor do token de portador do cliente. Utilize o prefixo `Bearer` seguido o valor da `access_token` valor devolvido pelo serviço de token de autenticação.|cabeçalho   |cadeia|
|OCP-Apim-Subscription-Key|(vazio)|Necessário se o `Authorization` cabeçalho não for especificado.|cabeçalho|cadeia|
|access_token|(vazio)   |Forma alternativa de passar um token de acesso OAuth válido. O token de portador, normalmente, é fornecido com o cabeçalho `Authorization`. Algumas bibliotecas de websocket não permitem que o código de cliente definir cabeçalhos. Nesse caso, o cliente pode utilizar o `access_token` parâmetro para passar um token válido de consulta. Ao utilizar um token de acesso para se autenticar, se `Authorization` cabeçalho não for definido, em seguida, `access_token` tem de ser definido. Se o cabeçalho e o parâmetro de consulta são definidos, o parâmetro de consulta é ignorado. Os clientes só devem utilizar um método para transmitir o token.|consulta|cadeia|
|subscription-key|(vazio)   |Forma alternativa para passar a chave de subscrição. Algumas bibliotecas de websocket não permitem que o código de cliente definir cabeçalhos. Nesse caso, o cliente pode utilizar o `subscription-key` consultar o parâmetro para transmitir uma chave de subscrição válido. Ao utilizar uma chave de subscrição para autenticar, se `Ocp-Apim-Subscription-Key` cabeçalho não for definido, tem de definir a chave de subscrição. Se o cabeçalho e o parâmetro de consulta são definidos, o parâmetro de consulta é ignorado. Os clientes só devem utilizar um método para passar o `subscription key`.|consulta|cadeia|
|X-ClientTraceId    |(vazio)    |Um GUID gerado pelo cliente utilizado para rastrear um pedido. Para adequada resolução de problemas relacionados, os clientes devem fornecer um novo valor com cada solicitação e registrá-lo.<br/>Em vez de usar um cabeçalho, este valor pode ser passado com o parâmetro de consulta `X-ClientTraceId`. Se o cabeçalho e o parâmetro de consulta são definidos, o parâmetro de consulta é ignorado.|cabeçalho|cadeia|
|X-CorrelationId|(vazio)    |Um identificador gerados pelo cliente utilizado para correlacionar vários canais numa conversa. Várias sessões de tradução de voz podem ser criados para permitir que as conversações entre os utilizadores. Em tal cenário, todas as sessões de tradução de voz, utilize o mesmo ID de correlação para associar os canais em conjunto. Isso facilita o diagnóstico e de rastreio. O identificador deve estar em conformidade com: `^[a-zA-Z0-9-_.]{1,64}$`<br/>Em vez de usar um cabeçalho, este valor pode ser passado com o parâmetro de consulta `X-CorrelationId`. Se o cabeçalho e o parâmetro de consulta são definidos, o parâmetro de consulta é ignorado.|cabeçalho|cadeia|
|X-ClientVersion|(vazio)    |Identifica a versão da aplicação cliente. Exemplo: "2.1.0.123".<br/>Em vez de usar um cabeçalho, este valor pode ser passado com o parâmetro de consulta `X-ClientVersion`. Se o cabeçalho e o parâmetro de consulta são definidos, o parâmetro de consulta é ignorado.|cabeçalho|cadeia|
|X-OsPlatform|(vazio)   |Identifica o nome e versão do sistema operativo, que o aplicativo cliente está em execução no. Exemplos: "Android 5.0", "iOs 8.1.3", "Windows 8.1".<br/>Em vez de usar um cabeçalho, este valor pode ser passado com o parâmetro de consulta `X-OsPlatform`. Se o cabeçalho e o parâmetro de consulta são definidos, o parâmetro de consulta é ignorado.|cabeçalho|cadeia|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|Modelo de resposta|Cabeçalhos|
|:--|:--|:--|:--|
|101    |Atualização do WebSocket.|Valor de exemplo do modelo <br/> Objeto {}|X-RequestId<br/>Um valor identificando o pedido para fins de resolução de problemas.<br/>cadeia|
|400    |Pedido incorreto. Verifique os parâmetros de entrada para garantir que são válidos. O objeto de resposta inclui uma descrição mais detalhada do erro.|||
|401    |Não autorizado. Certifique-se de que as credenciais estão definidos, que são válidas e a sua subscrição do mercado de dados do Azure está em conformidade com um saldo de disponível.|||
|500    |Ocorreu um erro. Se o erro persistir, relatá-lo com o identificador de rastreio de cliente (X-ClientTraceId) ou o identificador (X-RequestId) do pedido.|||
|503    |Servidor temporariamente indisponível. Tente novamente o pedido. Se o erro persistir, relatá-lo com o identificador de rastreio de cliente (X-ClientTraceId) ou o identificador (X-RequestId) do pedido.|||

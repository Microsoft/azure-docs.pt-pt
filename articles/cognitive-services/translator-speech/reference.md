---
title: Referência de API de Tradução de Fala
titleSuffix: Azure Cognitive Services
description: Documentação de referência para o API de Tradução de Fala.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: reference
ms.date: 05/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 9d2f78d05de6b966dd872e0b57a90d1c8e890975
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965364"
---
# <a name="translator-speech-api"></a>API de Voz do Microsoft Translator

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Esse serviço oferece uma API de streaming para transcrever a fala de conversação de um idioma para o texto de outra linguagem. A API também integra recursos de conversão de texto em fala para falar o texto traduzido de volta. O API de Tradução de Fala permite cenários como tradução em tempo real de conversas, como visto no conversor do Skype.

Com o API de Tradução de Fala, os aplicativos cliente transmitem áudio de fala para o serviço e recebem um fluxo de resultados baseados em texto, que incluem o texto reconhecido no idioma de origem e sua tradução no idioma de destino. Os resultados são produzidos ao aplicar o Reconhecimento de Voz Automático (ASR) com tecnologia de redes neurais avançadas para o fluxo de áudio de entrada. A saída de ASR bruta é melhorada por uma nova técnica chamada TrueText para refletir de forma mais próxima a intenção do usuário. Por exemplo, TrueText remove disfluencies (HMMs e coughs) e restaura a pontuação e a capitalização corretas. A capacidade de mascarar ou excluir linguagem obscena também está incluída. Os mecanismos de reconhecimento e tradução foram preparados especificamente para processar vozes em conversa. O serviço de tradução de fala usa a detecção de silêncio para determinar o fim de um expressão. Após uma pausa na atividade de voz, o serviço irá transmitir o resultado final da expressão oral. O serviço também pode enviar resultados parciais, que proporcionam reconhecimentos e traduções intermediários para uma expressão em curso. Para resultados finais, o serviço fornece a capacidade de sintetizar a fala (conversão de texto em fala) do texto falado nos idiomas de destino. O áudio de texto em voz é criado no formato especificado pelo cliente. Os formatos WAV e MP3 estão disponíveis.

API de Tradução de Fala aproveita o protocolo WebSocket para fornecer um canal de comunicação full-duplex entre o cliente e o servidor. Um aplicativo exigirá estas etapas para usar o serviço:

## <a name="1-getting-started"></a>1. Introdução
Para acessar o API de Tradução de Texto você precisará [se inscrever para Microsoft Azure](translator-speech-how-to-signup.md).

## <a name="2-authentication"></a>2. Authentication

Use a chave de assinatura para autenticar. O API de Tradução de Fala dá suporte a dois modos de autenticação:

* **Usando um token de acesso:** Em seu aplicativo, obtenha um token de acesso do serviço de token. Use sua chave de assinatura do API de Tradução de Fala para obter um token de acesso do serviço de autenticação de serviços cognitivas do Azure. O token de acesso é válido por 10 minutos. Obtenha um novo token de acesso a cada 10 minutos e continue usando o mesmo token de acesso para solicitações repetidas dentro desses 10 minutos.

* **Usando uma chave de assinatura diretamente:** Em seu aplicativo, passe sua chave de assinatura como um valor `Ocp-Apim-Subscription-Key` no cabeçalho.

Trate sua chave de assinatura e o token de acesso como segredos que devem ser ocultados da exibição.

## <a name="3-query-languages"></a>3. Linguagens de consulta
**Consulte o recurso de idiomas para o conjunto atual de idiomas com suporte.** O [recurso de idiomas](languages-reference.md) expõe o conjunto de idiomas e vozes disponíveis para o reconhecimento de fala, para tradução de texto e para conversão de texto em fala. Cada idioma ou voz recebe um identificador que o API de Tradução de Fala usa para identificar o mesmo idioma ou voz.

## <a name="4-stream-audio"></a>4. Transmitir áudio
**Abra uma conexão e comece a transmitir áudio para o serviço.** A URL do serviço `wss://dev.microsofttranslator.com/speech/translate`é. Os parâmetros e os formatos de áudio esperados pelo serviço são descritos abaixo `/speech/translate` , na operação. Um dos parâmetros é usado para passar o token de acesso da etapa 2 acima.

## <a name="5-process-the-results"></a>5. Processar os resultados
**Processe os resultados transmitidos de volta do serviço.** O formato dos resultados parciais, os resultados finais e os segmentos de áudio de texto para fala são descritos na documentação `/speech/translate` da operação abaixo.

Exemplos de código que demonstram o uso do API de Tradução de Fala estão disponíveis no [site GitHub do Microsoft Translator](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Notas de implementação

GET/Speech/translate estabelece uma sessão para tradução de fala

### <a name="connecting"></a>A ligar
Antes de se conectar ao serviço, examine a lista de parâmetros fornecida mais adiante nesta seção. Uma solicitação de exemplo é:

`GET wss://dev.microsofttranslator.com/speech/translate?from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa&api-version=1.0`
`Ocp-Apim-Subscription-Key: {subscription key}`
`X-ClientTraceId: {GUID}`

A solicitação especifica que o inglês falado será transmitido para o serviço e traduzido para o italiano. Cada resultado de reconhecimento final gerará uma resposta de áudio de conversão de texto em fala com a voz fêmea chamada Elsa. Observe que a solicitação inclui credenciais no `Ocp-Apim-Subscription-Key header`. A solicitação também segue uma prática recomendada definindo um identificador global exclusivo no cabeçalho `X-ClientTraceId`. Um aplicativo cliente deve registrar a ID de rastreamento para que possa ser usado para solucionar problemas quando eles ocorrerem.

### <a name="sending-audio"></a>Enviando áudio
Depois que a conexão é estabelecida, o cliente começa a transmitir áudio para o serviço. O cliente envia o áudio em partes. Cada parte é transmitida usando uma mensagem WebSocket do tipo binary.

A entrada de áudio está no formato WAVE ou mais conhecido como WAV devido à sua extensão de nome de arquivo). O aplicativo cliente deve transmitir um único canal, assinado 16 bits com áudio PCM amostrado a 16 kHz. O primeiro conjunto de bytes transmitidos pelo cliente incluirá o cabeçalho WAV. Um cabeçalho de 44 bytes para um fluxo PCM de 16 bits assinado por um único canal é amostrado em 16 kHz:

|Offset|Value|
|:---|:---|
|0 - 3|METÁLICA|
|4 - 7|0|
|8 - 11|ONDA|
|12 - 15|fmt|
|16 - 19|16|
|20 - 21|1|
|22 - 23|1|
|24 - 27|16000|
|28 - 31|32000|
|32-33|2|
|34-35|16|
|36-39|dado|
|40-43|0|

Observe que o tamanho total do arquivo (bytes 4-7) e o tamanho dos "dados" (bytes 40-43) são definidos como zero. Isso é válido para o cenário de streaming em que o tamanho total não é necessariamente conhecido antecipadamente.

Depois de enviar o cabeçalho WAV (RIFF), o cliente envia partes dos dados de áudio. Normalmente, o cliente transmitirá partes de tamanho fixo que representam uma duração fixa (por exemplo, fluxos de 100 ms de áudio por vez).

### <a name="signal-the-end-of-the-utterance"></a>Sinalizar o fim do expressão
O API de Tradução de Fala retorna a transcrição e a tradução do fluxo de áudio à medida que você está enviando o áudio. A transcrição final, a tradução final e o áudio traduzido serão retornados para você somente após o final do expressão. Em alguns casos, talvez você queira forçar o final do expressão. Envie 2,5 segundos de silêncio para forçar o fim do expressão.

### <a name="final-result"></a>Resultado final
Um resultado de reconhecimento de fala final é gerado no final de um expressão. Um resultado é transmitido do serviço para o cliente usando uma mensagem WebSocket do tipo texto. O conteúdo da mensagem é a serialização JSON de um objeto com as seguintes propriedades:

* `type`: Constante de cadeia de caracteres para identificar o tipo de resultado. O valor é final para os resultados finais.
* `id`: Identificador de cadeia de caracteres atribuído ao resultado do reconhecimento.
* `recognition`: Texto reconhecido no idioma de origem. O texto pode ser uma cadeia de caracteres vazia no caso de um falso reconhecimento.
* `translation`: Texto reconhecido traduzido no idioma de destino.
* `audioTimeOffset`: Diferença de tempo do início do reconhecimento em tiques (1 tique = 100 nanossegundos). O deslocamento é relativo ao início do streaming.
* `audioTimeSize`: Duração em tiques (100 nanossegundos) do reconhecimento.
* `audioStreamPosition`: Deslocamento de byte do início do reconhecimento. O deslocamento é relativo ao início do fluxo.
* `audioSizeBytes`: Tamanho em bytes do reconhecimento.

Observe que o posicionamento do reconhecimento no fluxo de áudio não é incluído nos resultados por padrão. O `TimingInfo` recurso deve ser selecionado pelo cliente (consulte `features` o parâmetro).

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
Os resultados do reconhecimento de fala parcial ou intermediário não são transmitidos para o cliente por padrão. O cliente pode usar o parâmetro de consulta Features para solicitá-los.

Um resultado parcial é transmitido do serviço para o cliente usando uma mensagem WebSocket do tipo texto. O conteúdo da mensagem é a serialização JSON de um objeto com as seguintes propriedades:

* `type`: Constante de cadeia de caracteres para identificar o tipo de resultado. O valor é parcial para resultados parciais.
* `id`: Identificador de cadeia de caracteres atribuído ao resultado do reconhecimento.
* `recognition`: Texto reconhecido no idioma de origem.
* `translation`: Texto reconhecido traduzido no idioma de destino.
* `audioTimeOffset`: Diferença de tempo do início do reconhecimento em tiques (1 tique = 100 nanossegundos). O deslocamento é relativo ao início do streaming.
* `audioTimeSize`: Duração em tiques (100 nanossegundos) do reconhecimento.
* `audioStreamPosition`: Deslocamento de byte do início do reconhecimento. O deslocamento é relativo ao início do fluxo.
* `audioSizeBytes`: Tamanho em bytes do reconhecimento.

Observe que o posicionamento do reconhecimento no fluxo de áudio não é incluído nos resultados por padrão. O recurso TimingInfo deve ser selecionado pelo cliente (consulte o parâmetro Features).

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
Quando o recurso de conversão de texto em fala está habilitado ( `features` consulte o parâmetro abaixo), um resultado final é seguido pelo áudio do texto traduzido falado. Os dados de áudio são fragmentados e enviados do serviço para o cliente como uma sequência de mensagens WebSocket do tipo binary. Um cliente pode detectar o final do fluxo verificando o bit de FIN de cada mensagem. A última mensagem binária terá seu bit de FIN definido como um para indicar o final do fluxo. O formato do fluxo depende do valor do `format` parâmetro.

### <a name="closing-the-connection"></a>Fechando a conexão
Quando um aplicativo cliente termina o streaming de áudio e recebeu o último resultado final, ele deve fechar a conexão iniciando o handshake de fechamento do WebSocket. Há condições que farão com que o servidor encerre a conexão. Os códigos fechados do WebSocket a seguir podem ser recebidos pelo cliente:

* `1003 - Invalid Message Type`: O servidor está encerrando a conexão porque não pode aceitar o tipo de dados recebido. Isso geralmente acontece quando o áudio de entrada não começa com um cabeçalho adequado.
* `1000 - Normal closure`: A conexão foi fechada depois que a solicitação foi atendida. O servidor fechará a conexão: quando nenhum áudio for recebido do cliente por um longo período de tempo; Quando o silêncio é transmitido por um longo período de tempo; Quando uma sessão atinge a duração máxima permitida (aproximadamente 90 minutos).
* `1001 - Endpoint Unavailable`: Indica que o servidor ficará indisponível. O aplicativo cliente pode tentar se reconectar com um limite no número de repetições.
* `1011 - Internal Server Error`: A conexão será fechada pelo servidor devido a um erro no servidor.

### <a name="parameters"></a>Parâmetros

|Parâmetro|Value|Descrição|Tipo de parâmetro|Tipo de Dados|
|:---|:---|:---|:---|:---|
|versão de API|1.0|Versão da API solicitada pelo cliente. Os valores permitidos são `1.0`:.|query   |Cadeia de caracteres|
|from|esvaziá   |Especifica o idioma da fala de entrada. O valor é um dos identificadores de idioma do `speech` escopo na resposta da API de idiomas.|query|Cadeia de caracteres|
|para|esvaziá|Especifica o idioma no qual converter o texto transcrita. O valor é um dos identificadores de idioma do `text` escopo na resposta da API de idiomas.|query|Cadeia de caracteres|
|elástica|esvaziá   |Conjunto de recursos separados por vírgulas selecionado pelo cliente. Os recursos disponíveis incluem:<ul><li>`TextToSpeech`: Especifica que o serviço deve retornar o áudio traduzido da frase traduzida final.</li><li>`Partial`: Especifica que o serviço deve retornar resultados de reconhecimento intermediários enquanto o áudio é transmitido para o serviço.</li><li>`TimingInfo`: Especifica que o serviço deve retornar informações de tempo associadas a cada reconhecimento.</li></ul>Por exemplo, um cliente especificaria `features=partial,texttospeech` para receber resultados parciais e conversão de texto em fala, mas sem informações de tempo. Observe que os resultados finais são sempre transmitidos para o cliente.|query|Cadeia de caracteres|
|Fatura|esvaziá|Identifica qual voz usar para a renderização de conversão de texto em fala do texto traduzido. O valor é um dos identificadores de voz do escopo de TTS na resposta da API de idiomas. Se uma voz não for especificada, o sistema escolherá automaticamente uma quando o recurso de conversão de texto em fala estiver habilitado.|query|Cadeia de caracteres|
|format|esvaziá|Especifica o formato do fluxo de áudio de texto para fala retornado pelo serviço. As opções disponíveis são:<ul><li>`audio/wav`: Fluxo de áudio de formato de onda. O cliente deve usar o cabeçalho WAV para interpretar corretamente o formato de áudio. O áudio WAV para conversão de texto em fala é de 16 bits, um único canal PCM com uma taxa de amostragem de 24kHz ou 16kHz.</li><li>`audio/mp3`: Fluxo de áudio MP3.</li></ul>A predefinição é `audio/wav`.|query|Cadeia de caracteres|
|ProfanityAction    |esvaziá    |Especifica como o serviço deve lidar com as obscenidades reconhecidas na fala. As ações válidas são:<ul><li>`NoAction`: As obscenidades são deixadas como estão.</li><li>`Marked`: As obscenidades são substituídas por um marcador. Consulte `ProfanityMarker` parâmetro.</li><li>`Deleted`: As obscenidades são excluídas. Por exemplo, se a palavra `"jackass"` for tratada como uma profanação, a frase `"He is a jackass."` se tornará`"He is a .".`</li></ul>O padrão é marcado.|query|Cadeia de caracteres|
|ProfanityMarker|esvaziá    |Especifica como as obscenidades detectadas são `ProfanityAction` tratadas quando `Marked`é definido como. As opções válidas são:<ul><li>`Asterisk`: As obscenidades são substituídas pela `***`cadeia de caracteres. Por exemplo, se a palavra `"jackass"` for tratada como uma profanação, a frase `"He is a jackass."` se tornará`"He is a ***.".`</li><li>`Tag`: A profanação está envolvida por uma marca XML de profanação. Por exemplo, se a palavra `"jackass"` for tratada como uma profanação, a frase `"He is a jackass."` se tornará `"He is a <profanity>jackass</profanity>."`.</li></ul>A predefinição é `Asterisk`.|query|Cadeia de caracteres|
|Autorização|esvaziá  |Especifica o valor do token de portador do cliente. Use o prefixo `Bearer` seguido pelo valor `access_token` do valor retornado pelo serviço de token de autenticação.|cabeçalho   |Cadeia de caracteres|
|OCP-Apim-Subscription-Key|esvaziá|Obrigatório se o `Authorization` cabeçalho não for especificado.|cabeçalho|Cadeia de caracteres|
|access_token|esvaziá   |Modo alternativo de passar um token de acesso OAuth válido. O token de portador geralmente é fornecido com `Authorization`o cabeçalho. Algumas bibliotecas WebSocket não permitem que o código do cliente defina cabeçalhos. Nesse caso, o cliente pode usar o parâmetro `access_token` de consulta para passar um token válido. Ao usar um token de acesso para autenticar `Authorization` , se o cabeçalho não estiver `access_token` definido, deverá ser definido. Se o cabeçalho e o parâmetro de consulta forem definidos, o parâmetro de consulta será ignorado. Os clientes só devem usar um método para passar o token.|query|Cadeia de caracteres|
|chave de assinatura|esvaziá   |Modo alternativo de passar a chave de assinatura. Algumas bibliotecas WebSocket não permitem que o código do cliente defina cabeçalhos. Nesse caso, o cliente pode usar o `subscription-key` parâmetro de consulta para passar uma chave de assinatura válida. Ao usar uma chave de assinatura para autenticar `Ocp-Apim-Subscription-Key` , se o cabeçalho não estiver definido, a chave de assinatura deverá ser definida. Se o cabeçalho e o parâmetro de consulta forem definidos, o parâmetro de consulta será ignorado. Os clientes devem usar apenas um método para passar `subscription key`o.|query|Cadeia de caracteres|
|X-ClientTraceId    |esvaziá    |Um GUID gerado pelo cliente usado para rastrear uma solicitação. Para uma solução de problemas adequada, os clientes devem fornecer um novo valor com cada solicitação e registrar em log.<br/>Em vez de usar um cabeçalho, esse valor pode ser passado com o `X-ClientTraceId`parâmetro de consulta. Se o cabeçalho e o parâmetro de consulta forem definidos, o parâmetro de consulta será ignorado.|cabeçalho|Cadeia de caracteres|
|X-CorrelationId|esvaziá    |Um identificador gerado pelo cliente usado para correlacionar vários canais em uma conversa. Várias sessões de tradução de fala podem ser criadas para habilitar conversas entre usuários. Nesse cenário, todas as sessões de tradução de fala usam a mesma ID de correlação para reunir os canais. Isso facilita o rastreamento e o diagnóstico. O identificador deve estar em conformidade com:`^[a-zA-Z0-9-_.]{1,64}$`<br/>Em vez de usar um cabeçalho, esse valor pode ser passado com o `X-CorrelationId`parâmetro de consulta. Se o cabeçalho e o parâmetro de consulta forem definidos, o parâmetro de consulta será ignorado.|cabeçalho|Cadeia de caracteres|
|X-ClientVersion|esvaziá    |Identifica a versão do aplicativo cliente. Exemplo: "2.1.0.123".<br/>Em vez de usar um cabeçalho, esse valor pode ser passado com o `X-ClientVersion`parâmetro de consulta. Se o cabeçalho e o parâmetro de consulta forem definidos, o parâmetro de consulta será ignorado.|cabeçalho|Cadeia de caracteres|
|X-OsPlatform|esvaziá   |Identifica o nome e a versão do sistema operacional em que o aplicativo cliente está sendo executado. Exemplos: "Android 5,0", "iOs 8.1.3", "Windows 8.1".<br/>Em vez de usar um cabeçalho, esse valor pode ser passado com o `X-OsPlatform`parâmetro de consulta. Se o cabeçalho e o parâmetro de consulta forem definidos, o parâmetro de consulta será ignorado.|cabeçalho|Cadeia de caracteres|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Reason|Modelo de resposta|Cabeçalhos|
|:--|:--|:--|:--|
|101    |Atualização do WebSocket.|Exemplo de valor de modelo <br/> Objeto{}|X-RequestId<br/>Um valor que identifica a solicitação para fins de solução de problemas.<br/>Cadeia de caracteres|
|400    |Solicitação inadequada. Verifique os parâmetros de entrada para garantir que eles sejam válidos. O objeto de resposta inclui uma descrição mais detalhada do erro.|||
|401    |Não autorizado. Verifique se as credenciais estão definidas, se elas são válidas e se sua assinatura do Azure data Market está em boas condições com um saldo disponível.|||
|500    |Ocorreu um erro. Se o erro persistir, informe-o com o identificador de rastreamento do cliente (X-ClientTraceId) ou o identificador de solicitação (X-RequestId).|||
|503    |Servidor temporariamente indisponível. Repita a solicitação. Se o erro persistir, informe-o com o identificador de rastreamento do cliente (X-ClientTraceId) ou o identificador de solicitação (X-RequestId).|||

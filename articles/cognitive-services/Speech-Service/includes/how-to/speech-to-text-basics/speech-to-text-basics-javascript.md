---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2021
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: dd92cf24cf007418e52cb5091eb390b46d7a5571
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104988228"
---
Uma das características centrais do serviço de fala é a capacidade de reconhecer e transcrever a fala humana (muitas vezes referida como discurso-a-texto). Neste arranque rápido, aprende-se a usar o Speech SDK nas suas apps e produtos para realizar conversão de fala a texto de alta qualidade.

## <a name="skip-to-samples-on-github"></a>Salte para as amostras no GitHub

Se quiser saltar diretamente para o código de amostra, consulte as [amostras de arranque rápido do JavaScript](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node) no GitHub.

Em alternativa, consulte a [amostra Reagir](https://github.com/Azure-Samples/AzureSpeechReactSample) para aprender a usar o SDK do discurso num ambiente baseado no navegador.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que tem uma conta Azure e subscrição do serviço de fala. Se não tiver uma conta e subscrição, [experimente gratuitamente o serviço Desemação](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instale o SDK de discurso

Antes de poder fazer qualquer coisa, tem de instalar o SDK do Discurso para Node.js. Se quiser apenas instalar o nome do pacote, corra `npm install microsoft-cognitiveservices-speech-sdk` . Para obter instruções de instalação guiadas, consulte o artigo [de início.](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet%2Clinux%2Cjre%2Cnodejs&pivots=programming-language-javascript)

Utilize a seguinte `require` declaração para importar o SDK.

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Para obter mais informações sobre `require` , consulte a [documentação necessária.](https://nodejs.org/en/knowledge/getting-started/what-is-require/)

## <a name="create-a-speech-configuration"></a>Criar uma configuração de discurso

Para ligar para o serviço de discurso usando o SDK de discurso, precisa de criar um [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) . Esta classe inclui informações sobre a sua subscrição, como a sua chave e região associada, ponto final, anfitrião ou token de autorização. Crie uma [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) utilização da sua chave e região. Consulte as [teclas e a](../../../overview.md#find-keys-and-region) página da região para encontrar o seu par de regiões-chave.

```javascript
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
```

Há algumas outras formas de inicializar [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) uma:

* Com um ponto final: passe num ponto final de serviço de discurso. Uma chave ou sinal de autorização é opcional.
* Com um anfitrião: passe em um endereço de anfitrião. Uma chave ou sinal de autorização é opcional.
* Com um sinal de autorização: passe em um token de autorização e na região associada.

> [!NOTE]
> Independentemente de estar a realizar reconhecimento de voz, síntese de fala, tradução ou reconhecimento de intenções, irá sempre criar uma configuração.

## <a name="recognize-from-microphone-browser-only"></a>Reconhecer a partir do microfone (apenas browser)

Reconhecer o discurso de um microfone não é **suportado em Node.js**, e só é suportado num ambiente JavaScript baseado no navegador. Consulte a [amostra reagir](https://github.com/Azure-Samples/AzureSpeechReactSample) no GitHub para ver o [discurso-texto a partir da implementação do microfone](https://github.com/Azure-Samples/AzureSpeechReactSample/blob/main/src/App.js#L29).

> [!NOTE]
> Se pretender utilizar um dispositivo *específico* de entrada de áudio, tem de especificar o ID do dispositivo no `AudioConfig` . Saiba [como obter o ID do dispositivo](../../../how-to-select-audio-input-devices.md) para o seu dispositivo de entrada de áudio.

## <a name="recognize-from-file"></a>Reconhecer a partir de arquivo 

Para reconhecer a fala a partir de um ficheiro áudio, crie uma `AudioConfig` utilização `fromWavFileInput()` que aceite um `Buffer` objeto. Em seguida, inicialize [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest) um, passando o seu `audioConfig` e `speechConfig` .

```javascript
const fs = require('fs');
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromFile() {
    let audioConfig = sdk.AudioConfig.fromWavFileInput(fs.readFileSync("YourAudioFile.wav"));
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);

    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
        recognizer.close();
    });
}
fromFile();
```

## <a name="recognize-from-in-memory-stream"></a>Reconhecer a partir do fluxo de memória

Para muitos casos de uso, é provável que os seus dados de áudio venham do armazenamento de bolhas, ou de outra forma já estejam na memória como uma [`ArrayBuffer`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) estrutura de dados brutos ou similar. O seguinte código:

* Cria um fluxo de pressão utilizando `createPushStream()` .
* Lê um `.wav` ficheiro que é utilizado para fins de `fs.createReadStream` demonstração, mas se já tiver dados áudio num `ArrayBuffer` , pode saltar diretamente para escrever o conteúdo para o fluxo de entrada.
* Cria uma config de áudio utilizando o fluxo de impulso.

```javascript
const fs = require('fs');
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromStream() {
    let pushStream = sdk.AudioInputStream.createPushStream();

    fs.createReadStream("YourAudioFile.wav").on('data', function(arrayBuffer) {
        pushStream.write(arrayBuffer.slice());
    }).on('end', function() {
        pushStream.close();
    });
 
    let audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
        recognizer.close();
    });
}
fromStream();
```

A utilização de um fluxo de pressão como entrada pressupõe que os dados de áudio são um PCM cru, por exemplo, saltar quaisquer cabeçalhos.
A API continuará a funcionar em certos casos se o cabeçalho não tiver sido ignorado, mas para que os melhores resultados considerem implementar lógica para ler os cabeçalhos para que o `fs` início *dos dados áudio* comece.

## <a name="error-handling"></a>Processamento de erros

Os exemplos anteriores simplesmente obtêm o texto reconhecido `result.text` de , mas para lidar com erros e outras respostas, terá de escrever algum código para lidar com o resultado. O seguinte código avalia o [`result.reason`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognitionresult#reason) imóvel e:

* Imprime o resultado do reconhecimento: `ResultReason.RecognizedSpeech`
* Se não houver correspondência de reconhecimento, informe o utilizador: `ResultReason.NoMatch`
* Se for encontrado um erro, imprima a mensagem de erro: `ResultReason.Canceled`

```javascript
switch (result.reason) {
    case ResultReason.RecognizedSpeech:
        console.log(`RECOGNIZED: Text=${result.text}`);
        break;
    case ResultReason.NoMatch:
        console.log("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        const cancellation = CancellationDetails.fromResult(result);
        console.log(`CANCELED: Reason=${cancellation.reason}`);

        if (cancellation.reason == CancellationReason.Error) {
            console.log(`CANCELED: ErrorCode=${cancellation.ErrorCode}`);
            console.log(`CANCELED: ErrorDetails=${cancellation.errorDetails}`);
            console.log("CANCELED: Did you update the subscription info?");
        }
        break;
    }
```

## <a name="continuous-recognition"></a>Reconhecimento contínuo

Os exemplos anteriores usam o reconhecimento de um único tiro, que reconhece uma única expressão. O fim de uma única expressão é determinado ouvindo o silêncio no final ou até que um máximo de 15 segundos de áudio seja processado.

Em contrapartida, o reconhecimento contínuo é utilizado quando se pretende **controlar** quando se deve deixar de reconhecer. Requer que subscreva o `Recognizing` `Recognized` , e `Canceled` eventos para obter os resultados de reconhecimento. Para parar o reconhecimento, tem de [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#stopcontinuousrecognitionasync) ligar. Aqui está um exemplo de como o reconhecimento contínuo é realizado num ficheiro de entrada de áudio.

Comece por definir a entrada e inicializar [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer) a:

```javascript
const recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
```

Em seguida, subscreva os eventos enviados do [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer) .

* [`recognizing`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognizing): Sinal para eventos que contenham resultados intermédios de reconhecimento.
* [`recognized`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognized): Sinal para eventos que contenham resultados finais de reconhecimento (indicando uma tentativa de reconhecimento bem sucedida).
* [`sessionStopped`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#sessionstopped): Sinal para eventos que indiquem o fim de uma sessão de reconhecimento (operação).
* [`canceled`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#canceled): Sinal para eventos que contenham resultados de reconhecimento cancelados (indicando uma tentativa de reconhecimento que foi cancelada em resultado ou um pedido de cancelamento direto ou, em alternativa, uma falha de transporte ou protocolo).

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`RECOGNIZING: Text=${e.result.text}`);
};

recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`RECOGNIZED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be recognized.");
    }
};

recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);

    if (e.reason == CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the subscription info?");
    }

    recognizer.stopContinuousRecognitionAsync();
};

recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

Com tudo preparado, ligue [`startContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#startcontinuousrecognitionasync) para começar a reconhecer.

```javascript
recognizer.startContinuousRecognitionAsync();

// make the following call at some point to stop recognition.
// recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Modo de ditado

Ao utilizar o reconhecimento contínuo, pode ativar o processamento de ditados utilizando a função correspondente de "ativar o ditado". Este modo fará com que a instância config da fala interprete descrições de palavras de estruturas de frases como a pontuação. Por exemplo, a expressão "Vives no ponto de interrogação da cidade" seria interpretada como o texto "Vives na cidade?".

Para ativar o modo de ditado, utilize o [`enableDictation`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#enabledictation--) método no seu [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) .

```javascript
speechConfig.enableDictation();
```

## <a name="change-source-language"></a>Alterar linguagem de origem

Uma tarefa comum para o reconhecimento da fala é especificar a linguagem de entrada (ou origem). Vamos ver como mudaria a língua de entrada para italiana. No seu código, encontre o [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) seu, em seguida, adicione esta linha diretamente abaixo.

```javascript
speechConfig.speechRecognitionLanguage = "it-IT";
```

A [`speechRecognitionLanguage`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#speechrecognitionlanguage) propriedade espera uma cadeia de formato idioma-local. Pode fornecer qualquer valor na coluna **Locale** na lista de [locais/idiomas suportados.](../../../language-support.md)

## <a name="improve-recognition-accuracy"></a>Melhorar a precisão do reconhecimento

As listas de frases são usadas para identificar frases conhecidas em dados áudio, como o nome de uma pessoa ou uma localização específica. Ao fornecer uma lista de frases, melhora a precisão do reconhecimento da fala.

Como exemplo, se tiver um comando "Move to" e um possível destino de "Ward" que possa ser falado, pode adicionar uma entrada de "Move to Ward". Adicionar uma frase aumentará a probabilidade de quando o áudio for reconhecido que "Move to Ward" será reconhecido em vez de "Mover-se para"

Palavras simples ou frases completas podem ser adicionadas a uma Lista de Frases. Durante o reconhecimento, uma entrada numa lista de frases é usada para impulsionar o reconhecimento das palavras e frases na lista, mesmo quando as entradas aparecem no meio da expressão. 

> [!IMPORTANT]
> A funcionalidade Lista de Frases está disponível nos seguintes idiomas: en-US, de-DE, en-AU, en-CA, en-GB, es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN

Para utilizar uma lista de frases, primeiro crie um [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar) objeto e, em seguida, adicione palavras e frases específicas com [`addPhrase`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar#addphrase-string-) .

Quaisquer alterações que [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar) entrem em vigor no próximo reconhecimento ou após uma reconexão ao serviço Discurso.

```javascript
const phraseList = sdk.PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Se precisar de limpar a sua lista de frases:

```javascript
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Outras opções para melhorar a precisão do reconhecimento

As listas de frases são apenas uma opção para melhorar a precisão do reconhecimento. Também pode: 

* [Melhorar a precisão com Discurso Personalizado](../../../custom-speech-overview.md)
* [Melhorar a precisão com modelos de inquilinos](../../../tutorial-tenant-model.md)

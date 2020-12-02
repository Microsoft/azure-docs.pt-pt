---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 26afd53c1ff4c6f13b6f3a237ccf12aa0d46ffc6
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509434"
---
Uma das características centrais do serviço de fala é a capacidade de reconhecer e transcrever a fala humana (muitas vezes referida como discurso-a-texto). Neste arranque rápido, aprende-se a usar o Speech SDK nas suas apps e produtos para realizar conversão de fala a texto de alta qualidade.

## <a name="skip-to-samples-on-github"></a>Salte para as amostras no GitHub

Se quiser saltar diretamente para o código de amostra, consulte as [amostras de arranque rápido do JavaScript](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que tem uma conta Azure e subscrição do serviço de fala. Se não tiver uma conta e subscrição, [experimente gratuitamente o serviço Desemação](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instale o SDK de discurso

Antes de poder fazer qualquer coisa, terá de instalar o <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">SDK <span class="docon docon-navigate-external x-hidden-focus"></span> </a>de voz para JavaScript . Dependendo da sua plataforma, utilize as seguintes instruções:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Navegador Web <span class="docon docon-navigate-external x-hidden-focus"></span></a>

Além disso, dependendo do ambiente-alvo, utilize um dos seguintes:

# <a name="script"></a>[script](#tab/script)

Faça o download e extrai o <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">Ficheiro SDK de discurso para <span class="docon docon-navigate-external x-hidden-focus"></span> JavaScript</a> *microsoft.cognitiveservices.speech.sdk.bundle.js* e coloque-o numa pasta acessível ao seu ficheiro HTML.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Se estiver a direcionar um navegador web e a utilizar a `<script>` etiqueta; o `sdk` prefixo não é necessário quando se refere às aulas. O `sdk` prefixo é um pseudónimo usado para dar o nome ao `require` módulo.

# <a name="require"></a>[requerer](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Para mais informações `require` sobre, veja <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">o que é necessário? <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

---

## <a name="create-a-speech-configuration"></a>Criar uma configuração de discurso

Para ligar para o serviço de discurso usando o SDK de discurso, precisa de criar um [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest) . Esta classe inclui informações sobre a sua subscrição, como a sua chave e região associada, ponto final, anfitrião ou token de autorização. Crie uma [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest) utilização da sua chave e região. Consulte as [teclas e a](../../../overview.md#find-keys-and-region) página da região para encontrar o seu par de regiões-chave.

```javascript
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
```

Há algumas outras formas de inicializar [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest) uma:

* Com um ponto final: passe num ponto final de serviço de discurso. Uma chave ou sinal de autorização é opcional.
* Com um anfitrião: passe em um endereço de anfitrião. Uma chave ou sinal de autorização é opcional.
* Com um sinal de autorização: passe em um token de autorização e na região associada.

> [!NOTE]
> Independentemente de estar a realizar reconhecimento de voz, síntese de fala, tradução ou reconhecimento de intenções, irá sempre criar uma configuração.

## <a name="recognize-from-microphone-browser-only"></a>Reconhecer a partir do microfone (apenas browser)

Para reconhecer a fala utilizando o microfone do dispositivo, crie uma `AudioConfig` utilização `fromDefaultMicrophoneInput()` . Em seguida, inicialize [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest) um, passando o seu `speechConfig` e `audioConfig` .

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromMic() {
    let audioConfig = sdk.AudioConfig.fromDefaultMicrophoneInput();
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    
    console.log('Speak into your microphone.');
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
    });
}
fromMic();
```

Se pretender utilizar um dispositivo *específico* de entrada de áudio, tem de especificar o ID do dispositivo no `AudioConfig` . Saiba [como obter o ID do dispositivo](../../../how-to-select-audio-input-devices.md) para o seu dispositivo de entrada de áudio.

## <a name="recognize-from-file"></a>Reconhecer a partir de arquivo 

# <a name="browser"></a>[Browser](#tab/browser)

Para reconhecer a fala a partir de um ficheiro áudio num ambiente JavaScript baseado no navegador, utiliza a `fromWavFileInput()` função para criar um [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?preserve-view=true&view=azure-node-latest) . A função `fromWavFileInput()` espera um objeto JavaScript como [`File`](https://developer.mozilla.org/en-US/docs/Web/API/File/File) parâmetro.

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromFile() {
    // wavByteContent should be a byte array of the raw wav content
    let file = new File([wavByteContent]);
    let audioConfig = sdk.AudioConfig.fromWavFileInput(file);
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
    });
}
fromFile();
```

# <a name="nodejs"></a>[Node.js](#tab/node)

Para reconhecer a fala a partir de um ficheiro áudio em Node.js, deve ser utilizado um padrão de design alternativo utilizando um fluxo de pressão, uma vez que o objeto JavaScript [`File`](https://developer.mozilla.org/en-US/docs/Web/API/File/File) não pode ser utilizado num tempo de execução Node.js. O seguinte código:

* Cria um fluxo de impulso usando `createPushStream()`
* Abre o `.wav` ficheiro criando um fluxo de leitura, e escreve-o para o fluxo de impulso
* Cria um config de áudio usando o fluxo de impulso

```javascript
const fs = require('fs');
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromFile() {
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
fromFile();
```

A utilização de um fluxo de pressão como entrada pressupõe que os dados de áudio são um PCM cru, por exemplo, saltar quaisquer cabeçalhos.
A API continuará a funcionar em certos casos se o cabeçalho não tiver sido ignorado, mas para que os melhores resultados considerem implementar lógica para ler os cabeçalhos para que o `fs` início *dos dados áudio* comece.

---

## <a name="error-handling"></a>Processamento de erros

Os exemplos anteriores simplesmente obtêm o texto reconhecido `result.text` de , mas para lidar com erros e outras respostas, terá de escrever algum código para lidar com o resultado. O seguinte código avalia o [`result.reason`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognitionresult?preserve-view=true&view=azure-node-latest#reason) imóvel e:

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

Em contrapartida, o reconhecimento contínuo é utilizado quando se pretende **controlar** quando se deve deixar de reconhecer. Requer que subscreva o `Recognizing` `Recognized` , e `Canceled` eventos para obter os resultados de reconhecimento. Para parar o reconhecimento, tem de [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#stopcontinuousrecognitionasync) ligar. Aqui está um exemplo de como o reconhecimento contínuo é realizado num ficheiro de entrada de áudio.

Comece por definir a entrada e inicializar [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest) a:

```javascript
const recognizer = new sdk.SpeechRecognizer(speechConfig);
```

Em seguida, subscreva os eventos enviados do [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest) .

* [`recognizing`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#recognizing): Sinal para eventos que contenham resultados intermédios de reconhecimento.
* [`recognized`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#recognized): Sinal para eventos que contenham resultados finais de reconhecimento (indicando uma tentativa de reconhecimento bem sucedida).
* [`sessionStopped`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#sessionstopped): Sinal para eventos que indiquem o fim de uma sessão de reconhecimento (operação).
* [`canceled`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#canceled): Sinal para eventos que contenham resultados de reconhecimento cancelados (indicando uma tentativa de reconhecimento que foi cancelada em resultado ou um pedido de cancelamento direto ou, em alternativa, uma falha de transporte ou protocolo).

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

Com tudo preparado, ligue [`startContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#startcontinuousrecognitionasync) para começar a reconhecer.

```javascript
recognizer.startContinuousRecognitionAsync();

// make the following call at some point to stop recognition.
// recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Modo de ditado

Ao utilizar o reconhecimento contínuo, pode ativar o processamento de ditados utilizando a função correspondente de "ativar o ditado". Este modo fará com que a instância config da fala interprete descrições de palavras de estruturas de frases como a pontuação. Por exemplo, a expressão "Vives no ponto de interrogação da cidade" seria interpretada como o texto "Vives na cidade?".

Para ativar o modo de ditado, utilize o [`enableDictation`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest#enabledictation--) método no seu [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest) .

```javascript
speechConfig.enableDictation();
```

## <a name="change-source-language"></a>Alterar linguagem de origem

Uma tarefa comum para o reconhecimento da fala é especificar a linguagem de entrada (ou origem). Vamos ver como mudaria a língua de entrada para italiana. No seu código, encontre o [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest) seu, em seguida, adicione esta linha diretamente abaixo.

```javascript
speechConfig.speechRecognitionLanguage = "it-IT";
```

A [`speechRecognitionLanguage`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest#speechrecognitionlanguage) propriedade espera uma cadeia de formato idioma-local. Pode fornecer qualquer valor na coluna **Locale** na lista de [locais/idiomas suportados.](../../../language-support.md)

## <a name="improve-recognition-accuracy"></a>Melhorar a precisão do reconhecimento

Há algumas formas de melhorar a precisão do reconhecimento com o Discurso Vamos ver as Listas de Frases. As listas de frases são usadas para identificar frases conhecidas em dados áudio, como o nome de uma pessoa ou uma localização específica. Palavras simples ou frases completas podem ser adicionadas a uma Lista de Frases. Durante o reconhecimento, é utilizada uma entrada numa lista de frases se for incluída no áudio uma correspondência exata para toda a frase. Se não for encontrado um jogo exato com a frase, o reconhecimento não é assistido.

> [!IMPORTANT]
> A funcionalidade Lista de Frases só está disponível em inglês.

Para utilizar uma lista de frases, primeiro crie um [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?preserve-view=true&view=azure-node-latest) objeto e, em seguida, adicione palavras e frases específicas com [`addPhrase`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?preserve-view=true&view=azure-node-latest#addphrase-string-) .

Quaisquer alterações que [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?preserve-view=true&view=azure-node-latest) entrem em vigor no próximo reconhecimento ou após uma reconexão ao serviço Discurso.

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
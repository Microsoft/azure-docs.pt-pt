---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 07/14/2020
ms.author: v-demjoh
ms.custom: devx-track-js
ms.openlocfilehash: b43c0da3303b4cdfd4941f9d76b663f8089a1417
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105105458"
---
Uma das características centrais do serviço de fala é a capacidade de reconhecer a fala humana e traduzi-la para outras línguas. Neste arranque rápido aprende-se a usar o Speech SDK nas suas apps e produtos para realizar tradução de fala de alta qualidade. Este arranque rápido abrange temas como:

* Tradução de discurso a texto
* Traduzindo o discurso para várias línguas-alvo
* Realização de tradução direta do discurso para a fala

## <a name="skip-to-samples-on-github"></a>Salte para as amostras no GitHub

Se quiser saltar diretamente para o código de amostra, consulte as [amostras de arranque rápido do JavaScript](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser/translate-speech-to-text) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que tem uma conta Azure e subscrição do serviço de fala. Se não tiver uma conta e subscrição, [experimente gratuitamente o serviço Desemação](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instale o SDK de discurso

Antes de poder fazer qualquer coisa, terá de instalar o <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">SDK </a>de voz para JavaScript . Dependendo da sua plataforma, utilize as seguintes instruções:
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Navegador Web </a>

Além disso, dependendo do ambiente-alvo, utilize um dos seguintes:

# <a name="script"></a>[script](#tab/script)

Faça o download e extrai o <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">Ficheiro SDK de discurso para JavaScript</a> *microsoft.cognitiveservices.speech.sdk.bundle.js* e coloque-o numa pasta acessível ao seu ficheiro HTML.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Se estiver a direcionar um navegador web e a usar a `<script>` etiqueta; o `sdk` prefixo não é necessário. O `sdk` prefixo é um pseudónimo usado para dar o nome ao `require` módulo.

# <a name="import"></a>[importar](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

Para obter mais informações `import` sobre, consulte <a href="https://javascript.info/import-export" target="_blank">a exportação e a importação. </a>

# <a name="require"></a>[requerer](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Para mais informações `require` sobre, veja <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">o que é necessário? </a>

---

## <a name="create-a-translation-configuration"></a>Criar uma configuração de tradução

Para ligar para o serviço de tradução utilizando o SDK de voz, precisa de criar um [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) . Esta classe inclui informações sobre a sua subscrição, como a sua chave e região associada, ponto final, anfitrião ou token de autorização.

> [!NOTE]
> Independentemente de estar a realizar reconhecimento de voz, síntese de fala, tradução ou reconhecimento de intenções, irá sempre criar uma configuração.
Há algumas maneiras de inicializar [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) um:

* Com uma subscrição: passe numa chave e na região associada.
* Com um ponto final: passe num ponto final de serviço de discurso. Uma chave ou sinal de autorização é opcional.
* Com um anfitrião: passe em um endereço de anfitrião. Uma chave ou sinal de autorização é opcional.
* Com um sinal de autorização: passe em um token de autorização e na região associada.

Vamos ver como um [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) é criado usando uma chave e região. Obtenha estas credenciais seguindo os passos no [serviço Try the Speech gratuitamente](../../../overview.md#try-the-speech-service-for-free).

```javascript
const speechTranslationConfig = SpeechTranslationConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-translator"></a>Inicializar um tradutor

Depois de criar um [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) , o próximo passo é inicializar um [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) . Quando rubricar [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) um, terá de passar o seu `speechTranslationConfig` . Isto fornece as credenciais que o serviço de tradução requer para validar o seu pedido.

Se está a traduzir a fala fornecida através do microfone padrão do seu dispositivo, eis como [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) deve ser:

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

Se quiser especificar o dispositivo de entrada de áudio, terá de criar um [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig) e fornecer o parâmetro ao `audioConfig` rubricar o seu [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) .

> [!TIP]
> [Saiba como obter o ID do dispositivo para o seu dispositivo de entrada de áudio](../../../how-to-select-audio-input-devices.md).
Referenciar o `AudioConfig` objeto da seguinte forma:

```javascript
const audioConfig = AudioConfig.fromDefaultMicrophoneInput();
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

Se quiser fornecer um ficheiro áudio em vez de utilizar um microfone, ainda terá de fornecer um `audioConfig` . No entanto, isto só pode ser feito quando seNode.js **e** quando cria um , em vez de ligar [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig) - vai ligar e passar o `fromDefaultMicrophoneInput` `fromWavFileOutput` `filename` parâmetro.

```javascript
const audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

## <a name="translate-speech"></a>Traduzir voz

A [aula TranslationRecognizer](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) para o Speech SDK para JavaScript expõe alguns métodos que pode usar para tradução de voz.

* Tradução de um único tiro (async) - Executa a tradução num modo não-bloqueador (assíncronos). Isto traduzirá uma única expressão. O fim de uma única expressão é determinado ouvindo o silêncio no final ou até que um máximo de 15 segundos de áudio seja processado.
* Tradução contínua (async) - Assíncronia inicia uma operação de tradução contínua. O utilizador regista-se em eventos e trata de vários estados de aplicação. Para parar a tradução contínua assíncronea, ligue [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#stopcontinuousrecognitionasync) .

> [!NOTE]
> Saiba mais sobre como [escolher um modo de reconhecimento de voz.](../../../get-started-speech-to-text.md)
### <a name="specify-a-target-language"></a>Especificar uma língua-alvo

Para traduzir, deve especificar tanto uma língua de origem como pelo menos uma língua-alvo.
Pode escolher um idioma de origem utilizando um local listado na [tabela de tradução do Discurso](../../../language-support.md#speech-translation). Encontre as suas opções para linguagem traduzida no mesmo link. As suas opções para línguas-alvo diferem quando pretende ver texto ou quer ouvir a fala traduzida sintetizada. Para traduzir do inglês para o alemão, modifique o objeto config de tradução:

```javascript
speechTranslationConfig.speechRecognitionLanguage = "en-US";
speechTranslationConfig.addTargetLanguage("de");
```

### <a name="single-shot-recognition"></a>Reconhecimento de um único tiro

Aqui está um exemplo de tradução assíncronea de tiro único [`recognizeOnceAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#recognizeonceasync) usando:

```javascript
recognizer.recognizeOnceAsync(result => {
    // Interact with result
});
```

Terá de escrever um código para lidar com o resultado. Esta amostra avalia a [`result.reason`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognitionresult) tradução para alemão:

```javascript
recognizer.recognizeOnceAsync(
  function (result) {
    let translation = result.translations.get("de");
    window.console.log(translation);
    recognizer.close();
  },
  function (err) {
    window.console.log(err);
    recognizer.close();
});
```

O seu código também pode lidar com as atualizações fornecidas enquanto a tradução está a ser processada.
Pode utilizar estas atualizações para fornecer feedback visual sobre o progresso da tradução.
Consulte [este JavaScript Node.js exemplo](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/js/node/translation.js) para o código de amostra que mostra as atualizações fornecidas durante o processo de tradução. O código a seguir também apresenta detalhes produzidos durante o processo de tradução.

```javascript
recognizer.recognizing = function (s, e) {
    var str = ("(recognizing) Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " +  e.result.text +
            " Translation:");
    str += e.result.translations.get("de");
    console.log(str);
};
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " + e.result.text +
            " Translation:";
    str += e.result.translations.get("de");
    str += "\r\n";
    console.log(str);
};
```

### <a name="continuous-translation"></a>Tradução contínua

A tradução contínua é um pouco mais envolvida do que o reconhecimento de um único tiro. Requer que subscreva o `recognizing` `recognized` , e `canceled` eventos para obter os resultados de reconhecimento. Para parar a tradução, tem de [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#stopcontinuousrecognitionasync) ligar. Aqui está um exemplo de como a tradução contínua é realizada num ficheiro de entrada de áudio.

Comecemos por definir a entrada e inicializar [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) um:

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

Subscreveremos os eventos enviados do [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) .

* [`recognizing`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#recognizing): Sinal para eventos que contenham resultados de tradução intermédia.
* [`recognized`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#recognized): Sinal para eventos que contenham resultados finais de tradução (indicando uma tentativa de tradução bem sucedida).
* [`sessionStopped`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#sessionstopped): Sinal para eventos que indiquem o fim de uma sessão de tradução (operação).
* [`canceled`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#canceled): Sinal para eventos que contenham resultados de tradução cancelados (indicando uma tentativa de tradução que foi cancelada em resultado ou um pedido de cancelamento direto ou, em alternativa, uma falha de transporte ou protocolo).

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`TRANSLATING: Text=${e.result.text}`);
};
recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`TRANSLATED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be translated.");
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

Com tudo preparado, podemos [`startContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#startcontinuousrecognitionasync) ligar.

```javascript
// Starts continuous recognition. Uses stopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync();
// Something later can call, stops recognition.
// recognizer.StopContinuousRecognitionAsync();
```

## <a name="choose-a-source-language"></a>Escolha uma língua de origem

Uma tarefa comum para a tradução da fala é especificar a linguagem de entrada (ou origem). Vamos ver como mudaria a língua de entrada para italiana. No seu código, encontre o seu [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) e, em seguida, adicione a seguinte linha diretamente abaixo.

```javascript
speechTranslationConfig.speechRecognitionLanguage = "it-IT";
```

A [`speechRecognitionLanguage`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig#speechrecognitionlanguage) propriedade espera uma cadeia de formato idioma-local. Pode fornecer qualquer valor na coluna **Locale** na lista de [locais/idiomas suportados.](../../../language-support.md)

## <a name="choose-one-or-more-target-languages"></a>Escolha uma ou mais línguas-alvo

O SDK de discurso pode traduzir-se para várias línguas-alvo em paralelo. As línguas-alvo disponíveis são um pouco diferentes da lista de línguas de origem, e você especifica línguas-alvo usando um código de idioma, em vez de um local.
Consulte uma lista de códigos linguísticos para alvos de texto na [tabela de tradução de voz na página de suporte à língua](../../../language-support.md#speech-translation). Também pode encontrar detalhes sobre a tradução em línguas sintetizadas lá.

O seguinte código adiciona o alemão como língua-alvo:

```javascript
translationConfig.addTargetLanguage("de");
```

Uma vez que várias traduções linguísticas-alvo são possíveis, o seu código deve especificar a linguagem-alvo ao examinar o resultado. O seguinte código obtém resultados de tradução para alemão.

```javascript
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " +
            sdk.ResultReason[e.result.reason] +
            " Text: " + e.result.text + " Translations:";
    var language = "de";
    str += " [" + language + "] " + e.result.translations.get(language);
    str += "\r\n";
    // show str somewhere
};
```
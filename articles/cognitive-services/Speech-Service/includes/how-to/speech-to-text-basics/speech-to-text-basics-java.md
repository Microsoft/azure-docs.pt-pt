---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 2de836a63eed79b7d166db220218e0c11ef11e70
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399811"
---
## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que tem uma subscrição de serviço de conta Azure e Speech. Se não tiver uma conta e subscrição, [experimente gratuitamente o serviço De Fala.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>Instale o SDK do Discurso

Antes de poder fazer qualquer coisa, terá de instalar o SDK do Discurso. Dependendo da sua plataforma, utilize as seguintes instruções:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=jre&pivots=programming-language-java" target="_blank">Tempo de execução de Java<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=android&pivots=programming-language-java" target="_blank">Android<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Criar uma configuração de fala

Para chamar o serviço de Fala usando o SDK do Discurso, é necessário criar um [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable). Esta aula inclui informações sobre a sua subscrição, como a sua chave e região associada, ponto final, anfitrião ou sinal de autorização.

> [!NOTE]
> Independentemente de estar a realizar reconhecimento de discurso, síntese de fala, tradução ou reconhecimento de intenções, irá sempre criar uma configuração.

Há algumas maneiras de inicializar um: [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable)

* Com uma subscrição: passe numa chave e na região associada.
* Com um ponto final: passe num ponto final do serviço da Fala. Uma chave ou ficha de autorização é opcional.
* Com um anfitrião: passe num endereço de anfitrião. Uma chave ou ficha de autorização é opcional.
* Com um símbolo de autorização: passe num símbolo de autorização e na região associada.

Vamos ver como um [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) é criado usando uma chave e região. Consulte a página de apoio da [região](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) para encontrar o seu identificador de região.

```java
SpeechConfig config = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>Inicializar um reconhecível

Depois de ter [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable)criado um , o próximo [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable)passo é inicializar um . Quando rubricar [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable)um, terá de passar `config`o seu. Isto fornece as credenciais que o serviço de fala requer para validar o seu pedido.

Se está a reconhecer a fala usando o microfone padrão [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable) do seu dispositivo, eis como deve ser:

```java
SpeechRecognizer recognizer = new SpeechRecognizer(config);
```

Se pretender especificar o dispositivo de entrada de áudio, [`AudioConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-java-stable) terá de `audioConfig` criar um e [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable)fornecer o parâmetro ao rubricar o seu .

> [!TIP]
> [Aprenda a obter o ID do dispositivo para o seu dispositivo](../../../how-to-select-audio-input-devices.md)de entrada de áudio .

Primeiro, adicione `import` as seguintes declarações.

```java
import java.util.concurrent.Future;
import com.microsoft.cognitiveservices.speech.*;
```

Em seguida, poderá fazer referência `AudioConfig` ao objeto da seguinte forma:

```java
AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

Se pretender fornecer um ficheiro áudio em vez de utilizar um `audioConfig`microfone, ainda terá de fornecer um . No entanto, [`AudioConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-java-stable)quando criar `fromDefaultMicrophoneInput`um , em `fromWavFileOutput` vez `filename` de ligar, ligará e passará o parâmetro.

```java
AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

## <a name="recognize-speech"></a>Reconhecer voz

A [classe Recogniser](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable) para o Speech SDK para Java expõe alguns métodos que pode usar para reconhecimento da fala.

* Reconhecimento de tiro único (assincron) - Realiza o reconhecimento num modo não bloqueante (assíncrono). Isto reconhecerá uma única expressão. O fim de uma única expressão é determinado ouvindo o silêncio no final ou até que um máximo de 15 segundos de áudio seja processado.
* Reconhecimento contínuo (assincron) - Inicia assincronicamente o funcionamento de reconhecimento contínuo. Se quiser fornecer um ficheiro áudio em vez de utilizar um microfone, ainda terá de fornecer um. Para parar o reconhecimento contínuo assíncrono, ligue para [pararOReconhecimento ContínuoAsync](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync).

> [!NOTE]
> Saiba mais sobre como [escolher um modo](../../../how-to-choose-recognition-mode.md)de reconhecimento de voz .

### <a name="single-shot-recognition"></a>Reconhecimento de um único tiro

Aqui está um exemplo de reconhecimento assíncrono [`recognizeOnceAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-java-stable)de tiro único usando:

```java
Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = task.get();
```

Tens de escrever um código para lidar com o resultado. Esta amostra avalia: [`result.getReason()`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.resultreason?view=azure-java-stable)

* Imprime o resultado do reconhecimento:`ResultReason.RecognizedSpeech`
* Se não houver correspondência de reconhecimento, informe o utilizador:`ResultReason.NoMatch`
* Se for encontrado um erro, imprima a mensagem de erro:`ResultReason.Canceled`

```java
switch (result.getReason()) {
    case ResultReason.RecognizedSpeech:
        System.out.println("We recognized: " + result.getText());
        exitCode = 0;
        break;
    case ResultReason.NoMatch:
        System.out.println("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled: {
            CancellationDetails cancellation = CancellationDetails.fromResult(result);
            System.out.println("CANCELED: Reason=" + cancellation.getReason());

            if (cancellation.getReason() == CancellationReason.Error) {
                System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                System.out.println("CANCELED: Did you update the subscription info?");
            }
        }
        break;
}
```

### <a name="continuous-recognition"></a>Reconhecimento contínuo

O reconhecimento contínuo é um pouco mais envolvido do que o reconhecimento de um único tiro. Requer que subscreva os `recognizing` `recognized`eventos `canceled` e eventos para obter os resultados do reconhecimento. Para parar o reconhecimento, deve ligar. [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync) Aqui está um exemplo de como o reconhecimento contínuo é realizado num ficheiro de entrada de áudio.

Comecemos por definir a entrada e inicializar um: [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable)

```java
AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

Em seguida, vamos criar uma variável para gerir o estado de reconhecimento da fala. Para começar, declaramos `Semaphore` um no âmbito da aula.

```java
private static Semaphore stopTranslationWithFileSemaphore;
```

Subscreveremos os eventos enviados do. [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable)

* [`recognizing`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognizing?view=azure-java-stable): Sinal para eventos que contenham resultados intermédios de reconhecimento.
* [`recognized`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognized?view=azure-java-stable): Sinal para eventos que contenham resultados finais de reconhecimento (indicando uma tentativa de reconhecimento bem sucedida).
* [`sessionStopped`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.recognizer.sessionstopped?view=azure-java-stable): Sinal de sinalização para eventos que indiquem o fim de uma sessão de reconhecimento (operação).
* [`canceled`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.canceled?view=azure-java-stable): Sinal de sinalização para eventos que contenham resultados de reconhecimento cancelados (indicando uma tentativa de reconhecimento que foi cancelada como resultado ou um pedido de cancelamento direto ou, em alternativa, uma falha de transporte ou protocolo).

```java
// First initialize the semaphore.
stopTranslationWithFileSemaphore = new Semaphore(0);

recognizer.recognizing.addEventListener((s, e) -> {
    System.out.println("RECOGNIZING: Text=" + e.getResult().getText());
});

recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        System.out.println("RECOGNIZED: Text=" + e.getResult().getText());
    }
    else if (e.getResult().getReason() == ResultReason.NoMatch) {
        System.out.println("NOMATCH: Speech could not be recognized.");
    }
});

recognizer.canceled.addEventListener((s, e) -> {
    System.out.println("CANCELED: Reason=" + e.getReason());

    if (e.getReason() == CancellationReason.Error) {
        System.out.println("CANCELED: ErrorCode=" + e.getErrorCode());
        System.out.println("CANCELED: ErrorDetails=" + e.getErrorDetails());
        System.out.println("CANCELED: Did you update the subscription info?");
    }

    stopTranslationWithFileSemaphore.release();
});

recognizer.sessionStopped.addEventListener((s, e) -> {
    System.out.println("\n    Session stopped event.");
    stopTranslationWithFileSemaphore.release();
});
```

Com tudo preparado, podemos ligar. [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync)

```java
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync().get();

// Waits for completion.
stopTranslationWithFileSemaphore.acquire();

// Stops recognition.
recognizer.stopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>Modo dictação

Ao utilizar um reconhecimento contínuo, pode ativar o processamento de ditados utilizando a função correspondente de "ativar o ditado". Este modo fará com que a instância de config da fala interprete descrições de palavras de estruturas de frases como pontuação. Por exemplo, a expressão "Vives no ponto de interrogação da cidade" seria interpretada como o texto "Vives na cidade?".

Para ativar o modo [`enableDictation`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-java-stable) de ditado, utilize o método no seu [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable).

```java
config.enableDictation();
```

## <a name="change-source-language"></a>Alterar a linguagem de origem

Uma tarefa comum para o reconhecimento da fala é especificar a linguagem de entrada (ou fonte). Vamos ver como mudarias a linguagem de entrada para francês. No seu código, [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable)encontre o seu, em seguida, adicione esta linha diretamente abaixo dele.

```java
config.setSpeechRecognitionLanguage("fr-FR");
```

[`setSpeechRecognitionLanguage`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage?view=azure-java-stable)é um parâmetro que toma uma corda como argumento. Pode fornecer qualquer valor na lista de [locais/línguas](../../../language-support.md)suportados.

## <a name="improve-recognition-accuracy"></a>Melhorar a precisão do reconhecimento

Existem algumas formas de melhorar a precisão do reconhecimento com o SDK do discurso. Vamos dar uma olhada nas Listas de Frases. As listas de frases são usadas para identificar frases conhecidas em dados áudio, como o nome de uma pessoa ou um local específico. Palavras simples ou frases completas podem ser adicionadas a uma Lista de Frases. Durante o reconhecimento, é utilizada uma entrada numa lista de frases se for incluída no áudio uma correspondência exata para toda a frase. Se não for encontrado um fósforo exato com a frase, o reconhecimento não é assistido.

> [!IMPORTANT]
> A funcionalidade Lista de Frases só está disponível em inglês.

Para utilizar uma lista de [`PhraseListGrammar`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-java-stable) frases, primeiro crie [`AddPhrase`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar.addphrase?view=azure-java-stable#com_microsoft_cognitiveservices_speech_PhraseListGrammar_addPhrase_String_)um objeto e, em seguida, adicione palavras e frases específicas com .

Quaisquer [`PhraseListGrammar`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-java-stable) alterações que entrem em vigor no próximo reconhecimento ou após uma reconexão ao serviço da Fala.

```java
PhraseListGrammar phraseList = PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Se precisar de limpar a sua lista de frases: 

```java
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Outras opções para melhorar a precisão do reconhecimento

As listas de frases são apenas uma opção para melhorar a precisão do reconhecimento. Também pode: 

* [Melhorar a precisão com Discurso Personalizado](../../../how-to-custom-speech.md)
* [Melhorar a precisão com modelos de inquilinos](../../../tutorial-tenant-model.md)
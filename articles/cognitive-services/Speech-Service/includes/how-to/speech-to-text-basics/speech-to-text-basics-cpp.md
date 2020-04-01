---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: dapine
ms.openlocfilehash: 0c0fa3e07e0cdae6ce28fcd1521b63cb2575767f
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501840"
---
## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que tem uma subscrição de serviço de conta Azure e Speech. Se não tiver uma conta e subscrição, [experimente gratuitamente o serviço De Fala.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>Instale o SDK do Discurso

Antes de poder fazer qualquer coisa, terá de instalar o SDK do Discurso. Dependendo da sua plataforma, utilize as seguintes instruções:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=linux&pivots=programming-language-cpp" target="_blank">Linux<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=macos&pivots=programming-language-cpp" target="_blank">macOS<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=windows&pivots=programming-language-cpp" target="_blank">Janelas<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Criar uma configuração de fala

Para chamar o serviço de Fala usando o SDK do Discurso, é necessário criar um [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig). Esta aula inclui informações sobre a sua subscrição, como a sua chave e região associada, ponto final, anfitrião ou sinal de autorização.

> [!NOTE]
> Independentemente de estar a realizar reconhecimento de discurso, síntese de fala, tradução ou reconhecimento de intenções, irá sempre criar uma configuração.

Há algumas maneiras de inicializar um: [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)

* Com uma subscrição: passe numa chave e na região associada.
* Com um ponto final: passe num ponto final do serviço da Fala. Uma chave ou ficha de autorização é opcional.
* Com um anfitrião: passe num endereço de anfitrião. Uma chave ou ficha de autorização é opcional.
* Com um símbolo de autorização: passe num símbolo de autorização e na região associada.

Vamos ver como um [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) é criado usando uma chave e região. Consulte a página de apoio da [região](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) para encontrar o seu identificador de região.

```cpp
auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>Inicializar um reconhecível

Depois de ter [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)criado um , o próximo [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer)passo é inicializar um . Quando rubricar [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer)um, terá de passar `speech_config`o seu. Isto fornece as credenciais que o serviço de fala requer para validar o seu pedido.

Se está a reconhecer a fala usando o microfone padrão [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) do seu dispositivo, eis como deve ser:

```cpp
auto recognizer = SpeechRecognizer::FromConfig(config);
```

Se pretender especificar o dispositivo de entrada de áudio, [`AudioConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig) terá de `audioConfig` criar um e [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer)fornecer o parâmetro ao rubricar o seu .

> [!TIP]
> [Aprenda a obter o ID do dispositivo para o seu dispositivo](../../../how-to-select-audio-input-devices.md)de entrada de áudio .

Em primeiro lugar, adicione a seguinte `using namespace` declaração após as suas `#include` definições.

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;
```

Em seguida, poderá fazer referência `AudioConfig` ao objeto da seguinte forma:

```cpp
auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
auto recognizer = SpeechRecognizer::FromConfig(config, audioConfig);
```

Se pretender fornecer um ficheiro áudio em vez de utilizar um `audioConfig`microfone, ainda terá de fornecer um . No entanto, [`AudioConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig)quando criar `FromDefaultMicrophoneInput`um , em `FromWavFileOutput` vez `filename` de ligar, ligará e passará o parâmetro.

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

## <a name="recognize-speech"></a>Reconhecer voz

A [classe Recogniser](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) para o SDK de Fala para C++ expõe alguns métodos que pode usar para reconhecimento de voz.

* Reconhecimento de tiro único (assincron) - Realiza o reconhecimento num modo não bloqueante (assíncrono). Isto reconhecerá uma única expressão. O fim de uma única expressão é determinado ouvindo o silêncio no final ou até que um máximo de 15 segundos de áudio seja processado.
* Reconhecimento contínuo (assincron) - Inicia assincronicamente o funcionamento de reconhecimento contínuo. O utilizador tem de se ligar ao lidar com o evento para receber resultados de reconhecimento. Para parar o reconhecimento contínuo assíncrono, ligue. [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync)

> [!NOTE]
> Saiba mais sobre como [escolher um modo](../../../how-to-choose-recognition-mode.md)de reconhecimento de voz .

### <a name="single-shot-recognition"></a>Reconhecimento de um único tiro

Aqui está um exemplo de reconhecimento assíncrono [`RecognizeOnceAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync)de tiro único usando:

```cpp
auto result = recognizer->RecognizeOnceAsync().get();
```

Tens de escrever um código para lidar com o resultado. Esta amostra avalia: [`result->Reason`](https://docs.microsoft.com/cpp/cognitive-services/speech/recognitionresult#reason)

* Imprime o resultado do reconhecimento:`ResultReason::RecognizedSpeech`
* Se não houver correspondência de reconhecimento, informe o utilizador:`ResultReason::NoMatch`
* Se for encontrado um erro, imprima a mensagem de erro:`ResultReason::Canceled`

```cpp
switch (result->Reason)
{
    case ResultReason::RecognizedSpeech:
        cout << "We recognized: " << result->Text << std::endl;
        break;
    case ResultReason::NoMatch:
        cout << "NOMATCH: Speech could not be recognized." << std::endl;
        break;
    case ResultReason::Canceled:
        {
            auto cancellation = CancellationDetails::FromResult(result);
            cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;
    
            if (cancellation->Reason == CancellationReason::Error) {
                cout << "CANCELED: ErrorCode= " << (int)cancellation->ErrorCode << std::endl;
                cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
                cout << "CANCELED: Did you update the subscription info?" << std::endl;
            }
        }
        break;
    default:
        break;
}
```

### <a name="continuous-recognition"></a>Reconhecimento contínuo

O reconhecimento contínuo é um pouco mais envolvido do que o reconhecimento de um único tiro. Requer que subscreva os `Recognizing` `Recognized`eventos `Canceled` e eventos para obter os resultados do reconhecimento. Para parar o reconhecimento, deve ligar para [StopContinuousRecognitionAsync](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync). Aqui está um exemplo de como o reconhecimento contínuo é realizado num ficheiro de entrada de áudio.

Comecemos por definir a entrada e inicializar um: [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer)

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

Em seguida, vamos criar uma variável para gerir o estado de reconhecimento da fala. Para começar, vamos declarar `promise<void>`um, uma vez que no início do reconhecimento podemos assumir com segurança que não está terminado.

```cpp
promise<void> recognitionEnd;
```

Subscreveremos os eventos enviados do. [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer)

* [`Recognizing`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizing): Sinal para eventos que contenham resultados intermédios de reconhecimento.
* [`Recognized`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognized): Sinal para eventos que contenham resultados finais de reconhecimento (indicando uma tentativa de reconhecimento bem sucedida).
* [`SessionStopped`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#sessionstopped): Sinal de sinalização para eventos que indiquem o fim de uma sessão de reconhecimento (operação).
* [`Canceled`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#canceled): Sinal de sinalização para eventos que contenham resultados de reconhecimento cancelados (indicando uma tentativa de reconhecimento que foi cancelada como resultado ou um pedido de cancelamento direto ou, em alternativa, uma falha de transporte ou protocolo).

```cpp
recognizer->Recognizing.Connect([](const SpeechRecognitionEventArgs& e)
    {
        cout << "Recognizing:" << e.Result->Text << std::endl;
    });

recognizer->Recognized.Connect([](const SpeechRecognitionEventArgs& e)
    {
        if (e.Result->Reason == ResultReason::RecognizedSpeech)
        {
            cout << "RECOGNIZED: Text=" << e.Result->Text 
                 << " (text could not be translated)" << std::endl;
        }
        else if (e.Result->Reason == ResultReason::NoMatch)
        {
            cout << "NOMATCH: Speech could not be recognized." << std::endl;
        }
    });

recognizer->Canceled.Connect([&recognitionEnd](const SpeechRecognitionCanceledEventArgs& e)
    {
        cout << "CANCELED: Reason=" << (int)e.Reason << std::endl;
        if (e.Reason == CancellationReason::Error)
        {
            cout << "CANCELED: ErrorCode=" << (int)e.ErrorCode << "\n"
                 << "CANCELED: ErrorDetails=" << e.ErrorDetails << "\n"
                 << "CANCELED: Did you update the subscription info?" << std::endl;

            recognitionEnd.set_value(); // Notify to stop recognition.
        }
    });

recognizer->SessionStopped.Connect([&recognitionEnd](const SessionEventArgs& e)
    {
        cout << "Session stopped.";
        recognitionEnd.set_value(); // Notify to stop recognition.
    });
```

Com tudo preparado, podemos ligar. [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#startcontinuousrecognitionasync)

```cpp
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer->StartContinuousRecognitionAsync().get();

// Waits for recognition end.
recognitionEnd.get_future().get();

// Stops recognition.
recognizer->StopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>Modo dictação

Ao utilizar um reconhecimento contínuo, pode ativar o processamento de ditados utilizando a função correspondente de "ativar o ditado". Este modo fará com que a instância de config da fala interprete descrições de palavras de estruturas de frases como pontuação. Por exemplo, a expressão "Vives no ponto de interrogação da cidade" seria interpretada como o texto "Vives na cidade?".

Para ativar o modo [`EnableDictation`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation) de ditado, utilize o método no seu [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig).

```cpp
config->EnableDictation();
```

## <a name="change-source-language"></a>Alterar a linguagem de origem

Uma tarefa comum para o reconhecimento da fala é especificar a linguagem de entrada (ou fonte). Vamos ver como mudarias a linguagem de entrada para alemão. No seu código, [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)encontre o seu, em seguida, adicione esta linha diretamente abaixo dele.

```cpp
config->SetSpeechRecognitionLanguage("fr-FR");
```

[`SetSpeechRecognitionLanguage`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage)é um parâmetro que toma uma corda como argumento. Pode fornecer qualquer valor na lista de [locais/línguas](../../../language-support.md)suportados.

## <a name="improve-recognition-accuracy"></a>Melhorar a precisão do reconhecimento

Existem algumas formas de melhorar a precisão do reconhecimento com o SDK do discurso. Vamos dar uma olhada nas Listas de Frases. As listas de frases são usadas para identificar frases conhecidas em dados áudio, como o nome de uma pessoa ou um local específico. Palavras simples ou frases completas podem ser adicionadas a uma Lista de Frases. Durante o reconhecimento, é utilizada uma entrada numa lista de frases se for incluída no áudio uma correspondência exata para toda a frase. Se não for encontrado um fósforo exato com a frase, o reconhecimento não é assistido.

> [!IMPORTANT]
> A funcionalidade Lista de Frases só está disponível em inglês.

Para utilizar uma lista de [`PhraseListGrammar`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar) frases, primeiro crie [`AddPhrase`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar#addphrase)um objeto e, em seguida, adicione palavras e frases específicas com .

Quaisquer [`PhraseListGrammar`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar) alterações que entrem em vigor no próximo reconhecimento ou após uma reconexão ao serviço da Fala.

```cpp
auto phraseListGrammar = PhraseListGrammar::FromRecognizer(recognizer);
phraseListGrammar->AddPhrase("Supercalifragilisticexpialidocious");
```

Se precisar de limpar a sua lista de frases: 

```cpp
phraseListGrammar->Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Outras opções para melhorar a precisão do reconhecimento

As listas de frases são apenas uma opção para melhorar a precisão do reconhecimento. Também pode: 

* [Melhorar a precisão com Voz Personalizada](../../../how-to-custom-speech.md)
* [Melhorar a precisão com modelos de inquilinos](../../../tutorial-tenant-model.md)
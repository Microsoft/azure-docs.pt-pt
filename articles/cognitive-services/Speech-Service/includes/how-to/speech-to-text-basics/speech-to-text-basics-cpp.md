---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: trbye
ms.openlocfilehash: 3d8558fbee05f663d72be64cb45ed4a2481513a3
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104956"
---
Uma das características centrais do serviço de fala é a capacidade de reconhecer e transcrever a fala humana (muitas vezes referida como discurso-a-texto). Neste arranque rápido, aprende-se a usar o Speech SDK nas suas apps e produtos para realizar conversão de fala a texto de alta qualidade.

## <a name="skip-to-samples-on-github"></a>Salte para as amostras no GitHub

Se quiser saltar diretamente para o código de amostra, consulte as [amostras de arranque rápido C++](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que tem uma conta Azure e subscrição do serviço de fala. Se não tiver uma conta e subscrição, [experimente gratuitamente o serviço Desemação](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instale o SDK de discurso

Antes de poder fazer qualquer coisa, terá de instalar o SDK do Discurso. Dependendo da sua plataforma, utilize as seguintes instruções:

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=linux" target="_blank">Linux </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=macos" target="_blank">macOS </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=windows" target="_blank">Janelas </a>

## <a name="create-a-speech-configuration"></a>Criar uma configuração de discurso

Para ligar para o serviço de discurso usando o SDK de discurso, precisa de criar um [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) . Esta classe inclui informações sobre a sua subscrição, como a sua chave e região associada, ponto final, anfitrião ou token de autorização. Crie um [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) utilizando a sua chave e região. Consulte as [teclas e a](../../../overview.md#find-keys-and-region) página da região para encontrar o seu par de regiões-chave.

```cpp
using namespace std;
using namespace Microsoft::CognitiveServices::Speech;

auto config = SpeechConfig::FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
```

Há algumas outras formas de inicializar [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) uma:

* Com um ponto final: passe num ponto final de serviço de discurso. Uma chave ou sinal de autorização é opcional.
* Com um anfitrião: passe em um endereço de anfitrião. Uma chave ou sinal de autorização é opcional.
* Com um sinal de autorização: passe em um token de autorização e na região associada.

> [!NOTE]
> Independentemente de estar a realizar reconhecimento de voz, síntese de fala, tradução ou reconhecimento de intenções, irá sempre criar uma configuração.

## <a name="recognize-from-microphone"></a>Reconhecer a partir do microfone

Para reconhecer a fala utilizando o microfone do dispositivo, crie uma `AudioConfig` utilização `FromDefaultMicrophoneInput()` . Em seguida, inicialize [`SpeechRecognizer`](/cpp/cognitive-services/speech/speechrecognizer) um, passando o seu `audioConfig` e `config` .

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
auto recognizer = SpeechRecognizer::FromConfig(config, audioConfig);

cout << "Speak into your microphone." << std::endl;
auto result = recognizer->RecognizeOnceAsync().get();
cout << "RECOGNIZED: Text=" << result->Text << std::endl;
```

Se pretender utilizar um dispositivo *específico* de entrada de áudio, tem de especificar o ID do dispositivo no `AudioConfig` . Saiba [como obter o ID do dispositivo](../../../how-to-select-audio-input-devices.md) para o seu dispositivo de entrada de áudio.

## <a name="recognize-from-file"></a>Reconhecer a partir de arquivo

Se quiser reconhecer a fala a partir de um ficheiro áudio em vez de utilizar um microfone, ainda precisa de criar um `AudioConfig` . No entanto, quando cria o [`AudioConfig`](/cpp/cognitive-services/speech/audio-audioconfig) , em vez de ligar - ligue e passe o caminho do `FromDefaultMicrophoneInput()` `FromWavFileInput()` ficheiro.

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);

auto result = recognizer->RecognizeOnceAsync().get();
cout << "RECOGNIZED: Text=" << result->Text << std::endl;
```

## <a name="recognize-speech"></a>Reconhecer voz

A [classe Recogniser](/cpp/cognitive-services/speech/speechrecognizer) para o Discurso SDK para C++ expõe alguns métodos que pode usar para reconhecimento de voz.

### <a name="single-shot-recognition"></a>Reconhecimento de um único tiro

O reconhecimento de um único tiro reconhece assincronamente uma única expressão. O fim de uma única expressão é determinado ouvindo o silêncio no final ou até que um máximo de 15 segundos de áudio seja processado. Aqui está um exemplo de reconhecimento assíncronos de tiro único [`RecognizeOnceAsync`](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) usando:

```cpp
auto result = recognizer->RecognizeOnceAsync().get();
```

Terá de escrever um código para lidar com o resultado. Esta amostra [`result->Reason`](/cpp/cognitive-services/speech/recognitionresult#reason) avalia:

* Imprime o resultado do reconhecimento: `ResultReason::RecognizedSpeech`
* Se não houver correspondência de reconhecimento, informe o utilizador: `ResultReason::NoMatch`
* Se for encontrado um erro, imprima a mensagem de erro: `ResultReason::Canceled`

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

O reconhecimento contínuo é um pouco mais envolvido do que o reconhecimento de um único tiro. Requer que subscreva o `Recognizing` `Recognized` , e `Canceled` eventos para obter os resultados de reconhecimento. Para parar o reconhecimento, deve ligar [para StopContinuousRecognitionAsync](/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync). Aqui está um exemplo de como o reconhecimento contínuo é realizado num ficheiro de entrada de áudio.

Comecemos por definir a entrada e inicializar [`SpeechRecognizer`](/cpp/cognitive-services/speech/speechrecognizer) um:

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

Em seguida, vamos criar uma variável para gerir o estado do reconhecimento da fala. Para começar, vamos declarar `promise<void>` um, já que no início do reconhecimento podemos assumir com segurança que não está terminado.

```cpp
promise<void> recognitionEnd;
```

Subscreveremos os eventos enviados do [`SpeechRecognizer`](/cpp/cognitive-services/speech/speechrecognizer) .

* [`Recognizing`](/cpp/cognitive-services/speech/asyncrecognizer#recognizing): Sinal para eventos que contenham resultados intermédios de reconhecimento.
* [`Recognized`](/cpp/cognitive-services/speech/asyncrecognizer#recognized): Sinal para eventos que contenham resultados finais de reconhecimento (indicando uma tentativa de reconhecimento bem sucedida).
* [`SessionStopped`](/cpp/cognitive-services/speech/asyncrecognizer#sessionstopped): Sinal para eventos que indiquem o fim de uma sessão de reconhecimento (operação).
* [`Canceled`](/cpp/cognitive-services/speech/asyncrecognizer#canceled): Sinal para eventos que contenham resultados de reconhecimento cancelados (indicando uma tentativa de reconhecimento que foi cancelada em resultado ou um pedido de cancelamento direto ou, em alternativa, uma falha de transporte ou protocolo).

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

Com tudo preparado, podemos [`StopContinuousRecognitionAsync`](/cpp/cognitive-services/speech/speechrecognizer#startcontinuousrecognitionasync) ligar.

```cpp
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer->StartContinuousRecognitionAsync().get();

// Waits for recognition end.
recognitionEnd.get_future().get();

// Stops recognition.
recognizer->StopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>Modo de ditado

Ao utilizar o reconhecimento contínuo, pode ativar o processamento de ditados utilizando a função correspondente de "ativar o ditado". Este modo fará com que a instância config da fala interprete descrições de palavras de estruturas de frases como a pontuação. Por exemplo, a expressão "Vives no ponto de interrogação da cidade" seria interpretada como o texto "Vives na cidade?".

Para ativar o modo de ditado, utilize o [`EnableDictation`](/cpp/cognitive-services/speech/speechconfig#enabledictation) método no seu [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) .

```cpp
config->EnableDictation();
```

## <a name="change-source-language"></a>Alterar linguagem de origem

Uma tarefa comum para o reconhecimento da fala é especificar a linguagem de entrada (ou origem). Vamos ver como mudaria a língua de entrada para alemão. No seu código, encontre o [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) seu, em seguida, adicione esta linha diretamente abaixo.

```cpp
config->SetSpeechRecognitionLanguage("de-DE");
```

[`SetSpeechRecognitionLanguage`](/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage) é um parâmetro que toma uma corda como argumento. Pode fornecer qualquer valor na lista de [locais/idiomas suportados.](../../../language-support.md)

## <a name="improve-recognition-accuracy"></a>Melhorar a precisão do reconhecimento

As listas de frases são usadas para identificar frases conhecidas em dados áudio, como o nome de uma pessoa ou uma localização específica. Ao fornecer uma lista de frases, melhora a precisão do reconhecimento da fala.

Como exemplo, se tiver um comando "Move to" e um possível destino de "Ward" que possa ser falado, pode adicionar uma entrada de "Move to Ward". Adicionar uma frase aumentará a probabilidade de quando o áudio for reconhecido que "Move to Ward" será reconhecido em vez de "Mover-se para"

Palavras simples ou frases completas podem ser adicionadas a uma Lista de Frases. Durante o reconhecimento, uma entrada numa lista de frases é usada para impulsionar o reconhecimento das palavras e frases na lista, mesmo quando as entradas aparecem no meio da expressão. 

> [!IMPORTANT]
> A funcionalidade Lista de Frases está disponível nos seguintes idiomas: en-US, de-DE, en-AU, en-CA, en-GB, es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN

Para utilizar uma lista de frases, primeiro crie um [`PhraseListGrammar`](/cpp/cognitive-services/speech/phraselistgrammar) objeto e, em seguida, adicione palavras e frases específicas com [`AddPhrase`](/cpp/cognitive-services/speech/phraselistgrammar#addphrase) .

Quaisquer alterações que [`PhraseListGrammar`](/cpp/cognitive-services/speech/phraselistgrammar) entrem em vigor no próximo reconhecimento ou após uma reconexão ao serviço Discurso.

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

* [Melhorar a precisão com Discurso Personalizado](../../../custom-speech-overview.md)
* [Melhorar a precisão com modelos de inquilinos](../../../tutorial-tenant-model.md)
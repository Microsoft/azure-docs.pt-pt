---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 3ddd7b1139396a5952d1575ea72b00d5dfa14fab
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95097799"
---
Uma das características centrais do serviço de fala é a capacidade de reconhecer e transcrever a fala humana (muitas vezes referida como discurso-a-texto). Neste arranque rápido, aprende-se a usar o Speech SDK nas suas apps e produtos para realizar conversão de fala a texto de alta qualidade.

## <a name="skip-to-samples-on-github"></a>Salte para as amostras no GitHub

Se quiser saltar diretamente para o código de amostra, consulte as [amostras de arranque rápido C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que tem uma conta Azure e subscrição do serviço de fala. Se não tiver uma conta e subscrição, [experimente gratuitamente o serviço Desemação](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instale o SDK de discurso

Se só quer que o nome do pacote seja iniciado, corra `Install-Package Microsoft.CognitiveServices.Speech` na consola NuGet.

Para instruções de instalação específicas da plataforma, consulte os seguintes links:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET Core <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Unidade <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">UWP <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Criar uma configuração de discurso

Para ligar para o serviço de discurso usando o SDK de discurso, precisa de criar um [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) . Esta classe inclui informações sobre a sua subscrição, como a sua chave e região associada, ponto final, anfitrião ou token de autorização. Crie um [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) utilizando a sua chave e região. Consulte as [teclas e a](../../../overview.md#find-keys-and-region) página da região para encontrar o seu par de regiões-chave.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
    }
}
```

Há algumas outras formas de inicializar [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) uma:

* Com um ponto final: passe num ponto final de serviço de discurso. Uma chave ou sinal de autorização é opcional.
* Com um anfitrião: passe em um endereço de anfitrião. Uma chave ou sinal de autorização é opcional.
* Com um sinal de autorização: passe em um token de autorização e na região associada.

> [!NOTE]
> Independentemente de estar a realizar reconhecimento de voz, síntese de fala, tradução ou reconhecimento de intenções, irá sempre criar uma configuração.

## <a name="recognize-from-microphone"></a>Reconhecer a partir do microfone

Para reconhecer a fala utilizando o microfone do dispositivo, crie uma `AudioConfig` utilização `FromDefaultMicrophoneInput()` . Em seguida, inicialize [`SpeechRecognizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet) um, passando o seu `audioConfig` e `speechConfig` .

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task FromMic(SpeechConfig speechConfig)
    {
        using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
        using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        Console.WriteLine("Speak into your microphone.");
        var result = await recognizer.RecognizeOnceAsync();
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
    }

    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        await FromMic(speechConfig);
    }
}
```

Se pretender utilizar um dispositivo *específico* de entrada de áudio, tem de especificar o ID do dispositivo no `AudioConfig` . Saiba [como obter o ID do dispositivo](../../../how-to-select-audio-input-devices.md) para o seu dispositivo de entrada de áudio.

## <a name="recognize-from-file"></a>Reconhecer a partir de arquivo

Se quiser reconhecer a fala a partir de um ficheiro áudio em vez de um microfone, ainda precisa de criar um `AudioConfig` . No entanto, quando cria o [`AudioConfig`](/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet) , em vez de ligar - ligue e passe o caminho do `FromDefaultMicrophoneInput()` `FromWavFileInput()` ficheiro.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task FromFile(SpeechConfig speechConfig)
    {
        using var audioConfig = AudioConfig.FromWavFileInput("PathToFile.wav");
        using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        var result = await recognizer.RecognizeOnceAsync();
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
    }

    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        await FromFile(speechConfig);
    }
}
```

## <a name="recognize-from-in-memory-stream"></a>Reconhecer a partir do fluxo de memória

Para muitos casos de uso, é provável que os seus dados de áudio venham do armazenamento de bolhas, ou de outra forma já estejam na memória como uma `byte[]` estrutura de dados brutos ou similar. O exemplo a seguir usa um [`PushAudioInputStream`](/dotnet/api/microsoft.cognitiveservices.speech.audio.pushaudioinputstream?view=azure-dotnet) para reconhecer a fala, que é essencialmente um fluxo de memória abstrato. O código de amostra faz o seguinte:

* Escreve dados de áudio brutos (PCM) para a `PushAudioInputStream` utilização da `Write()` função, que aceita um `byte[]` .
* Lê um `.wav` ficheiro utilizando um para fins de `FileReader` demonstração, mas se já tiver dados áudio num `byte[]` , pode saltar diretamente para escrever o conteúdo para o fluxo de entrada.
* O formato padrão é de 16 bits, 16khz mono PCM. Para personalizar o formato, pode passar um [`AudioStreamFormat`](/dotnet/api/microsoft.cognitiveservices.speech.audio.audiostreamformat?view=azure-dotnet) objeto para utilizar a `CreatePushStream()` função estática `AudioStreamFormat.GetWaveFormatPCM(sampleRate, (byte)bitRate, (byte)channels)` .

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task FromStream(SpeechConfig speechConfig)
    {
        var reader = new BinaryReader(File.OpenRead("PathToFile.wav"));
        using var audioInputStream = AudioInputStream.CreatePushStream();
        using var audioConfig = AudioConfig.FromStreamInput(audioInputStream);
        using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        byte[] readBytes;
        do
        {
            readBytes = reader.ReadBytes(1024);
            audioInputStream.Write(readBytes, readBytes.Length);
        } while (readBytes.Length > 0);

        var result = await recognizer.RecognizeOnceAsync();
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
    }

    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        await FromStream(speechConfig);
    }
}
```

A utilização de um fluxo de pressão como entrada pressupõe que os dados de áudio são um PCM cru, por exemplo, saltar quaisquer cabeçalhos.
A API continuará a funcionar em certos casos se o cabeçalho não tiver sido ignorado, mas para que os melhores resultados considerem implementar lógica para ler os cabeçalhos para que o `byte[]` início *dos dados áudio* comece.

## <a name="error-handling"></a>Processamento de erros

Os exemplos anteriores simplesmente obtêm o texto reconhecido `result.text` de , mas para lidar com erros e outras respostas, terá de escrever algum código para lidar com o resultado. O seguinte código avalia o [`result.Reason`](/dotnet/api/microsoft.cognitiveservices.speech.recognitionresult.reason?preserve-view=true&view=azure-dotnet) imóvel e:

* Imprime o resultado do reconhecimento: `ResultReason.RecognizedSpeech`
* Se não houver correspondência de reconhecimento, informe o utilizador: `ResultReason.NoMatch`
* Se for encontrado um erro, imprima a mensagem de erro: `ResultReason.Canceled`

```csharp
switch (result.Reason)
{
    case ResultReason.RecognizedSpeech:
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
        break;
    case ResultReason.NoMatch:
        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        var cancellation = CancellationDetails.FromResult(result);
        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

        if (cancellation.Reason == CancellationReason.Error)
        {
            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
            Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
            Console.WriteLine($"CANCELED: Did you update the subscription info?");
        }
        break;
}
```

## <a name="continuous-recognition"></a>Reconhecimento contínuo

Os exemplos anteriores usam o reconhecimento de um único tiro, que reconhece uma única expressão. O fim de uma única expressão é determinado ouvindo o silêncio no final ou até que um máximo de 15 segundos de áudio seja processado.

Em contrapartida, o reconhecimento contínuo é utilizado quando se pretende **controlar** quando se deve deixar de reconhecer. Requer que subscreva o `Recognizing` `Recognized` , e `Canceled` eventos para obter os resultados de reconhecimento. Para parar o reconhecimento, tem de [`StopContinuousRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync?preserve-view=true&view=azure-dotnet) ligar. Aqui está um exemplo de como o reconhecimento contínuo é realizado num ficheiro de entrada de áudio.

Comece por definir a entrada e inicializar [`SpeechRecognizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?preserve-view=true&view=azure-dotnet) a:

```csharp
using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```

Em seguida, criar um `TaskCompletionSource<int>` para gerir o estado de reconhecimento da fala.

```csharp
var stopRecognition = new TaskCompletionSource<int>();
```

Em seguida, subscreva os eventos enviados do [`SpeechRecognizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet) .

* [`Recognizing`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizing?preserve-view=true&view=azure-dotnet): Sinal para eventos que contenham resultados intermédios de reconhecimento.
* [`Recognized`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognized?preserve-view=true&view=azure-dotnet): Sinal para eventos que contenham resultados finais de reconhecimento (indicando uma tentativa de reconhecimento bem sucedida).
* [`SessionStopped`](/dotnet/api/microsoft.cognitiveservices.speech.recognizer.sessionstopped?preserve-view=true&view=azure-dotnet): Sinal para eventos que indiquem o fim de uma sessão de reconhecimento (operação).
* [`Canceled`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.canceled?preserve-view=true&view=azure-dotnet): Sinal para eventos que contenham resultados de reconhecimento cancelados (indicando uma tentativa de reconhecimento que foi cancelada em resultado ou um pedido de cancelamento direto ou, em alternativa, uma falha de transporte ou protocolo).

```csharp
recognizer.Recognizing += (s, e) =>
{
    Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
};

recognizer.Recognized += (s, e) =>
{
    if (e.Result.Reason == ResultReason.RecognizedSpeech)
    {
        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
    }
    else if (e.Result.Reason == ResultReason.NoMatch)
    {
        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
    }
};

recognizer.Canceled += (s, e) =>
{
    Console.WriteLine($"CANCELED: Reason={e.Reason}");

    if (e.Reason == CancellationReason.Error)
    {
        Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
        Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
        Console.WriteLine($"CANCELED: Did you update the subscription info?");
    }

    stopRecognition.TrySetResult(0);
};

recognizer.SessionStopped += (s, e) =>
{
    Console.WriteLine("\n    Session stopped event.");
    stopRecognition.TrySetResult(0);
};
```

Com tudo preparado, ligue `StartContinuousRecognitionAsync` para começar a reconhecer.

```csharp
await recognizer.StartContinuousRecognitionAsync();

// Waits for completion. Use Task.WaitAny to keep the task rooted.
Task.WaitAny(new[] { stopRecognition.Task });

// make the following call at some point to stop recognition.
// await recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Modo de ditado

Ao utilizar o reconhecimento contínuo, pode ativar o processamento de ditados utilizando a função correspondente de "ativar o ditado". Este modo fará com que a instância config da fala interprete descrições de palavras de estruturas de frases como a pontuação. Por exemplo, a expressão "Vives no ponto de interrogação da cidade" seria interpretada como o texto "Vives na cidade?".

Para ativar o modo de ditado, utilize o [`EnableDictation`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?preserve-view=true&view=azure-dotnet) método no seu [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-dotnet) .

```csharp
speechConfig.EnableDictation();
```

## <a name="change-source-language"></a>Alterar linguagem de origem

Uma tarefa comum para o reconhecimento da fala é especificar a linguagem de entrada (ou origem). Vamos ver como mudaria a língua de entrada para italiana. No seu código, encontre o [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-dotnet) seu, em seguida, adicione esta linha diretamente abaixo.

```csharp
speechConfig.SpeechRecognitionLanguage = "it-IT";
```

A [`SpeechRecognitionLanguage`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage?preserve-view=true&view=azure-dotnet) propriedade espera uma cadeia de formato idioma-local. Pode fornecer qualquer valor na coluna **Locale** na lista de [locais/idiomas suportados.](../../../language-support.md)

## <a name="improve-recognition-accuracy"></a>Melhorar a precisão do reconhecimento

Existem algumas formas de melhorar a precisão do reconhecimento com o SDK do discurso. Vamos ver as Listas de Frases. As listas de frases são usadas para identificar frases conhecidas em dados áudio, como o nome de uma pessoa ou uma localização específica. Palavras simples ou frases completas podem ser adicionadas a uma Lista de Frases. Durante o reconhecimento, é utilizada uma entrada numa lista de frases se for incluída no áudio uma correspondência exata para toda a frase. Se não for encontrado um jogo exato com a frase, o reconhecimento não é assistido.

> [!IMPORTANT]
> A funcionalidade Lista de Frases só está disponível em inglês.

Para utilizar uma lista de frases, primeiro crie um [`PhraseListGrammar`](/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar?preserve-view=true&view=azure-dotnet) objeto e, em seguida, adicione palavras e frases específicas com [`AddPhrase`](/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar.addphrase?preserve-view=true&view=azure-dotnet) .

Quaisquer alterações que [`PhraseListGrammar`](/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar?preserve-view=true&view=azure-dotnet) entrem em vigor no próximo reconhecimento ou após uma reconexão ao serviço Discurso.

```csharp
var phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Supercalifragilisticexpialidocious");
```

Se precisar de limpar a sua lista de frases: 

```csharp
phraseList.Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Outras opções para melhorar a precisão do reconhecimento

As listas de frases são apenas uma opção para melhorar a precisão do reconhecimento. Também pode: 

* [Melhorar a precisão com Discurso Personalizado](../../../how-to-custom-speech.md)
* [Melhorar a precisão com modelos de inquilinos](../../../tutorial-tenant-model.md)
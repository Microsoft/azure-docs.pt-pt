---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 01/04/2021
ms.author: trbye
ms.openlocfilehash: 407906727332f3db8d3d0a6840d0c865c6b33ff7
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609523"
---
Em primeiro lugar, carregue o ficheiro do modelo de palavra-chave utilizando a `FromFile()` função estática, que devolve um `KeywordRecognitionModel` . Use o caminho para o `.table` ficheiro que descarregou do Speech Studio. Além disso, cria-se um `AudioConfig` microfone padrão e, em seguida, instantaneamente um novo `KeywordRecognizer` utilizando a configuração de áudio.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

Em seguida, executar o reconhecimento de palavras-chave é feito com uma chamada para `RecognizeOnceAsync()` passar o seu objeto modelo. Isto inicia uma sessão de reconhecimento de palavras-chave que dura até que a palavra-chave seja reconhecida. Assim, você geralmente usa este padrão de design em aplicações multi-roscadas, ou em casos em que você pode estar esperando por uma palavra de despertar indefinidamente.

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> O exemplo aqui mostrado utiliza o reconhecimento de palavras-chave locais, uma vez que não requer um `SpeechConfig` objeto para o contexto de autenticação, e não contacta o back-end. No entanto, pode executar reconhecimento e verificação de [palavras-chave utilizando uma ligação direta de back-end](../../../tutorial-voice-enable-your-bot-speech-sdk.md#view-the-source-code-that-enables-keyword).

### <a name="continuous-recognition"></a>Reconhecimento contínuo

Outras classes do Discurso SDK apoiam o reconhecimento contínuo (tanto para o reconhecimento da fala como para o reconhecimento de intenções) com reconhecimento de palavras-chave. Isto permite-lhe utilizar o mesmo código que normalmente utilizaria para reconhecimento contínuo, com a capacidade de fazer referência a um `.table` ficheiro para o seu modelo de palavra-chave.

Para falar em texto, siga o mesmo padrão de design mostrado no [quickstart](../../../get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=script%2cbrowser%2cwindowsinstall#continuous-recognition) para configurar o reconhecimento contínuo. Em seguida, substitua a chamada `recognizer.StartContinuousRecognitionAsync()` `recognizer.StartKeywordRecognitionAsync(KeywordRecognitionModel)` para, e passe o seu `KeywordRecognitionModel` objeto. Para parar o reconhecimento contínuo com o reconhecimento de palavras-chave, utilize `recognizer.StopKeywordRecognitionAsync()` em vez de `recognizer.StopContinuousRecognitionAsync()` .

O reconhecimento de intenções usa um padrão idêntico com as [`StartKeywordRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.startkeywordrecognitionasync#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StartKeywordRecognitionAsync_Microsoft_CognitiveServices_Speech_KeywordRecognitionModel_) [`StopKeywordRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.stopkeywordrecognitionasync#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StopKeywordRecognitionAsync) funções e as funções.

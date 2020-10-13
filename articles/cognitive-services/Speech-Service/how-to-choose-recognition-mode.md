---
title: Escolha um modo de reconhecimento de voz com o SDK de discurso
titleSuffix: Azure Cognitive Services
description: Saiba como escolher o melhor modo de reconhecimento ao utilizar o SDK do discurso.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two-with-js
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 1955e29eb1bef7fe6c8f57fa5d411c5c98faf58b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91322772"
---
# <a name="choose-a-speech-recognition-mode"></a>Escolha um modo de reconhecimento de voz

Ao considerar operações de reconhecimento de voz a texto, o [Speech SDK](speech-sdk.md) fornece vários modos para processar a fala. Conceptualmente, às vezes chamado de *modo de reconhecimento.* Este artigo compara os vários modos de reconhecimento.

## <a name="recognize-once"></a>Reconhecer uma vez

Se quiser processar cada expressão uma "frase" de cada vez, utilize a função "reconhecer uma vez". Este método detetará uma expressão reconhecida a partir da entrada a partir do início da fala detetada até à próxima pausa. Normalmente, uma pausa marca o fim de uma frase ou linha de pensamento.

No final de uma expressão reconhecida, o serviço deixa de processar áudio a partir desse pedido. O limite máximo para reconhecimento é uma duração de 20 segundos.

::: zone pivot="programming-language-csharp"

Para obter mais informações sobre a utilização da `RecognizeOnceAsync` função, consulte os [docs .NET Speech SDK](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechRecognizer_RecognizeOnceAsync).

```csharp
var result = await recognizer.RecognizeOnceAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Para obter mais informações sobre a utilização da `RecognizeOnceAsync` função, consulte os [docs SDK de discurso C++](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizeonceasync).

```cpp
auto result = recognize->RecognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

Para obter mais informações sobre a utilização da `recognizeOnceAsync` função, consulte os [docs Java Speech SDK](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechRecognizer.recognizeOnceAsync?view=azure-java-stable).

```java
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

Para obter mais informações sobre a utilização da `recognize_once` função, consulte os [docs Python Speech SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python#recognize-once------azure-cognitiveservices-speech-speechrecognitionresult).

```python
result = speech_recognizer.recognize_once()
```

::: zone-end

::: zone pivot="programming-language-javascript"

Para obter mais informações sobre a utilização da `recognizeOnceAsync` função, consulte o [SDK de voz para docs JavaScript](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-).

```JavaScript
recognizer.recognizeOnceAsync((result)=>{}, (error)=>{}));
```

::: zone-end

::: zone pivot="programming-language-more"

Para mais línguas, consulte os [documentos de referência do SDK do discurso](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="continuous"></a>Contínuo

Se necessitar de reconhecimento de longa duração, utilize as funções de paragem inicial e correspondentes para reconhecimento contínuo. A função inicial iniciará e continuará a processar todas as expressões até invocar a função stop, ou até que tenha passado demasiado tempo em silêncio. Ao utilizar o modo contínuo, certifique-se de que se regista nos vários eventos que irão disparar após a ocorrência. Por exemplo, o evento "reconhecido" dispara quando ocorre o reconhecimento da fala. É preciso ter um manipulador de eventos no local para lidar com o reconhecimento.

::: zone pivot="programming-language-csharp"

```csharp
// Subscribe to event
recognizer.Recognized += (s, e) => 
{
    if (e.Result.Reason == ResultReason.RecognizedSpeech)
    {
        // Do something with the recognized text
        // e.Result.Text
    }
};

// Start continuous speech recognition
await recognizer.StartContinuousRecognitionAsync();

// Stop continuous speech recognition
await recognizer.StopContinuousRecognitionAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

```cpp
// Connect to event
recognizer->Recognized.Connect([] (const SpeechRecognitionEventArgs& e)
{
    if (e.Result->Reason == ResultReason::RecognizedSpeech)
    {
        // Do something with the recognized text
        // e.Result->Text
    }
});

// Start continuous speech recognition
recognizer->StartContinuousRecognitionAsync().get();

// Stop continuous speech recognition
recognizer->StopContinuousRecognitionAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

```java
recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        // Do something with the recognized text
        // e.getResult().getText()
    }
});

// Start continuous speech recognition
recognizer.startContinuousRecognitionAsync().get();

// Stop continuous speech recognition
recognizer.stopContinuousRecognitionAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

```python
def recognized_cb(evt):
    if evt.result.reason == speechsdk.ResultReason.RecognizedSpeech:
        # Do something with the recognized text
        # evt.result.text

speech_recognizer.recognized.connect(recognized_cb)

# Start continuous speech recognition
speech_recognizer.start_continuous_recognition()

# Stop continuous speech recognition
speech_recognizer.stop_continuous_recognition()
```

::: zone-end

::: zone pivot="programming-language-javascript"

```JavaScript
recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        // Do something with the recognized text
        // e.getResult().getText()
    }
});

// Start continuous speech recognition
recognizer.startContinuousRecognitionAsync(()=>{}, (error)=>{});

// Stop continuous speech recognition
recognizer.stopContinuousRecognitionAsync(()=>{}, (error)=>{});
```

::: zone-end

::: zone pivot="programming-language-more"

Para mais línguas, consulte os [documentos de referência do SDK do discurso](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="dictation"></a>Ditado

Ao utilizar o reconhecimento contínuo, pode ativar o processamento de ditados utilizando a função correspondente de "ativar o ditado". Este modo fará com que a instância config da fala interprete descrições de palavras de estruturas de frases como a pontuação. Por exemplo, a expressão "Vives no ponto de interrogação da cidade" seria interpretada como o texto "Vives na cidade?".

::: zone pivot="programming-language-csharp"

Para obter mais informações sobre a utilização da `EnableDictation` função, consulte os [docs .NET Speech SDK](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechConfig_EnableDictation).

```csharp
// Enable diction
SpeechConfig.EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Para obter mais informações sobre a utilização da `EnableDictation` função, consulte os [docs SDK de discurso C++](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation).

```cpp
// Enable diction
SpeechConfig->EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-java"

Para obter mais informações sobre a utilização da `enableDictation` função, consulte os [docs Java Speech SDK](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechConfig.enableDictation?view=azure-java-stable).

```java
// Enable diction
SpeechConfig.enableDictation();
```

::: zone-end
::: zone pivot="programming-language-python"

Para obter mais informações sobre a utilização da `enable_dictation` função, consulte os [docs Python Speech SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--).

```python
# Enable diction
SpeechConfig.enable_dictation()
```

::: zone-end

::: zone pivot="programming-language-javascript"

Para obter mais informações sobre a utilização da `enableDictation` função, consulte o [SDK de voz para docs JavaScript](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#enabledictation--).

```JavaScript
// Enable diction
speechConfig.enableDictation();
```

::: zone-end

::: zone pivot="programming-language-more"

Para mais línguas, consulte os [documentos de referência do SDK do discurso](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore amostras adicionais de SDK de discurso no GitHub](https://aka.ms/csspeech/samples)

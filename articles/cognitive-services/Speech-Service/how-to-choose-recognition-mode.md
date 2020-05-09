---
title: Escolha um modo de reconhecimento de voz com o SDK da fala
titleSuffix: Azure Cognitive Services
description: Aprenda a escolher o melhor modo de reconhecimento ao utilizar o SDK do Discurso.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: dapine
zone_pivot_groups: programming-languages-set-two-with-js
ms.openlocfilehash: 0c0c57c27689da7df23285c9740665f811f71fd5
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82977571"
---
# <a name="choose-a-speech-recognition-mode"></a>Escolha um modo de reconhecimento de voz

Ao considerar as operações de reconhecimento de discurso sms, o [SDK](speech-sdk.md) do Discurso fornece vários modos para processar a fala. Conceptualmente, às vezes chamado de modo de *reconhecimento.* Este artigo compara os vários modos de reconhecimento.

## <a name="recognize-once"></a>Reconhecer uma vez

Se quiser processar cada expressão uma "frase" de cada vez, use a função "reconhecer uma vez". Este método detetará uma expressão reconhecida a partir da entrada a partir do início da fala detetada até à próxima pausa. Normalmente, uma pausa marca o fim de uma frase ou linha de pensamento.

No final de uma expressão reconhecida, o serviço deixa de processar áudio a partir desse pedido. O limite máximo para o reconhecimento é uma duração de 20 segundos.

::: zone pivot="programming-language-csharp"

Para obter mais `RecognizeOnceAsync` informações sobre a utilização da função, consulte os [docs .NET Speech SDK](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechRecognizer_RecognizeOnceAsync).

```csharp
var result = await recognizer.RecognizeOnceAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Para obter mais `RecognizeOnceAsync` informações sobre a utilização da função, consulte os [docs C++ Speech SDK](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizeonceasync).

```cpp
auto result = recognize->RecognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

Para obter mais `recognizeOnceAsync` informações sobre a utilização da função, consulte os [docs Java Speech SDK](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechRecognizer.recognizeOnceAsync?view=azure-java-stable).

```java
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

Para obter mais `recognize_once` informações sobre a utilização da função, consulte os [docs python Speech SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python#recognize-once------azure-cognitiveservices-speech-speechrecognitionresult).

```python
result = speech_recognizer.recognize_once()
```

::: zone-end

::: zone pivot="programming-language-javascript"

Para obter mais `recognizeOnceAsync` informações sobre a utilização da função, consulte os [docs JavaScript Speech SDK](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-).

```JavaScript
recognizer.recognizeOnceAsync((result)=>{}, (error)=>{}));
```

::: zone-end

::: zone pivot="programming-language-more"

Para obter idiomas adicionais, consulte os [docs de referência do Speech SDK](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="continuous"></a>Contínuo

Se necessitar de reconhecimento de longa duração, utilize as funções de arranque e paragem correspondentes para um reconhecimento contínuo. A função de início começará e continuará a processar todas as expressões até que invoque a função stop, ou até que passado demasiado tempo em silêncio. Ao utilizar o modo contínuo, certifique-se de que se regista nos vários eventos que irão disparar após a ocorrência. Por exemplo, o evento "reconhecido" dispara quando ocorre o reconhecimento da fala. Precisa de um manipulador de eventos para lidar com o reconhecimento.

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

Para obter idiomas adicionais, consulte os [docs de referência do Speech SDK](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="dictation"></a>Ditado

Ao utilizar um reconhecimento contínuo, pode ativar o processamento de ditados utilizando a função correspondente de "ativar o ditado". Este modo fará com que a instância de config da fala interprete descrições de palavras de estruturas de frases como pontuação. Por exemplo, a expressão "Vives no ponto de interrogação da cidade" seria interpretada como o texto "Vives na cidade?".

::: zone pivot="programming-language-csharp"

Para obter mais `EnableDictation` informações sobre a utilização da função, consulte os [docs .NET Speech SDK](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechConfig_EnableDictation).

```csharp
// Enable diction
SpeechConfig.EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-cpp"

Para obter mais `EnableDictation` informações sobre a utilização da função, consulte os [docs C++ Speech SDK](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation).

```cpp
// Enable diction
SpeechConfig->EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-java"

Para obter mais `enableDictation` informações sobre a utilização da função, consulte os [docs Java Speech SDK](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechConfig.enableDictation?view=azure-java-stable).

```java
// Enable diction
SpeechConfig.enableDictation();
```

::: zone-end
::: zone pivot="programming-language-python"

Para obter mais `enable_dictation` informações sobre a utilização da função, consulte os [docs python Speech SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--).

```python
# Enable diction
SpeechConfig.enable_dictation()
```

::: zone-end

::: zone pivot="programming-language-javascript"

Para obter mais `enableDictation` informações sobre a utilização da função, consulte os [docs JavaScript Speech SDK](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#enabledictation--).

```JavaScript
// Enable diction
speechConfig.enableDictation();
```

::: zone-end

::: zone pivot="programming-language-more"

Para obter idiomas adicionais, consulte os [docs de referência do Speech SDK](speech-to-text.md#speech-sdk-reference-docs).

::: zone-end

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore amostras adicionais de SDK de Discurso no GitHub](https://aka.ms/csspeech/samples)

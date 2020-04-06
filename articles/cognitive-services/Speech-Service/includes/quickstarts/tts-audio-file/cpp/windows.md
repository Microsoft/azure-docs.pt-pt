---
title: 'Quickstart: Sintetizar o discurso em ficheiro áudio, C++ (Windows) - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: wolfma
ms.openlocfilehash: d9ddc288c7e6aeaba2a239c4148bcd4360647075
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671626"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Criar um recurso de fala azure](../../../../get-started.md)
> * [Crie o seu ambiente de desenvolvimento e crie um projeto vazio](../../../../quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp)

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra o ficheiro de origem **helloworld.cpp**.

1. Substitua todo o código pelo seguinte corte:

   ```C++

    // Creates an instance of a speech config with specified subscription key and service region.
    // Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // Creates a speech synthesizer using file as audio output.
    // Replace with your own audio file name.
    auto fileName = "helloworld.wav";
    auto fileOutput = AudioConfig::FromWavFileOutput(fileName);
    auto synthesizer = SpeechSynthesizer::FromConfig(config, fileOutput);

    // Converts the specified text to speech, saving the audio data in the file specified above.
    // Replace with your own text.
    auto text = "Hello world!";
    auto result = synthesizer->SpeakTextAsync(text).get();

    // Checks result for successful completion or errors.
    if (result->Reason == ResultReason::SynthesizingAudioCompleted)
    {
        cout << "Speech synthesized to [" << fileName << "] for text [" << text << "]" << std::endl;
    }
    else if (result->Reason == ResultReason::Canceled)
    {
        auto cancellation = SpeechSynthesisCancellationDetails::FromResult(result);
        cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;

        if (cancellation->Reason == CancellationReason::Error)
        {
            cout << "CANCELED: ErrorCode=" << (int)cancellation->ErrorCode << std::endl;
            cout << "CANCELED: ErrorDetails=[" << cancellation->ErrorDetails << "]" << std::endl;
            cout << "CANCELED: Did you update the subscription info?" << std::endl;
        }
    }

   ```

1. No mesmo ficheiro, substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.

1. Substitua a cadeia de carateres `YourServiceRegion` pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

1. Substitua `helloworld.wav` a cadeia pelo seu próprio nome de ficheiro.

1. A partir da barra de menus, escolha **File** > **Save All**.

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

1. A partir da barra de menus, selecione **Build Build** > **Solution** para construir a aplicação. Agora o código deverá ser compilado sem erros.

1. Escolha **Debug** > **Start Debugging** (ou prima **F5)** para iniciar a aplicação **Helloworld.**

1. O seu texto é convertido em fala e guardado nos dados áudio especificados.

   ```text
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Consulte também

- [Criar uma voz personalizada](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Gravar amostras de voz personalizadas](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)

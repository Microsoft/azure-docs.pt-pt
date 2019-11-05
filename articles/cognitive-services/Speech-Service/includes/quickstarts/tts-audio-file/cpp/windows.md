---
title: 'Início rápido: sintetizar a fala no arquivo C++ de áudio, (Windows)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: wolfma
ms.openlocfilehash: 0f166fedd1044ba3b7076f3fb7eb14becbc1272b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503137"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Criar um recurso de fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=windows)
> * [Criar um projeto de exemplo vazio](../../../../quickstarts/create-project.md?tabs=windows)

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra o ficheiro de origem **helloworld.cpp**.

1. Substitua todo o código pelo trecho a seguir:

   ```C++

    // Creates an instance of a speech config with specified subscription key and service region.
    // Replace with your own subscription key and service region (e.g., "westus").
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

1. Substitua a cadeia de caracteres `helloworld.wav` pelo seu próprio nome de arquivo.

1. Na barra de menus, escolha **arquivo** > **salvar tudo**.

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

1. Na barra de menus, selecione **compilar** > **Compilar solução** para compilar o aplicativo. Agora o código deverá ser compilado sem erros.

1. Escolha **depurar** > **Iniciar Depuração** (ou pressione **F5**) para iniciar o aplicativo **HelloWorld** .

1. O texto é convertido em fala e salvo nos dados de áudio especificados.

   ```text
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Consultar também

- [Criar uma voz personalizada](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Registrar amostras de voz personalizadas](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)

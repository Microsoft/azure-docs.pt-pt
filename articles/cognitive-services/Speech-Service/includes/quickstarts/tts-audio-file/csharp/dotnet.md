---
title: 'Quickstart: Sintetizar o C# discurso em ficheiro áudio, (.NET) - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 4ccc68b38d98c332435e252877d258c8591aab8a
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78924744"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Criar um recurso de fala azure](../../../../get-started.md)
> * [Crie o seu ambiente de desenvolvimento e crie um projeto vazio](../../../../quickstarts/setup-platform.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>Abra o seu projeto no Estúdio Visual

O primeiro passo é garantir que tem o seu projeto aberto no Estúdio Visual.

1. Lançar O Estúdio Visual 2019.
2. Carregue o seu projeto e abra `Program.cs`.

## <a name="start-with-some-boilerplate-code"></a>Comece com um pouco de código de placa de caldeira

Vamos adicionar um código que funciona como um esqueleto para o nosso projeto. Tome nota de que criou um método de asincronização chamado `SynthesisToAudioFileAsync()`.

````C#

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace helloworld
{
    class Program
    {
        public static async Task SynthesisToAudioFileAsync()
        {
        }

        static void Main()
        {
            SynthesisToAudioFileAsync().Wait();
        }
    }
}

````

## <a name="create-a-speech-configuration"></a>Criar uma configuração de Discurso

Antes de poder inicializar um `SpeechSynthesizer` objeto, precisa de criar uma configuração que utilize a sua chave de subscrição e região de subscrição. Insira este código no método `SynthesisToAudioFileAsync()`.

````C#
// Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
// The default language is "en-us".
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
````

## <a name="create-an-audio-configuration"></a>Criar uma configuração áudio

Agora, tens de criar um objeto ````AudioConfig```` que aponte para o teu ficheiro áudio. Este objeto é criado dentro de uma declaração de utilização para garantir a libertação adequada de recursos não geridos. Insira este código no método `SynthesisToAudioFileAsync()`, logo abaixo da configuração do Discurso.

````C#
var fileName = "helloworld.wav";
using (var fileOutput = AudioConfig.FromWavFileOutput(fileName))
{
}
````

## <a name="initialize-a-speechsynthesizer"></a>Inicializar um SpeechSynthesizer

Agora, vamos criar o `SpeechSynthesizer` objeto usando os objetos `SpeechConfig` e `AudioConfig` criados anteriormente. Este objeto também é criado dentro de uma declaração de utilização para garantir a libertação adequada de recursos não geridos. Insira este código no método `SynthesisToAudioFileAsync()`, dentro da declaração de utilização que envolve o seu objeto ````AudioConfig````.

````C#
using (var synthesizer = new SpeechSynthesizer(config, fileOutput))
{
}
````

## <a name="synthesize-text-using-speaktextasync"></a>Texto sintetizador usando SpeakTextAsync

Pelo `SpeechSynthesizer` objeto, vai chamar o método `SpeakTextAsync()`. Este método envia o seu texto para o serviço da Fala que o converte em áudio. O `SpeechSynthesizer` usará a voz predefinida se `config.VoiceName` não for explicitamente especificado.

Dentro da declaração de utilização, adicione este código:
````C#
var text = "Hello world!";
var result = await synthesizer.SpeakTextAsync(text);
````

## <a name="check-for-errors"></a>Verifique se há erros

Quando o resultado da síntese for devolvido pelo serviço de Fala, deve verificar se o seu texto foi sintetizado com sucesso.

Dentro da declaração de utilização, abaixo `SpeakTextAsync()`, adicione este código:
````C#
if (result.Reason == ResultReason.SynthesizingAudioCompleted)
{
    Console.WriteLine($"Speech synthesized to [{fileName}] for text [{text}]");
}
else if (result.Reason == ResultReason.Canceled)
{
    var cancellation = SpeechSynthesisCancellationDetails.FromResult(result);
    Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

    if (cancellation.Reason == CancellationReason.Error)
    {
        Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
        Console.WriteLine($"CANCELED: ErrorDetails=[{cancellation.ErrorDetails}]");
        Console.WriteLine($"CANCELED: Did you update the subscription info?");
    }
}
````

## <a name="check-your-code"></a>Verifique o seu código

Neste ponto, o seu código deve ser assim:

````C#
//
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE.md file in the project root for full license information.
//

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace helloworld
{
    class Program
    {
        public static async Task SynthesisToAudioFileAsync()
        {
            var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

            var fileName = "helloworld.wav";
            using (var fileOutput = AudioConfig.FromWavFileOutput(fileName))
            {
                using (var synthesizer = new SpeechSynthesizer(config, fileOutput))
                {
                    var text = "Hello world!";
                    var result = await synthesizer.SpeakTextAsync(text);

                    if (result.Reason == ResultReason.SynthesizingAudioCompleted)
                    {
                        Console.WriteLine($"Speech synthesized to [{fileName}] for text [{text}]");
                    }
                    else if (result.Reason == ResultReason.Canceled)
                    {
                        var cancellation = SpeechSynthesisCancellationDetails.FromResult(result);
                        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

                        if (cancellation.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails=[{cancellation.ErrorDetails}]");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        }
                    }
                }
            }
        }

        static void Main()
        {
            SynthesisToAudioFileAsync().Wait();
        }
    }
}
````

## <a name="build-and-run-your-app"></a>Construa e execute a sua app

Agora está pronto para construir a sua app e testar a nossa síntese de discurso usando o serviço de Fala.

1. **Compile o código** - A partir da barra de menu sécpor do Estúdio Visual, escolha **Build** > **Build Solution**.
2. **Inicie a sua aplicação** - A partir da barra de menus, escolha **Debug** > **Começar dedepuração** ou prima **F5**.
3. **Iniciar a síntese** - O seu texto é convertido em fala e guardado nos dados áudio especificados.

   ```text
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Consulte também

- [Criar uma voz personalizada](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Gravar amostras de voz personalizadas](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)

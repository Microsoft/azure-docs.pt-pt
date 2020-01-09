---
title: 'Início rápido: sintetizar a fala no arquivo C# de áudio, (.net)-serviço de fala'
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
ms.openlocfilehash: dfe2d5925e19697a2e3ed4491958da1325df5574
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468576"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Criar um recurso de fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Criar um projeto de exemplo vazio](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>Abra seu projeto no Visual Studio

A primeira etapa é certificar-se de que seu projeto esteja aberto no Visual Studio.

1. Inicie o Visual Studio 2019.
2. Carregue seu projeto e abra `Program.cs`.

## <a name="start-with-some-boilerplate-code"></a>Comece com algum código clichê

Vamos adicionar um código que funciona como um esqueleto para nosso projeto. Lembre-se de que você criou um método assíncrono chamado `SynthesisToAudioFileAsync()`.

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

## <a name="create-a-speech-configuration"></a>Criar uma configuração de fala

Antes de inicializar um objeto `SpeechSynthesizer`, você precisa criar uma configuração que usa sua chave de assinatura e sua região de assinatura. Insira esse código no método `SynthesisToAudioFileAsync()`.

````C#
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
````

## <a name="create-an-audio-configuration"></a>Criar uma configuração de áudio

Agora, você precisa criar um objeto ````AudioConfig```` que aponte para o arquivo de áudio. Esse objeto é criado dentro de uma instrução using para garantir a liberação adequada de recursos não gerenciados. Insira esse código no método `SynthesisToAudioFileAsync()`, logo abaixo da sua configuração de fala.

````C#
var fileName = "helloworld.wav";
using (var fileOutput = AudioConfig.FromWavFileOutput(fileName))
{
}
````

## <a name="initialize-a-speechsynthesizer"></a>Inicializar um SpeechSynthesizer

Agora, vamos criar o objeto `SpeechSynthesizer` usando os objetos `SpeechConfig` e `AudioConfig` criados anteriormente. Esse objeto também é criado dentro de uma instrução using para garantir a liberação adequada de recursos não gerenciados. Insira esse código no método `SynthesisToAudioFileAsync()`, dentro da instrução using que encapsula o objeto ````AudioConfig````.

````C#
using (var synthesizer = new SpeechSynthesizer(config, fileOutput))
{
}
````

## <a name="synthesize-text-using-speaktextasync"></a>Sintetizar texto usando SpeakTextAsync

A partir do objeto `SpeechSynthesizer`, você chamará o método `SpeakTextAsync()`. Esse método envia o texto para o serviço de fala que o converte em áudio. O `SpeechSynthesizer` usará a voz padrão se `config.VoiceName` não for especificado explicitamente.

Dentro da instrução using, adicione este código:
````C#
var text = "Hello world!";
var result = await synthesizer.SpeakTextAsync(text);
````

## <a name="check-for-errors"></a>Verificar se há erros

Quando o resultado da síntese é retornado pelo serviço de fala, você deve verificar se o texto foi sintetizado com êxito.

Dentro da instrução using, abaixo `SpeakTextAsync()`, adicione este código:
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

## <a name="check-your-code"></a>Verifique seu código

Neste ponto, seu código deve ter a seguinte aparência:

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

## <a name="build-and-run-your-app"></a>Compilar e executar seu aplicativo

Agora você está pronto para criar seu aplicativo e testar a síntese de fala usando o serviço de fala.

1. **Compile o código** -na barra de menus do Visual Stuio, escolha **Compilar** > **Compilar solução**.
2. **Inicie seu aplicativo** -na barra de menus, escolha **depurar** > **Iniciar Depuração** ou pressione **F5**.
3. **Iniciar síntese** -o texto é convertido em fala e salvo nos dados de áudio especificados.

   ```text
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Ver também

- [Criar uma voz personalizada](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Registrar amostras de voz personalizadas](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)

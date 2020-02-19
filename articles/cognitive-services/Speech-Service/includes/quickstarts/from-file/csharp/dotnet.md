---
title: 'Quickstart: Reconhecer o discurso C# de um ficheiro áudio, (.NET) - Serviço de fala'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: dapine
ms.openlocfilehash: 7bac943a4550103a941fbd812381e34ab2de2454
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77446522"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Criar um recurso azure speech](../../../../get-started.md)
> * [Configurar o seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Criar um projeto de amostra vazia](../../../../quickstarts/create-project.md?tabs=vs)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="open-your-project-in-visual-studio"></a>Abra o seu projeto no Estúdio Visual

O primeiro passo é garantir que tem o seu projeto aberto no Estúdio Visual.

1. Lançar O Estúdio Visual 2019.
2. Carregue o seu projeto e abra `Program.cs`.
3. Descarregue o <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike.wav <span class="docon docon-download x-hidden-focus"></span> </a> e adicione-o ao seu projeto.
    - Guarde o ficheiro *whatstheweatherlike.wav* ao lado do ficheiro `Program.cs`.
    - A partir do clique direito do **Solution Explorer** no projeto, selecione **Add > item existente**.
    - Selecione o ficheiro *whatstheweatherlike.wav* e, em seguida, selecione o botão **Adicionar.**
    - Clique à direita no ficheiro recém-adicionado, selecione **Propriedades**.
    - Mude sempre a **Cópia para O Diretório** de Saída para **Copiar**.

## <a name="start-with-some-boilerplate-code"></a>Comece com um pouco de código de placa de caldeira

Vamos adicionar um código que funciona como um esqueleto para o nosso projeto. Tome nota de que criou um método de asincronização chamado `RecognizeSpeechAsync()`.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

namespace HelloWorld
{
    class Program
    {
        static async Task Main()
        {
            await RecognizeSpeechAsync();
        }

        static async Task RecognizeSpeechAsync()
        {
        }
    }
}
```

## <a name="create-a-speech-configuration"></a>Criar uma configuração de Discurso

Antes de poder inicializar um `SpeechRecognizer` objeto, precisa de criar uma configuração que utilize a sua chave de subscrição e região de subscrição. Insira este código no método `RecognizeSpeechAsync()`.

> [!NOTE]
> Esta amostra utiliza o método `FromSubscription()` para construir a `SpeechConfig`. Para obter uma lista completa dos métodos disponíveis, consulte a [Aula de SpeechConfig](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).
> O SDK do Discurso não irá reconhecer o uso de en-us para a língua, consulte [especificar a linguagem fonte para a fala a texto](../../../../how-to-specify-source-language.md) para obter informações sobre a escolha da língua de origem.

```csharp
// Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="create-an-audio-configuration"></a>Criar uma configuração áudio

Agora, tens de criar um objeto `AudioConfig` que aponte para o teu ficheiro áudio. Este objeto é criado dentro de uma declaração de utilização para garantir a libertação adequada de recursos não geridos. Insira este código no método `RecognizeSpeechAsync()`, logo abaixo da configuração do Discurso.

```csharp
using (var audioInput = AudioConfig.FromWavFileInput("whatstheweatherlike.wav"))
{
}
```

## <a name="initialize-a-speechrecognizer"></a>Inicializar um Reconhecedor de Discursos

Agora, vamos criar o `SpeechRecognizer` objeto usando os objetos `SpeechConfig` e `AudioConfig` criados anteriormente. Este objeto também é criado dentro de uma declaração de utilização para garantir a libertação adequada de recursos não geridos. Insira este código no método `RecognizeSpeechAsync()`, dentro da declaração de utilização que envolve o seu objeto ```AudioConfig```.

```csharp
using (var recognizer = new SpeechRecognizer(config, audioInput))
{
}
```

## <a name="recognize-a-phrase"></a>Reconhecer uma frase

Pelo `SpeechRecognizer` objeto, vai chamar o método `RecognizeOnceAsync()`. Este método permite ao serviço da Fala saber que está a enviar uma única frase para reconhecimento, e que assim que a frase é identificada para parar de reconhecer o discurso.

Dentro da declaração de utilização, adicione este código:

```csharp
Console.WriteLine("Recognizing first result...");
var result = await recognizer.RecognizeOnceAsync();
```

## <a name="display-the-recognition-results-or-errors"></a>Mostrar os resultados do reconhecimento (ou erros)

Quando o resultado do reconhecimento for devolvido pelo serviço de Discurso, vai querer fazer algo com ele. Vamos mantê-lo simples e imprimir o resultado para consolar.

Dentro da declaração de utilização, abaixo `RecognizeOnceAsync()`, adicione este código:

```csharp
switch (result.Reason)
{
    case ResultReason.RecognizedSpeech:
        Console.WriteLine($"We recognized: {result.Text}");
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

## <a name="check-your-code"></a>Verifique o seu código

Neste ponto, o seu código deve ser assim:

```csharp
//
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE.md file in the project root for full license information.
//

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

namespace HelloWorld
{
    class Program
    {
        static async Task Main()
        {
            await RecognizeSpeechAsync();
        }

        static async Task RecognizeSpeechAsync()
        {
            var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

            using (var audioInput = AudioConfig.FromWavFileInput("whatstheweatherlike.wav"))
            using (var recognizer = new SpeechRecognizer(config, audioInput))
            {
                Console.WriteLine("Recognizing first result...");
                var result = await recognizer.RecognizeOnceAsync();

                switch (result.Reason)
                {
                    case ResultReason.RecognizedSpeech:
                        Console.WriteLine($"We recognized: {result.Text}");
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
            }
        }
    }
}
```

## <a name="build-and-run-your-app"></a>Construa e execute a sua app

Agora está pronto para construir a sua app e testar o nosso reconhecimento de voz usando o serviço de Discurso.

1. Compile o código: A partir da barra de menus do *Estúdio Visual,* escolha **Construir** > **Build Solution**.
2. Inicie a sua aplicação: A partir da barra de menus, escolha **Debug** > **Começar dedepuração** ou prima **F5**.
3. Reconhecimento inicial: O seu ficheiro áudio é enviado para o serviço Dafala, transcrito como texto e renderizado na consola.

   ```console
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]

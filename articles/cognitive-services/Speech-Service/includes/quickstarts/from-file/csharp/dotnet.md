---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: 4db81bcb65077de8cb923117905daebd80532685
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376839"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Criar um recurso de discurso azul](../../../../overview.md#try-the-speech-service-for-free)
> * [Configurar o seu ambiente de desenvolvimento e criar um projeto vazio](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="open-your-project-in-visual-studio"></a>Abra o seu projeto no Visual Studio

O primeiro passo é garantir que o seu projeto está aberto no Visual Studio.

1. Lançamento Visual Studio 2019.
2. Carregue o seu projeto e `Program.cs` abra.
3. Descarregue o <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike.wav <span class="docon docon-download x-hidden-focus"></span> </a> e adicione-o ao seu projeto.
    - Guarde o ficheiro *whatstheweatherlike.wav* ao lado do `Program.cs` ficheiro.
    - A partir do clique direito do **Solution Explorer** no projeto, selecione Adicionar > **item existente**.
    - Selecione o ficheiro *whatstheweatherlike.wav* e, em seguida, selecione o botão **Adicionar.**
    - Clique com o botão direito no ficheiro recém-adicionado, selecione **Propriedades**.
    - Altere sempre o **Copy para O Diretório de Saída** para **Copiar**.

## <a name="start-with-some-boilerplate-code"></a>Comece com um código de placa de caldeira

Vamos adicionar um código que funcione como um esqueleto para o nosso projeto. Note que criou um método async chamado `RecognizeSpeechAsync()` .

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

## <a name="create-a-speech-configuration"></a>Criar uma configuração de discurso

Antes de poder inicializar um `SpeechRecognizer` objeto, precisa de criar uma configuração que utilize a sua chave de subscrição e região de subscrição. Insira este código no `RecognizeSpeechAsync()` método.

> [!NOTE]
> Esta amostra utiliza o `FromSubscription()` método para construir o `SpeechConfig` . Para obter uma lista completa dos métodos disponíveis, consulte [a Aula deConfig da Fala](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).
> O SDK de discurso não reconhecerá a utilização do linguístico para a língua, consulte especificar a [língua de origem para falar para texto](../../../../how-to-specify-source-language.md) para obter informações sobre a escolha da língua de origem.

```csharp
// Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="create-an-audio-configuration"></a>Criar uma configuração áudio

Agora, tens de criar um `AudioConfig` objeto que aponte para o teu ficheiro áudio. Este objeto é criado dentro de uma declaração de utilização para garantir a libertação adequada de recursos não geridos. Insira este código no `RecognizeSpeechAsync()` método, logo abaixo da configuração do Discurso.

```csharp
using (var audioInput = AudioConfig.FromWavFileInput("whatstheweatherlike.wav"))
{
}
```

## <a name="initialize-a-speechrecognizer"></a>Inicializar um SpeechRecognizer

Agora, vamos criar o `SpeechRecognizer` objeto usando os `SpeechConfig` `AudioConfig` objetos e objetos criados anteriormente. Este objeto também é criado dentro de uma declaração de utilização para garantir a libertação adequada de recursos não geridos. Insira este código no `RecognizeSpeechAsync()` método, dentro da declaração de utilização que envolve o ```AudioConfig``` seu objeto.

```csharp
using (var recognizer = new SpeechRecognizer(config, audioInput))
{
}
```

## <a name="recognize-a-phrase"></a>Reconhecer uma frase

Pelo `SpeechRecognizer` objeto, vais chamar o `RecognizeOnceAsync()` método. Este método permite ao serviço de Discurso saber que está a enviar uma única frase para reconhecimento, e que uma vez que a frase é identificada para parar de reconhecer a fala.

Dentro da declaração de utilização, adicione este código:

```csharp
Console.WriteLine("Recognizing first result...");
var result = await recognizer.RecognizeOnceAsync();
```

## <a name="display-the-recognition-results-or-errors"></a>Mostrar os resultados do reconhecimento (ou erros)

Quando o resultado do reconhecimento for devolvido pelo serviço de Discurso, vai querer fazer algo com ele. Vamos mantê-lo simples e imprimir o resultado para consolar.

Dentro da declaração de utilização, `RecognizeOnceAsync()` abaixo, adicione este código:

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

Agora está pronto para construir a sua app e testar o nosso reconhecimento de voz usando o serviço Speech.

1. Compilar o código: A partir da barra de menus do *Estúdio Visual,* escolha **Build**  >  **Build Solution**.
2. Inicie a sua aplicação: A partir da barra de menus, escolha **Debug**  >  **Start Debugging** ou prima **F5**.
3. Reconhecimento de início: O seu ficheiro áudio é enviado para o serviço Deacebordo, transcrito como texto e renderizado na consola.

   ```console
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 63fc699cfd2f02a322f71324519b2f0b5b22234b
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88926471"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Criar um recurso de discurso azul <span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Configurar o seu ambiente de desenvolvimento e criar um projeto vazio](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp)
> * Certifique-se de que tem acesso a um microfone para captura de áudio

## <a name="open-your-project-in-visual-studio"></a>Abra o seu projeto no Visual Studio

O primeiro passo é garantir que o seu projeto está aberto no Visual Studio.

1. Lançamento **Visual Studio 2019**.
2. Carregue o seu projeto e abra *Program.cs.*

## <a name="source-code"></a>Código de origem

Substitua o conteúdo do ficheiro *Program.cs* pelo seguinte código C#.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace Speech.Recognition
{
    class Program
    {
        static async Task Main()
        {
            await RecognizeSpeechAsync();

            Console.WriteLine("Please press any key to continue...");
            Console.ReadLine();
        }

        static async Task RecognizeSpeechAsync()
        {
            var config =
                SpeechConfig.FromSubscription(
                    "YourSubscriptionKey",
                    "YourServiceRegion");

            using var recognizer = new SpeechRecognizer(config);
            
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
```

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Explicação de código

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>Construir e executar aplicativo

Agora está pronto para reconstruir a sua app e testar a funcionalidade de reconhecimento de voz utilizando o serviço Speech.

1. **Compilar o código** - A partir da barra de menus do Estúdio Visual, escolha **Build**  >  **Build Solution**.
2. **Inicie a sua aplicação** - A partir da barra de menus, escolha **Debug**  >  **Start Debugging** ou prima <kbd>F5</kbd>.
3. **Iniciar o reconhecimento** - Vai levá-lo a falar uma frase em inglês. O seu discurso é enviado para o serviço de discurso, transcrito como texto, e renderizado na consola.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

---
title: 'Início rápido: reconhecer a fala de um C# microfone, (.net)-serviço de fala'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: erhopf
ms.openlocfilehash: f3b0df9f663866c916a45c85767d49b8701152cd
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2020
ms.locfileid: "75927884"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * [Criar um recurso de fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Criar um projeto de exemplo vazio](../../../../quickstarts/create-project.md?tabs=dotnet)
> * Verifique se você tem acesso a um microfone para captura de áudio

## <a name="open-your-project-in-visual-studio"></a>Abra seu projeto no Visual Studio

A primeira etapa é certificar-se de que seu projeto esteja aberto no Visual Studio.

1. Inicie o Visual Studio 2019.
2. Carregue seu projeto e abra `Program.cs`.

## <a name="start-with-some-boilerplate-code"></a>Comece com algum código clichê

Vamos adicionar um código que funciona como um esqueleto para nosso projeto. Lembre-se de que você criou um método assíncrono chamado `RecognizeSpeechAsync()`.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=5-15,43-52)]

## <a name="create-a-speech-configuration"></a>Criar uma configuração de fala

Antes de inicializar um objeto `SpeechRecognizer`, você precisa criar uma configuração que usa sua chave de assinatura e sua região de assinatura. Insira esse código no método `RecognizeSpeechAsync()`.

> [!NOTE]
> Este exemplo usa o método `FromSubscription()` para criar o `SpeechConfig`. Para obter uma lista completa dos métodos disponíveis, consulte [classe SpeechConfig](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=16)]
> O SDK de fala usará como padrão o reconhecimento do uso de en-US para a linguagem, consulte [especificar o idioma de origem de fala para texto](../../../../how-to-specify-source-language.md) para obter informações sobre como escolher o idioma de origem.

## <a name="initialize-a-speechrecognizer"></a>Inicializar um SpeechRecognizer

Agora, vamos criar um `SpeechRecognizer`. Esse objeto é criado dentro de uma instrução using para garantir a liberação adequada de recursos não gerenciados. Insira esse código no método `RecognizeSpeechAsync()`, logo abaixo da sua configuração de fala.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=17-19,42)]

## <a name="recognize-a-phrase"></a>Reconhecer uma frase

A partir do objeto `SpeechRecognizer`, você chamará o método `RecognizeOnceAsync()`. Esse método permite que o serviço de fala saiba que você está enviando uma única frase para reconhecimento e que, depois que a frase for identificada para parar de reconhecer a fala.

Dentro da instrução using, adicione este código: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=20)]

## <a name="display-the-recognition-results-or-errors"></a>Exibir os resultados de reconhecimento (ou erros)

Quando o resultado do reconhecimento for retornado pelo serviço de fala, você desejará fazer algo com ele. Vamos mantê-lo simples e imprimir o resultado no console.

Dentro da instrução using, abaixo `RecognizeOnceAsync()`, adicione este código: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=22-41)]

## <a name="check-your-code"></a>Verifique seu código

Neste ponto, o código deve ser assim: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs)]

## <a name="build-and-run-your-app"></a>Compilar e executar seu aplicativo

Agora você está pronto para criar seu aplicativo e testar nosso reconhecimento de fala usando o serviço de fala.

1. **Compilar o código** -na barra de menus do Visual Studio, escolha **Compilar** > **Compilar solução**.
2. **Inicie seu aplicativo** -na barra de menus, escolha **depurar** > **Iniciar Depuração** ou pressione **F5**.
3. **Iniciar reconhecimento** -ele solicitará que você fale uma frase em inglês. Sua fala é enviada ao serviço de fala, transcrita como texto e renderizada no console do.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]

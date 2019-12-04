---
title: 'Início rápido: reconhecer fala, intenções e entidades, C# -serviço de fala'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 84b1712ac89751ca8eb5b1161221f511cf699374
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795892"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
>
> * [Criar um recurso de fala do Azure](../../../../get-started.md)
> * [Criar um aplicativo Reconhecimento vocal (LUIS) e obter uma chave de ponto de extremidade](../../../../quickstarts/create-luis.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Criar um projeto de exemplo vazio](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>Abra seu projeto no Visual Studio

A primeira etapa é certificar-se de que seu projeto esteja aberto no Visual Studio.

1. Inicie o Visual Studio 2019.
2. Carregue seu projeto e abra `Program.cs`.

## <a name="start-with-some-boilerplate-code"></a>Comece com algum código clichê

Vamos adicionar um código que funciona como um esqueleto para nosso projeto. Lembre-se de que você criou um método assíncrono chamado `RecognizeIntentAsync()`.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-17,77-86)]

## <a name="create-a-speech-configuration"></a>Criar uma configuração de fala

Antes de inicializar um objeto `IntentRecognizer`, você precisa criar uma configuração que usa sua chave e região de ponto de extremidade LUIS. Insira esse código no método `RecognizeIntentAsync()`.

Este exemplo usa o método `FromSubscription()` para criar o `SpeechConfig`. Para obter uma lista completa dos métodos disponíveis, consulte [classe SpeechConfig](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).
O SDK de fala usará como padrão o reconhecimento do uso de en-US para a linguagem, consulte [especificar o idioma de origem de fala para texto](../../../../how-to-specify-source-language.md) para obter informações sobre como escolher o idioma de origem.

> [!NOTE]
> É importante usar a chave de ponto de extremidade LUIS e não as chaves de início ou de criação, já que apenas a chave do ponto de extremidade é válida para reconhecimento de fala e intenção. Consulte [criar um aplicativo Luis e obter uma chave de ponto de extremidade](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md) para obter instruções sobre como obter a chave correta.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=26)]

## <a name="initialize-an-intentrecognizer"></a>Inicializar um IntentRecognizer

Agora, vamos criar um `IntentRecognizer`. Esse objeto é criado dentro de uma instrução using para garantir a liberação adequada de recursos não gerenciados. Insira esse código no método `RecognizeIntentAsync()`, logo abaixo da sua configuração de fala.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=28-30,76)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Adicionar uma LanguageUnderstandingModel e tentativas

Agora você precisa associar um `LanguageUnderstandingModel` ao reconhecedor de intenção e adicionar as tentativas que você deseja que sejam reconhecidos.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=31-35)]

## <a name="recognize-an-intent"></a>Reconhecer uma intenção

A partir do objeto `IntentRecognizer`, você chamará o método `RecognizeOnceAsync()`. Esse método permite que o serviço de fala saiba que você está enviando uma única frase para reconhecimento e que, depois que a frase for identificada para parar de reconhecer a fala.

Dentro da instrução using, adicione este código: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=46)]

## <a name="display-the-recognition-results-or-errors"></a>Exibir os resultados de reconhecimento (ou erros)

Quando o resultado do reconhecimento for retornado pelo serviço de fala, você desejará fazer algo com ele. Vamos mantê-lo simples e imprimir o resultado no console.

Dentro da instrução using, abaixo `RecognizeOnceAsync()`, adicione este código: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=48-75)]

## <a name="check-your-code"></a>Verifique seu código

Neste ponto, seu código deve ter a seguinte aparência:  
(Adicionamos alguns comentários a esta versão) [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=5-86)]

## <a name="build-and-run-your-app"></a>Compilar e executar seu aplicativo

Agora você está pronto para criar seu aplicativo e testar nosso reconhecimento de fala usando o serviço de fala.

1. **Compilar o código** -na barra de menus do Visual Studio, escolha **Compilar** > **Compilar solução**.
2. **Inicie seu aplicativo** -na barra de menus, escolha **depurar** > **Iniciar Depuração** ou pressione **F5**.
3. **Iniciar reconhecimento** -ele solicitará que você fale uma frase em inglês. Sua fala é enviada ao serviço de fala, transcrita como texto e renderizada no console do.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]

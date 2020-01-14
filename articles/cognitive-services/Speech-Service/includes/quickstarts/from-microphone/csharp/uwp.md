---
title: 'Início rápido: reconhecer a fala de um C# microfone, (UWP)-serviço de fala'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: erhopf
ms.openlocfilehash: e6934e908111773cef3f7ddcfc2a971d8e1706bf
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2020
ms.locfileid: "75927857"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * [Criar um recurso de fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=uwp)
> * [Criar um projeto de exemplo vazio](../../../../quickstarts/create-project.md?tabs=uwp)
> * Verifique se você tem acesso a um microfone para captura de áudio

Se você já fez isso, ótimo. Vamos continuar.

## <a name="open-your-project-in-visual-studio"></a>Abra seu projeto no Visual Studio

A primeira etapa é certificar-se de que seu projeto esteja aberto no Visual Studio.

## <a name="start-with-some-boilerplate-code"></a>Comece com algum código clichê

Vamos adicionar um código que funciona como um esqueleto para nosso projeto.

1. Em **Gerenciador de soluções**, abra `MainPage.xaml`.

2. Na exibição XAML do designer, insira o seguinte trecho XAML na marca de **grade** (entre `<Grid>` e `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml#StackPanel)]

3. No **Gerenciador de soluções**, abra o arquivo de origem code-behind `MainPage.xaml.cs`. (Está agrupado em `MainPage.xaml`.)

4. Substitua o código pelo seguinte código base:

   [!code-csharp[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=6-50,55-56,94-154)]

## <a name="create-a-speech-configuration"></a>Criar uma configuração de fala

Antes de inicializar um objeto `SpeechRecognizer`, você precisa criar uma configuração que usa sua chave de assinatura e sua região de assinatura. Insira esse código no método `RecognizeSpeechAsync()`.

> [!NOTE]
> Este exemplo usa o método `FromSubscription()` para criar o `SpeechConfig`. Para obter uma lista completa dos métodos disponíveis, consulte [classe SpeechConfig](https://docs.microsoft.com/dotnet/api/) [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=51-53)]

## <a name="initialize-a-speechrecognizer"></a>Inicializar um SpeechRecognizer

Agora, vamos criar um `SpeechRecognizer`. Esse objeto é criado dentro de uma instrução using para garantir a liberação adequada de recursos não gerenciados. Insira esse código no método `RecognizeSpeechAsync()`, logo abaixo da sua configuração de fala.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=58,59,93)]

## <a name="recognize-a-phrase"></a>Reconhecer uma frase

A partir do objeto `SpeechRecognizer`, você chamará o método `RecognizeOnceAsync()`. Esse método permite que o serviço de fala saiba que você está enviando uma única frase para reconhecimento e que, depois que a frase for identificada para parar de reconhecer a fala.

Dentro da instrução using, adicione este código: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=66)]

## <a name="display-the-recognition-results-or-errors"></a>Exibir os resultados de reconhecimento (ou erros)

Quando o resultado do reconhecimento for retornado pelo serviço de fala, você desejará fazer algo com ele. Vamos mantê-lo simples e imprimir o resultado no painel de status.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=68-93)]

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

Agora você está pronto para criar e testar seu aplicativo.

1. Na barra de menus, escolha **compilar** > **Compilar solução** para compilar o aplicativo. Agora o código deverá ser compilado sem erros.

1. Escolha **depurar** > **Iniciar Depuração** (ou pressione **F5**) para iniciar o aplicativo. A janela **HelloWorld** é exibida.

   ![Aplicativo de reconhecimento de fala de C# exemplo UWP no início rápido](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-helloworld-window.png)

1. Selecione **habilitar microfone**e, quando a solicitação de permissão de acesso for exibida, selecione **Sim**.

   ![Solicitação de permissão de acesso ao microfone](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Selecione **Reconhecimento de voz com entrada do microfone** e fale uma frase ou um trecho em inglês no microfone do dispositivo. A sua voz é transmitida ao serviço de Voz e convertida para texto que é apresentado na janela.

   ![Interface do usuário de reconhecimento de fala](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]

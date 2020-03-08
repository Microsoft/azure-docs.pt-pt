---
title: 'Quickstart: Reconheça o discurso C# de um microfone, (UWP) - Serviço de fala'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: erhopf
ms.openlocfilehash: 447b42fe3fce3baa5b50f8333892a39464b7ee3f
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78668960"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * [Criar um recurso de fala azure](../../../../get-started.md)
> * [Configurar o seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=uwp)
> * [Criar um projeto de amostra vazia](../../../../quickstarts/create-project.md?tabs=uwp)
> * Certifique-se de que tem acesso a um microfone para captura de áudio

Se já fez isto, ótimo. Vamos continuar.

## <a name="open-your-project-in-visual-studio"></a>Abra o seu projeto no Estúdio Visual

O primeiro passo é garantir que tem o seu projeto aberto no Estúdio Visual.

## <a name="start-with-some-boilerplate-code"></a>Comece com um pouco de código de placa de caldeira

Vamos adicionar um código que funciona como um esqueleto para o nosso projeto.

1. No **Solution Explorer,** abra `MainPage.xaml`.

2. Na vista XAML do designer, insira o seguinte corte XAML na etiqueta **Grid** (entre `<Grid>` e `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml#StackPanel)]

3. No **Solution Explorer,** abra o ficheiro fonte por trás do código `MainPage.xaml.cs`. (Está agrupado sob `MainPage.xaml`.)

4. Substitua o código pelo seguinte código base:

   [!code-csharp[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=6-50,55-56,94-154)]

## <a name="create-a-speech-configuration"></a>Criar uma configuração de Discurso

Antes de poder inicializar um `SpeechRecognizer` objeto, precisa de criar uma configuração que utilize a sua chave de subscrição e região de subscrição. Insira este código no método `RecognizeSpeechAsync()`.

> [!NOTE]
> Esta amostra utiliza o método `FromSubscription()` para construir a `SpeechConfig`. Para obter uma lista completa dos métodos disponíveis, consulte [A Classe SpeechConfig](https://docs.microsoft.com/dotnet/api/) [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=51-53)]

## <a name="initialize-a-speechrecognizer"></a>Inicializar um Reconhecedor de Discursos

Agora, vamos criar um `SpeechRecognizer`. Este objeto é criado dentro de uma declaração de utilização para garantir a libertação adequada de recursos não geridos. Insira este código no método `RecognizeSpeechAsync()`, logo abaixo da configuração do Discurso.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=58,59,93)]

## <a name="recognize-a-phrase"></a>Reconhecer uma frase

Pelo `SpeechRecognizer` objeto, vai chamar o método `RecognizeOnceAsync()`. Este método permite ao serviço da Fala saber que está a enviar uma única frase para reconhecimento, e que assim que a frase é identificada para parar de reconhecer o discurso.

Dentro da declaração de utilização, adicione este código.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=66)]

## <a name="display-the-recognition-results-or-errors"></a>Mostrar os resultados do reconhecimento (ou erros)

Quando o resultado do reconhecimento for devolvido pelo serviço de Discurso, vai querer fazer algo com ele. Vamos mantê-lo simples e imprimir o resultado para o painel de estado.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=68-93)]

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

Agora está pronto para construir e testar a sua aplicação.

1. A partir da barra de menus, escolha **construir** > **Construir Solução** para construir a aplicação. Agora o código deverá ser compilado sem erros.

1. Escolha **depurar** > **Iniciar depuração** (ou prima **F5)** para iniciar a aplicação. A janela do **Helloworld** aparece.

   ![Amostra UWP aplicação C# de reconhecimento de voz em - quickstart](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-helloworld-window.png)

1. Selecione **Ativar o microfone**e quando o pedido de autorização de acesso aparecer, selecione **Sim**.

   ![Pedido de permissão de acesso ao microfone](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Selecione **Reconhecimento de voz com entrada do microfone** e fale uma frase ou um trecho em inglês no microfone do dispositivo. A sua voz é transmitida ao serviço de Voz e convertida para texto que é apresentado na janela.

   ![Interface de utilizador de reconhecimento de voz](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]

---
title: 'Quickstart: Reconhecer discurso a partir de um microfone, C# (UWP) - Serviço de fala'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.openlocfilehash: 9a3f478604547819162ad13d39a9383263321857
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671265"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * [Criar um recurso de fala azure](../../../../get-started.md)
> * [Crie o seu ambiente de desenvolvimento e crie um projeto vazio](../../../../quickstarts/setup-platform.md?tabs=uwp&pivots=programming-language-csharp)
> * Certifique-se de que tem acesso a um microfone para captura de áudio

Se já fez isto, ótimo. Vamos continuar.

## <a name="open-your-project-in-visual-studio"></a>Abra o seu projeto no Estúdio Visual

O primeiro passo é garantir que tem o seu projeto aberto no Estúdio Visual.

## <a name="start-with-some-boilerplate-code"></a>Comece com um pouco de código de placa de caldeira

Vamos adicionar um código que funciona como um esqueleto para o nosso projeto.

1. No **Solution Explorer,** abra. `MainPage.xaml`

2. Na vista XAML do designer, insira o seguinte corte XAML `</Grid>`na etiqueta **Grid** (entre `<Grid>` e):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml#StackPanel)]

3. No **Solution Explorer,** abra o `MainPage.xaml.cs`ficheiro código por trás da fonte . (Está agrupado sob `MainPage.xaml`.)

4. Substitua o código pelo seguinte código base:

   [!code-csharp[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=6-50,55-56,94-154)]

## <a name="create-a-speech-configuration"></a>Criar uma configuração de Discurso

Antes de poder `SpeechRecognizer` inicializar um objeto, precisa de criar uma configuração que utilize a sua chave de subscrição e região de subscrição. Insira este `RecognizeSpeechAsync()` código no método.

> [!NOTE]
> Esta amostra `FromSubscription()` utiliza o `SpeechConfig`método para construir o . Para obter uma lista completa dos métodos disponíveis, consulte [A Aula de SpeechConfig](https://docs.microsoft.com/dotnet/api/)[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=51-53)]

## <a name="initialize-a-speechrecognizer"></a>Inicializar um Reconhecedor de Discursos

Agora, vamos criar `SpeechRecognizer`um. Este objeto é criado dentro de uma declaração de utilização para garantir a libertação adequada de recursos não geridos. Insira este `RecognizeSpeechAsync()` código no método, logo abaixo da configuração do Discurso.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=58,59,93)]

## <a name="recognize-a-phrase"></a>Reconhecer uma frase

Pelo `SpeechRecognizer` objeto, vais chamar o `RecognizeOnceAsync()` método. Este método permite ao serviço da Fala saber que está a enviar uma única frase para reconhecimento, e que assim que a frase é identificada para parar de reconhecer o discurso.

Dentro da declaração de utilização, adicione este código.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=66)]

## <a name="display-the-recognition-results-or-errors"></a>Mostrar os resultados do reconhecimento (ou erros)

Quando o resultado do reconhecimento for devolvido pelo serviço de Discurso, vai querer fazer algo com ele. Vamos mantê-lo simples e imprimir o resultado para o painel de estado.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=68-93)]

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

Agora está pronto para construir e testar a sua aplicação.

1. A partir da barra de menus, escolha **Build Build** > **Solution** para construir a aplicação. Agora o código deverá ser compilado sem erros.

1. Escolha **Depurar** > **Depuração** (ou prima **F5)** para iniciar a aplicação. A janela do **Helloworld** aparece.

   ![Amostra UWP aplicação de reconhecimento de voz em C# - quickstart](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-helloworld-window.png)

1. Selecione **Ativar o microfone**e quando o pedido de autorização de acesso aparecer, selecione **Sim**.

   ![Pedido de permissão de acesso ao microfone](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Selecione **Reconhecimento de voz com entrada do microfone** e fale uma frase ou um trecho em inglês no microfone do dispositivo. A sua voz é transmitida ao serviço de Voz e convertida para texto que é apresentado na janela.

   ![Interface de utilizador de reconhecimento de voz](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](../footer.md)]

---
title: 'Quickstart: Discurso sintetizador, C# (UWP) - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, vai criar um C# aplicação plataforma Universal do Windows (UWP) com o SDK de voz dos serviços cognitivos. Sintetiza o discurso do texto em tempo real para o altifalante do seu dispositivo. A aplicação é construída com o Pacote NuGet Speech SDK e o Microsoft Visual Studio 2019.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/19/2019
ms.author: yinhew
ms.openlocfilehash: 8ca97be2863bd8e45ac7937c49c464fa2f216b11
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925915"
---
> [!NOTE]
> A Plataforma Universal do Windows permite-lhe desenvolver aplicações que são executadas em qualquer dispositivo que suporte o Windows 10, incluindo PCs, Xbox, Surface Hub e outros dispositivos.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Criar um recurso de fala azure](../../../../get-started.md)
> * [Crie o seu ambiente de desenvolvimento e crie um projeto vazio](../../../../quickstarts/setup-platform.md?tabs=uwp)

## <a name="add-sample-code"></a>Adicionar código de exemplo

Adicione agora o código XAML que define a interface C# do utilizador da aplicação e adicione a implementação por detrás de código.

1. No **Solution Explorer,** abra `MainPage.xaml`.

1. Na vista XAML do designer, insira o seguinte corte XAML na etiqueta **Grid** (entre `<Grid>` e `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml#StackPanel)]

1. No **Solution Explorer,** abra o ficheiro fonte por trás do código `MainPage.xaml.cs`. (Está agrupado sob `MainPage.xaml`.)

1. Substitua todo o código nele pelo seguinte corte:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml.cs#code)]

1. No manipulador de `Speak_ButtonClicked` do ficheiro fonte, encontre o fio `YourSubscriptionKey`e substitua-o pela chave de subscrição.

1. No manipulador de `Speak_ButtonClicked`, encontre a cadeia `YourServiceRegion`, e substitua-a pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à sua subscrição. (Por exemplo, utilize `westus` para a subscrição de teste gratuito.)

1. A partir da barra de menus, escolha **File** > **Save All** para guardar as suas alterações.

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

Agora está pronto para construir e testar a sua aplicação.

1. A partir da barra de menus, escolha **construir** > **Construir Solução** para construir a aplicação. Agora o código deverá ser compilado sem erros.

1. Escolha **depurar** > **Iniciar depuração** (ou prima **F5)** para iniciar a aplicação. A janela do **Helloworld** aparece.

   ![Amostra UWP aplicação C# de síntese de fala em - quickstart](~/articles/cognitive-services/Speech-Service/media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. Introduza algum texto na caixa de texto e clique em **Falar**. O seu texto é transmitido ao serviço da Fala e sintetizado para a fala, que reproduz no seu orador.

    ![Interface de utilizador da síntese da fala](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Consulte também

- [Criar uma voz personalizada](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Gravar amostras de voz personalizadas](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)

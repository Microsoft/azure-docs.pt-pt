---
title: 'Quickstart: Discurso sintetizador, C# (UWP) - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, vai criar uma aplicação de Plataforma Universal do Windows (UWP) em C# com o SDK de Voz dos Serviços Cognitivos. Sintetiza o discurso do texto em tempo real para o altifalante do seu dispositivo. A aplicação é construída com o Pacote NuGet Speech SDK e o Microsoft Visual Studio 2019.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/19/2019
ms.author: yinhew
ms.openlocfilehash: 8ca97be2863bd8e45ac7937c49c464fa2f216b11
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
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

Adicione agora o código XAML que define a interface de utilizador da aplicação e adicione a implementação de código C# por trás.

1. No **Solution Explorer,** abra. `MainPage.xaml`

1. Na vista XAML do designer, insira o seguinte corte XAML `</Grid>`na etiqueta **Grid** (entre `<Grid>` e):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml#StackPanel)]

1. No **Solution Explorer,** abra o `MainPage.xaml.cs`ficheiro código por trás da fonte . (Está agrupado sob `MainPage.xaml`.)

1. Substitua todo o código nele pelo seguinte corte:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml.cs#code)]

1. No manipulador do `Speak_ButtonClicked` ficheiro fonte, `YourSubscriptionKey`encontre a corda e substitua-a pela chave de subscrição.

1. No `Speak_ButtonClicked` manipulador, encontre `YourServiceRegion`a corda e substitua-a pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à sua subscrição. (Por exemplo, `westus` utilização para a subscrição de teste gratuito.)

1. A partir da barra de menus, escolha **Guardar Ficheiros** > **para** guardar as suas alterações.

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

Agora está pronto para construir e testar a sua aplicação.

1. A partir da barra de menus, escolha **Build Build** > **Solution** para construir a aplicação. Agora o código deverá ser compilado sem erros.

1. Escolha **Depurar** > **Depuração** (ou prima **F5)** para iniciar a aplicação. A janela do **Helloworld** aparece.

   ![Amostra UWP aplicação de síntese de fala em C# - quickstart](~/articles/cognitive-services/Speech-Service/media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. Introduza algum texto na caixa de texto e clique em **Falar**. O seu texto é transmitido ao serviço da Fala e sintetizado para a fala, que reproduz no seu orador.

    ![Interface de utilizador da síntese da fala](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Consulte também

- [Criar uma voz personalizada](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Gravar amostras de voz personalizadas](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)

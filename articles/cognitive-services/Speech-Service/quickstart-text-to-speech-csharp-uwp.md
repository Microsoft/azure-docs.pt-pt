---
title: 'Início rápido: Sintetização de fala C# , (UWP)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, vai criar um C# aplicação plataforma Universal do Windows (UWP) com o SDK de voz dos serviços cognitivos. Você sintetiza a fala de texto em tempo real para o palestrante do dispositivo. O aplicativo é criado com o pacote NuGet do SDK de fala e Microsoft Visual Studio 2019.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: yinhew
ms.openlocfilehash: 65b65c9af377b6a9951f9f328e0732850d3b9c1d
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382229"
---
# <a name="quickstart-synthesize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Início rápido: Sintetizar a fala em um aplicativo UWP usando o SDK de fala

Os guias de início rápido também estão disponíveis para [reconhecimento de fala](quickstart-csharp-uwp.md), [tradução de fala](quickstart-translate-speech-uwp.md)e [assistente virtual de voz primeiro](quickstart-virtual-assistant-csharp-uwp.md).

Neste artigo, você desenvolverá um C# aplicativo plataforma universal do Windows (UWP) usando o [SDK de fala](speech-sdk.md)dos serviços cognitivas. O programa sintetiza a fala de texto em tempo real para o palestrante do dispositivo. Você cria o aplicativo usando o [pacote NuGet do SDK de fala](https://aka.ms/csspeech/nuget) e Microsoft Visual Studio 2019 (qualquer edição).

> [!NOTE]
> A Plataforma Universal do Windows permite-lhe desenvolver aplicações que são executadas em qualquer dispositivo que suporte o Windows 10, incluindo PCs, Xbox, Surface Hub e outros dispositivos.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Uma chave de assinatura do Azure para o serviço de fala. [Obtenha um gratuitamente](get-started.md).

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

Agora, adicione o código XAML que define a interface do usuário do aplicativo e adicione a C# implementação code-behind.

1. Em **Gerenciador de soluções**, abra `MainPage.xaml`.

1. Na exibição XAML do designer, insira o seguinte trecho XAML na marca de **grade** (entre `<Grid>` e `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Em **Gerenciador de soluções**, abra o arquivo `MainPage.xaml.cs`de origem code-behind. (Está agrupado em `MainPage.xaml`.)

1. Substitua todo o código nele pelo seguinte trecho:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. No manipulador do `Speak_ButtonClicked` arquivo de origem, localize a cadeia `YourSubscriptionKey`de caracteres e substitua-a pela sua chave de assinatura.

1. No manipulador, localize a cadeia de `YourServiceRegion`caracteres e substitua-a pela [região](regions.md) associada à sua assinatura. `Speak_ButtonClicked` (Por exemplo, use `westus` para a assinatura de avaliação gratuita.)

1. Na barra de menus, escolha **arquivo** > **salvar tudo** para salvar suas alterações.

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

Agora você está pronto para criar e testar seu aplicativo.

1. Na barra de menus, escolha **Compilar** > compilar**solução** para compilar o aplicativo. Agora o código deverá ser compilado sem erros.

1. Escolha **depurar** > **Iniciar Depuração** (ou pressione **F5**) para iniciar o aplicativo. A janela **HelloWorld** é exibida.

   ![Aplicativo de síntese de fala de C# exemplo UWP no início rápido](media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. Insira um texto na caixa e clique em **fala**. Seu texto é transmitido para os serviços de fala e sintetizado para fala, que é reproduzido no seu palestrante.

    ![Interface do usuário de síntese de fala](media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explorar C# exemplos no github](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Criar e usar modelos de voz personalizados](how-to-custom-voice-create-voice.md)
- [Gravar amostras de voz](record-custom-voice-samples.md)

---
title: 'Início rápido: Sintetização de fala C# , (UWP)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, vai criar um C# aplicação plataforma Universal do Windows (UWP) com o SDK de voz dos serviços cognitivos. Você sintetiza a fala de texto em tempo real para o palestrante do dispositivo. A aplicação baseia-se com o pacote de NuGet do SDK de voz e o Microsoft Visual Studio 2017.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: 3d5bc1e9cc27fd3c40c15ba55f577779c65e6851
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559327"
---
# <a name="quickstart-synthesize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Início rápido: Sintetizar a fala em um aplicativo UWP usando o SDK de fala

Os guias de início rápido também estão disponíveis para [reconhecimento de fala](quickstart-csharp-uwp.md), [voz-tradução](quickstart-translate-speech-uwp.md) e [assistente virtual de voz-primeiro](quickstart-virtual-assistant-csharp-uwp.md).

Neste artigo, você desenvolverá um C# plataforma universal do Windows (UWP; Windows versão 1709 mais recente) usando o [SDK de fala](speech-sdk.md)dos serviços cognitivas. O programa irá sintetizar a fala de texto em tempo real para o palestrante do dispositivo. A aplicação é compilada com o [Pacote NuGet de SDK de Voz](https://aka.ms/csspeech/nuget) e o Microsoft Visual Studio 2017 (qualquer edição).

> [!NOTE]
> A Plataforma Universal do Windows permite-lhe desenvolver aplicações que são executadas em qualquer dispositivo que suporte o Windows 10, incluindo PCs, Xbox, Surface Hub e outros dispositivos.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Uma chave de assinatura do Azure para o serviço de fala. [Obtenha um gratuitamente](get-started.md).

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. A interface do utilizador da aplicação é definida com o XAML. Abra `MainPage.xaml` no Explorador de Soluções. Na vista XAML do designer, insira o seguinte fragmento de código XAML na etiqueta da Grelha (entre `<Grid>` e `</Grid>`).

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Abra o ficheiro de origem do code-behind `MainPage.xaml.cs` (encontre-o agrupado sob `MainPage.xaml`). Substitua todo o código no mesmo pelo seguinte.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. No processador `YourSubscriptionKey` neste ficheiro, substitua a cadeia de carateres `Speak_ButtonClicked` pela sua chave de subscrição.

1. No processador `westus`, substitua também a cadeia de carateres `Speak_ButtonClicked` pela `YourServiceRegion`região[ associada à subscrição (por exemplo, ](regions.md) para a subscrição de avaliação gratuita).

1. Guarde todas as alterações no projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

1. Compile a aplicação. Na barra de menus, selecione **Compilar** > **Compilar Solução**. Agora o código deverá ser compilado sem erros.

    ![Captura de ecrã da aplicação Visual Studio, com a opção Compilar Solução realçada](media/sdk/qs-csharp-uwp-08-build.png "Compilação bem-sucedida")

1. Inicie a aplicação. Na barra de menus, selecione **Depurar** > **Iniciar Depuração** ou prima **F5**.

    ![Captura de ecrã da aplicação Visual Studio, com a opção Iniciar Depuração realçada](media/sdk/qs-csharp-uwp-09-start-debugging.png "Iniciar a depuração da aplicação")

1. Insira um texto na caixa e clique em **fala**. Seu texto é transmitido para os serviços de fala e sintetizado para fala, que é reproduzido no seu palestrante.

    ![Captura de tela da interface do usuário de síntese de fala](media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Explorar C# exemplos no github](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar fontes de voz](how-to-customize-voice-font.md)
- [Gravar amostras de voz](record-custom-voice-samples.md)

---
title: 'Início rápido: Sintetizar a conversão de voz, C# (UWP) - serviços de voz'
titleSuffix: Azure Cognitive Services
description: Neste artigo, vai criar um C# aplicação plataforma Universal do Windows (UWP) com o SDK de voz dos serviços cognitivos. Sintetizar conversão de voz de texto em tempo real em orador do seu dispositivo. A aplicação baseia-se com o pacote de NuGet do SDK de voz e o Microsoft Visual Studio 2017.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: c11fbff883d2699bdd0a107fc462524d92ee410d
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67467447"
---
# <a name="quickstart-synthesize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Início rápido: Sintetizar voz numa aplicação UWP com o SDK de voz

Também estão disponíveis para inícios Rápidos [reconhecimento de fala](quickstart-csharp-uwp.md), [tradução de voz](quickstart-translate-speech-uwp.md) e [Assistente de voz em primeiro lugar virtual](quickstart-virtual-assistant-csharp-uwp.md).

Neste artigo, vai desenvolver uma C# plataforma Universal do Windows (UWP; Versão 1709 do Windows mais tarde) aplicativos usando os serviços cognitivos [SDK de voz](speech-sdk.md). O programa irá sintetizar a conversão de voz de texto em tempo real em orador do seu dispositivo. A aplicação é compilada com o [Pacote NuGet de SDK de Voz](https://aka.ms/csspeech/nuget) e o Microsoft Visual Studio 2017 (qualquer edição).

> [!NOTE]
> A Plataforma Universal do Windows permite-lhe desenvolver aplicações que são executadas em qualquer dispositivo que suporte o Windows 10, incluindo PCs, Xbox, Surface Hub e outros dispositivos.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Uma chave de subscrição do Azure para o serviço de voz. [Obter uma gratuitamente](get-started.md).

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

1. Introduza algum texto na caixa de texto e clique em **falar**. O texto é transmitido para os serviços de voz e sintetizado para conversão de voz, que desempenha na sua orador.

    ![Captura de ecrã da interface do usuário de síntese de voz](media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Explore C# exemplos no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar os tipos de voz](how-to-customize-voice-font.md)
- [Exemplos de voz de registo](record-custom-voice-samples.md)

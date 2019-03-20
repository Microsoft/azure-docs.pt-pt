---
title: 'Início rápido: Reconhecer a conversão de voz, C# (UWP) - serviços de voz'
titleSuffix: Azure Cognitive Services
description: Neste artigo, vai criar um C# aplicação plataforma Universal do Windows (UWP) com o SDK de voz dos serviços cognitivos. Converta voz em texto em tempo real, a partir do microfone do seu dispositivo. A aplicação baseia-se com o pacote de NuGet do SDK de voz e o Microsoft Visual Studio 2017.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/06/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: 26d6e5e53968a88eaccff863b4affee6057f5952
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57903305"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Início rápido: Reconhecer voz numa aplicação UWP com o SDK de voz

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, vai desenvolver uma C# plataforma Universal do Windows (UWP; Versão 1709 do Windows mais tarde) aplicativos usando os serviços cognitivos [SDK de voz](speech-sdk.md). O programa será transcrição de voz para texto em tempo real do microfone do dispositivo. A aplicação é compilada com o [Pacote NuGet de SDK de Voz](https://aka.ms/csspeech/nuget) e o Microsoft Visual Studio 2017 (qualquer edição).

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

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Abra o ficheiro de origem do code-behind `MainPage.xaml.cs` (encontre-o agrupado sob `MainPage.xaml`). Substitua todo o código no mesmo pelo seguinte.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. No processador `YourSubscriptionKey` neste ficheiro, substitua a cadeia de carateres `SpeechRecognitionFromMicrophone_ButtonClicked` pela sua chave de subscrição.

1. No processador `westus`, substitua também a cadeia de carateres `SpeechRecognitionFromMicrophone_ButtonClicked` pela `YourServiceRegion`região[ associada à subscrição (por exemplo, ](regions.md) para a subscrição de avaliação gratuita).

1. Guarde todas as alterações no projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

1. Compile a aplicação. Na barra de menus, selecione **Compilar** > **Compilar Solução**. Agora o código deverá ser compilado sem erros.

    ![Captura de ecrã da aplicação Visual Studio, com a opção Compilar Solução realçada](media/sdk/qs-csharp-uwp-08-build.png "Compilação bem-sucedida")

1. Inicie a aplicação. Na barra de menus, selecione **Depurar** > **Iniciar Depuração** ou prima **F5**.

    ![Captura de ecrã da aplicação Visual Studio, com a opção Iniciar Depuração realçada](media/sdk/qs-csharp-uwp-09-start-debugging.png "Iniciar a depuração da aplicação")

1. Surge uma janela. Selecione **Ativar Microfone** e confirme o pedido de permissão que aparece.

    ![Captura de ecrã do pedido de permissão](media/sdk/qs-csharp-uwp-10-access-prompt.png "Iniciar a aplicação para depuração")

1. Selecione **Reconhecimento de voz com entrada do microfone** e fale uma frase ou um trecho em inglês no microfone do dispositivo. Sua voz é transmitido para os serviços de voz e transcrito para texto, que aparece na janela.

    ![Captura de ecrã da interface de utilizador do reconhecimento de voz](media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Explore C# exemplos no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Traduzir voz](how-to-translate-speech-csharp.md)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de idioma](how-to-customize-language-model.md)

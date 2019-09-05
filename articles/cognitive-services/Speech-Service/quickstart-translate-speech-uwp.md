---
title: 'Início rápido: Traduzir fala, C# (UWP)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você criará um aplicativo Plataforma Universal do Windows (UWP) para capturar a fala do usuário, convertê-lo em outro idioma e gerar o texto para a linha de comando. Este guia foi projetado para usuários do Windows.
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 08/19/2019
ms.author: erhopf
ms.topic: quickstart
ms.openlocfilehash: e513cbbc615965ef196a830351aab8ac241c3f20
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382631"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c-uwp"></a>Início rápido: Traduzir a fala com o SDK de C# fala para (UWP)

Os guias de início rápido também estão disponíveis para [reconhecimento de fala](quickstart-csharp-uwp.md), [síntese de fala](quickstart-text-to-speech-csharp-uwp.md)e [assistente virtual de voz primeiro](quickstart-virtual-assistant-csharp-uwp.md).

Neste guia de início rápido, você criará um aplicativo Plataforma Universal do Windows (UWP) que captura a fala do usuário do microfone do seu computador, traduz a fala e transcreve o texto traduzido para a linha de comando em tempo real. Este aplicativo foi projetado para ser executado em Windows de 64 bits e é compilado com o [pacote NuGet do SDK de fala](https://aka.ms/csspeech/nuget) e Microsoft Visual Studio 2019.

Para obter uma lista completa de idiomas disponíveis para tradução de fala, consulte [suporte a idiomas](language-support.md).

> [!NOTE]
> O UWP permite que você desenvolva aplicativos que são executados em qualquer dispositivo que ofereça suporte ao Windows 10, incluindo PCs, Xbox, Surface Hub e outros dispositivos.

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

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Em **Gerenciador de soluções**, abra o arquivo `MainPage.xaml.cs`de origem code-behind. (Está agrupado em `MainPage.xaml`.)

1. Substitua todo o código nele pelo seguinte trecho:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. No manipulador deste arquivo, localize a cadeia de caracteres `YourSubscriptionKey`e substitua-a pela sua chave de assinatura. `SpeechTranslationFromMicrophone_ButtonClicked`

1. No manipulador, localize a cadeia de `YourServiceRegion`caracteres e substitua-a pela [região](regions.md) associada à sua assinatura. `SpeechTranslationFromMicrophone_ButtonClicked` (Por exemplo, use `westus` para a assinatura de avaliação gratuita.)

1. Na barra de menus, escolha **arquivo** > **salvar tudo** para salvar suas alterações.

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

Agora você está pronto para criar e testar seu aplicativo.

1. Na barra de menus, selecione **criar** > **solução de compilação** para compilar o aplicativo. Agora o código deverá ser compilado sem erros.

1. Escolha **depurar** > **Iniciar Depuração** (ou pressione **F5**) para iniciar o aplicativo. A janela **HelloWorld** é exibida.

   ![Aplicativo de tradução de exemplo C# do UWP no início rápido](media/sdk/qs-translate-speech-uwp-helloworld-window.png)

1. Selecione **habilitar microfone**e, quando a solicitação de permissão de acesso for exibida, selecione **Sim**.

   ![Solicitação de permissão de acesso ao microfone](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Selecione **traduzir fala da entrada do microfone**e fale uma frase ou sentença em inglês no microfone do dispositivo. O aplicativo transmite sua fala para o serviço de fala, que traduz a fala em texto em outro idioma (neste caso, alemão). O serviço de fala envia o texto traduzido de volta para o aplicativo, que exibe a tradução na janela.

   ![Interface do usuário de tradução de fala](media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Explorar C# exemplos no github](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Treinar um modelo para Fala Personalizada](how-to-custom-speech-train-model.md)

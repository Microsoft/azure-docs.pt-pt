---
title: 'Início rápido: sintetizar fala C# , (UWP)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, você cria um C# aplicativo plataforma universal do Windows (UWP) usando o SDK de fala dos serviços cognitivas. Você sintetiza a fala de texto em tempo real para o palestrante do dispositivo. O aplicativo é criado com o pacote NuGet do SDK de fala e Microsoft Visual Studio 2019.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/19/2019
ms.author: yinhew
ms.openlocfilehash: 3a88f4fc14286a60feb9d72676827b0cce91eb25
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503130"
---
> [!NOTE]
> A Plataforma Universal do Windows permite-lhe desenvolver aplicações que são executadas em qualquer dispositivo que suporte o Windows 10, incluindo PCs, Xbox, Surface Hub e outros dispositivos.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Criar um recurso de fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=uwp)
> * [Criar um projeto de exemplo vazio](../../../../quickstarts/create-project.md?tabs=uwp)

## <a name="add-sample-code"></a>Adicionar código de exemplo

Agora, adicione o código XAML que define a interface do usuário do aplicativo e adicione a C# implementação code-behind.

1. Em **Gerenciador de soluções**, abra `MainPage.xaml`.

1. Na exibição XAML do designer, insira o seguinte trecho XAML na marca de **grade** (entre `<Grid>` e `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml#StackPanel)]

1. No **Gerenciador de soluções**, abra o arquivo de origem code-behind `MainPage.xaml.cs`. (Está agrupado em `MainPage.xaml`.)

1. Substitua todo o código nele pelo seguinte trecho:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml.cs#code)]

1. No manipulador de `Speak_ButtonClicked` do arquivo de origem, localize a cadeia de caracteres `YourSubscriptionKey`e substitua-a pela sua chave de assinatura.

1. No manipulador de `Speak_ButtonClicked`, localize a cadeia de caracteres `YourServiceRegion`e substitua-a pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à sua assinatura. (Por exemplo, use `westus` para a assinatura de avaliação gratuita.)

1. Na barra de menus, escolha **arquivo** > **salvar tudo** para salvar suas alterações.

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

Agora você está pronto para criar e testar seu aplicativo.

1. Na barra de menus, escolha **compilar** > **Compilar solução** para compilar o aplicativo. Agora o código deverá ser compilado sem erros.

1. Escolha **depurar** > **Iniciar Depuração** (ou pressione **F5**) para iniciar o aplicativo. A janela **HelloWorld** é exibida.

   ![Aplicativo de síntese de fala de C# exemplo UWP no início rápido](~/articles/cognitive-services/Speech-Service/media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. Insira um texto na caixa e clique em **fala**. Seu texto é transmitido para os serviços de fala e sintetizado para fala, que é reproduzido no seu palestrante.

    ![Interface do usuário de síntese de fala](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Consultar também

- [Criar uma voz personalizada](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Registrar amostras de voz personalizadas](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)

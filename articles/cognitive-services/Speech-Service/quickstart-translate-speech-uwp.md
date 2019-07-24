---
title: 'Início rápido: Traduzir fala, C# (UWP)-serviços de fala'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você criará um aplicativo UWP (Plataforma Universal do Windows simples) para capturar a fala do usuário, convertê-lo em outro idioma e enviar o texto para a linha de comando. Este guia foi projetado para usuários do Windows.
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 07/23/2019
ms.author: erhopf
ms.openlocfilehash: dafcbd6bbe7f542d4eabc6b7cf858ac4871caae8
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404898"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c-uwp"></a>Início rápido: Traduzir a fala com o SDK de C# fala para (UWP)

Os guias de início rápido também estão disponíveis para o assistente virtual de [voz a texto](quickstart-csharp-uwp.md), conversão de [texto em fala](quickstart-text-to-speech-csharp-uwp.md) e [voz-primeiro](quickstart-virtual-assistant-csharp-uwp.md).

Neste guia de início rápido, você criará um aplicativo UWP (Plataforma Universal do Windows simples) que captura a fala do usuário do microfone do seu computador, traduz a fala e transcreve o texto traduzido para a linha de comando em tempo real. Este aplicativo foi projetado para ser executado em Windows de 64 bits e é criado com o [pacote NuGet do SDK de fala](https://aka.ms/csspeech/nuget) e Microsoft Visual Studio 2017.

Para obter uma lista completa de idiomas disponíveis para tradução de fala, consulte [suporte a idiomas](language-support.md).

> [!NOTE]
> O UWP permite que você desenvolva aplicativos que são executados em qualquer dispositivo que ofereça suporte ao Windows 10, incluindo PCs, Xbox, Surface Hub e outros dispositivos.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Uma chave de assinatura do Azure para o serviço de fala. [Obtenha um gratuitamente](get-started.md).

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. A interface do utilizador da aplicação é definida com o XAML. Abra `MainPage.xaml` no Explorador de Soluções. Na exibição XAML do designer, insira o seguinte trecho XAML entre `<Grid>` e. `</Grid>`

    [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Abra o ficheiro de origem do code-behind `MainPage.xaml.cs` (encontre-o agrupado sob `MainPage.xaml`). Substitua todo o código no mesmo pelo seguinte.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. No processador `YourSubscriptionKey` neste ficheiro, substitua a cadeia de carateres `SpeechTranslationFromMicrophone_ButtonClicked` pela sua chave de subscrição.

1. No processador `westus`, substitua também a cadeia de carateres `SpeechTranslationFromMicrophone_ButtonClicked` pela `YourServiceRegion`região[ associada à subscrição (por exemplo, ](regions.md) para a subscrição de avaliação gratuita).

1. Guarde todas as alterações no projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

1. Compile a aplicação. Na barra de menus, selecione **Compilar** > **Compilar Solução**. Agora o código deverá ser compilado sem erros.

    ![Captura de ecrã da aplicação Visual Studio, com a opção Compilar Solução realçada](media/sdk/qs-csharp-uwp-08-build.png "Compilação bem-sucedida")

1. Inicie a aplicação. Na barra de menus, selecione **Depurar** > **Iniciar Depuração** ou prima **F5**.

    ![Captura de ecrã da aplicação Visual Studio, com a opção Iniciar Depuração realçada](media/sdk/qs-csharp-uwp-09-start-debugging.png "Iniciar a depuração da aplicação")

1. Surge uma janela. Selecione **Ativar Microfone** e confirme o pedido de permissão que aparece.

    ![Captura de ecrã do pedido de permissão](media/sdk/qs-csharp-uwp-10-access-prompt.png "Iniciar a aplicação para depuração")

1. Selecione **Reconhecimento de voz com entrada do microfone** e fale uma frase ou um trecho em inglês no microfone do dispositivo. A sua voz é transmitida ao serviço de Voz e convertida para texto que é apresentado na janela.

    ![Captura de ecrã da interface de utilizador do reconhecimento de voz](media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explorar C# exemplos no github](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de idioma](how-to-customize-language-model.md)

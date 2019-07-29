---
title: 'Início rápido: Sintetizar fala, C# (.NET Core)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Saiba como sintetizar a fala em C# no .NET Core no Windows usando o SDK de fala
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: ccb6ec5b6f29c57a6136c571558d4126d0607710
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607692"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-core"></a>Início rápido: Sintetizar a fala com o SDK de fala para .NET Core

Os guias de início rápido também estão disponíveis para [reconhecimento](quickstart-csharp-dotnetcore-windows.md) de fala e [tradução de fala](quickstart-translate-speech-dotnetcore-windows.md).

Neste artigo, vai criar uma aplicação de consola C# para .NET Core em Windows, com o [SDK de Voz](speech-sdk.md) dos Serviços Cognitivos. Você sintetiza a fala de texto em tempo real para o palestrante do seu PC. O aplicativo é criado com o [pacote NuGet do SDK de fala](https://aka.ms/csspeech/nuget) e Microsoft Visual Studio 2017 ou posterior (qualquer edição).

> [!NOTE]
> O .NET Core é uma plataforma .NET de código-fonte aberto para várias plataformas, que implementa a especificação [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

Você precisa de uma chave de assinatura dos serviços de fala para concluir este guia de início rápido. Pode obter uma gratuitamente. Consulte [Experimente os serviços de fala gratuitamente](get-started.md) para obter detalhes.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [SDK .NET Core](https://dotnet.microsoft.com/download)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) ou posterior
* Uma chave de assinatura do Azure para o serviço de fala. [Obtenha um gratuitamente](get-started.md).

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-dotnetcore-create-proj.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra `Program.cs` e substitua todo o código no mesmo pelo seguinte.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnetcore/helloworld/Program.cs#code)]

1. No mesmo ficheiro, substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.

1. Substitua também a cadeia de carateres `YourServiceRegion`pela [região](regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

1. Guarde as alterações ao projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

1. Compile a aplicação. Na barra de menus, escolha **Compilar** > **Compilar solução**. O código deve ser compilado sem erros.

    ![Captura de ecrã da aplicação Visual Studio, com a opção Compilar Solução realçada](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Compilação bem-sucedida")

1. Inicie a aplicação. Na barra de menus, escolha **Depurar** > **Iniciar Depuração** ou prima **F5**.

    ![Captura de ecrã da aplicação Visual Studio, com a opção Iniciar Depuração realçada](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Iniciar a depuração da aplicação")

1. Uma janela de console é exibida, solicitando que você digite algum texto. Digite algumas palavras ou uma frase. O texto digitado é transmitido para os serviços de fala e sintetizado para fala, que é reproduzido no seu palestrante.

    ![Captura de tela da saída do console após a síntese bem-sucedida](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "Saída do console após síntese bem-sucedida")

## <a name="next-steps"></a>Passos Seguintes

Exemplos adicionais, como como resumir a fala para um arquivo de áudio, estão disponíveis no GitHub.

> [!div class="nextstepaction"]
> [Explorar C# exemplos no github](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar fontes de voz](how-to-customize-voice-font.md)
- [Gravar amostras de voz](record-custom-voice-samples.md)

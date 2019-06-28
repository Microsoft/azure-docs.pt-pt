---
title: 'Início rápido: Sintetizar a conversão de voz, C# (.NET Core) - serviços de voz'
titleSuffix: Azure Cognitive Services
description: Saiba como sintetizar voz em C# em .NET Core no Windows com o SDK de voz
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: 7b4a018e38ca625e38dc1658a95d3ce0e677f711
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67467426"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-core"></a>Início rápido: Sintetizar fala com o SDK de voz para .NET Core

Também estão disponíveis para inícios Rápidos [reconhecimento de fala](quickstart-csharp-dotnetcore-windows.md) e [tradução de voz](quickstart-translate-speech-dotnetcore-windows.md).

Neste artigo, vai criar uma aplicação de consola C# para .NET Core em Windows, com o [SDK de Voz](speech-sdk.md) dos Serviços Cognitivos. Sintetizar conversão de voz de texto em tempo real em orador do seu PC. A aplicação é compilada com o [pacote NuGet de SDK de Voz](https://aka.ms/csspeech/nuget) e o Microsoft Visual Studio 2017 (qualquer edição).

> [!NOTE]
> O .NET Core é uma plataforma .NET de código-fonte aberto para várias plataformas, que implementa a especificação [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

Precisa de uma chave de subscrição de serviços de voz para concluir este início rápido. Pode obter uma gratuitamente. Ver [experimentar gratuitamente os serviços de voz](get-started.md) para obter detalhes.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [SDK .NET Core](https://dotnet.microsoft.com/download)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Uma chave de subscrição do Azure para o serviço de voz. [Obter uma gratuitamente](get-started.md).

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

1. É apresentada uma janela de console, pedindo-lhe que introduza algum texto. Escreva algumas palavras ou uma frase. O texto que introduziu é transmitido para os serviços de voz e sintetizado para conversão de voz, que desempenha na sua orador.

    ![Captura de ecrã da saída da consola depois de síntese bem-sucedida](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "saída da consola depois de síntese com êxito")

## <a name="next-steps"></a>Passos Seguintes

Exemplos adicionais, tais como sintetizar a conversão de voz num arquivo de áudio, estão disponíveis no GitHub.

> [!div class="nextstepaction"]
> [Explore C# exemplos no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar os tipos de voz](how-to-customize-voice-font.md)
- [Exemplos de voz de registo](record-custom-voice-samples.md)

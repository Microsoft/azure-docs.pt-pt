---
title: 'Início rápido: Sintetizar fala, .NET Framework (Windows)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Use este guia para criar um aplicativo de console de conversão de texto em fala usando o .NET Framework para Windows e o SDK de fala. Quando terminar, você pode sintetizar a fala a partir do texto e ouvir a fala em seu palestrante em tempo real.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/27/2019
ms.author: yinhew
ms.openlocfilehash: 1a411455e4a6dea22e092cdfc8e70ee23b656435
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327455"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Início rápido: Sintetizar fala com o SDK de fala para .NET Framework (Windows)

Os guias de início rápido também estão disponíveis para [reconhecimento de fala](quickstart-csharp-dotnet-windows.md) e [tradução de fala](quickstart-translate-speech-dotnetframework-windows.md).

Use este guia para criar um aplicativo de console de conversão de texto em fala usando o .NET Framework para Windows e o SDK de fala. Quando terminar, você pode sintetizar a fala a partir do texto e ouvir a fala em seu palestrante em tempo real.

Para uma demonstração rápida (sem criar o projeto do Visual Studio por conta própria, conforme descrito neste artigo), obtenha os exemplos mais recentes do [SDK de fala dos serviços cognitivas](https://github.com/Azure-Samples/cognitive-services-speech-sdk) do github.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este projeto, precisa de:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Uma chave de assinatura para o serviço de fala. [Obtenha um gratuitamente](get-started.md).
* Um orador (ou headset) disponível.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra **Program.cs** e substitua o código gerado automaticamente por este exemplo:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Localize a cadeia de caracteres `YourSubscriptionKey` e substitua-a pela sua chave de assinatura dos serviços de fala.

1. Localize a cadeia de caracteres `YourServiceRegion` e substitua-a pela [região](regions.md) associada à sua assinatura. Por exemplo, se você estiver usando a assinatura de avaliação gratuita, a região será `westus`.

1. Na barra de menus, escolha **arquivo** > **salvar tudo**.

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

1. Na barra de menus, escolha **compilar** > **Compilar solução** para compilar o aplicativo. Agora o código deverá ser compilado sem erros.

1. Escolha **depurar** > **Iniciar Depuração** (ou selecione **F5**) para iniciar o aplicativo **HelloWorld** .

1. Insira uma frase ou sentença em inglês. O aplicativo transmite o texto para os serviços de fala, o que envia a fala sintetizada para o aplicativo para ser tocado no seu palestrante.

   ![Interface do usuário de síntese de fala](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explorar C# exemplos no github](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Criar uma voz personalizada](how-to-custom-voice-create-voice.md)
- [Registrar amostras de voz personalizadas](record-custom-voice-samples.md)

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
ms.date: 07/05/2019
ms.author: yinhew
ms.openlocfilehash: 3c925e0160a4c57f7e43c3463fc1db7098a6e606
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553606"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Início rápido: Sintetizar fala com o SDK de fala para .NET Framework (Windows)

Os guias de início rápido também estão disponíveis para [reconhecimento](quickstart-csharp-dotnet-windows.md) de fala e [tradução de fala](quickstart-translate-speech-dotnetframework-windows.md).

Use este guia para criar um aplicativo de console de conversão de texto em fala usando o .NET Framework para Windows e o SDK de fala. Quando terminar, você pode sintetizar a fala a partir do texto e ouvir a fala em seu palestrante em tempo real.

Para uma demonstração rápida (sem compilar o projeto do Visual Studio, conforme mostrado abaixo):

Obtenha os exemplos mais recentes do [SDK de fala dos serviços cognitivas](https://github.com/Azure-Samples/cognitive-services-speech-sdk) do github.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este projeto, precisa de:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Uma chave de assinatura para o serviço de fala. [Obtenha um gratuitamente](get-started.md).
* Um orador (ou headset) disponível.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra `Program.cs` e substitua o código gerado automaticamente por este exemplo:

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Localize e substitua a cadeia `YourSubscriptionKey` de caracteres pela sua chave de assinatura dos serviços de fala.

1. Encontre e substitua a cadeia de carateres `YourServiceRegion` pela [região](regions.md) associada à subscrição. Por exemplo, se estiver a utilizar a versão de avaliação gratuita, a região é `westus`.

1. Guarde as alterações ao projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

1. Na barra de menus, selecione **Compilar** > **Compilar Solução**. Agora o código deverá ser compilado sem erros.

    ![Captura de ecrã da aplicação Visual Studio, com a opção Compilar Solução realçada](media/sdk/qs-csharp-dotnet-windows-08-build.png "Compilação bem-sucedida")

1. Na barra de menus, selecione **Depurar** > **Iniciar Depuração** ou prima **F5** para iniciar a aplicação.

    ![Captura de ecrã da aplicação Visual Studio, com a opção Iniciar Depuração realçada](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Iniciar a depuração da aplicação")

1. Uma janela de console será exibida, solicitando que você digite algum texto. Digite algumas palavras ou uma frase. O texto digitado é transmitido para os serviços de fala e sintetizado para fala, que é reproduzido no seu palestrante.

    ![Captura de ecrã a mostrar o resultado da consola após o reconhecimento bem-sucedido](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "Resultado da consola após o reconhecimento bem-sucedido")

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Explorar C# exemplos no github](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar fontes de voz](how-to-customize-voice-font.md)
- [Gravar amostras de voz](record-custom-voice-samples.md)

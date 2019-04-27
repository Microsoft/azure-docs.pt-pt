---
title: 'Início rápido: Sintetizar a conversão de voz, .NET Framework (Windows) - serviços de voz'
titleSuffix: Azure Cognitive Services
description: Utilize este guia para criar uma aplicação de consola de texto para voz usando o .NET framework para Windows e o SDK de voz. Quando terminar, pode sintetizar voz de texto e ouça a conversão de voz na sua orador em tempo real.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 4/03/2019
ms.author: yinhew
ms.openlocfilehash: a013189e45b1c1c8eeb88d62a718d495c0c415a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60620488"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Início rápido: Sintetizar fala com o SDK de voz para .NET Framework (Windows)

Utilize este guia para criar uma aplicação de consola de texto para voz usando o .NET framework para Windows e o SDK de voz. Quando terminar, pode sintetizar voz de texto e ouça a conversão de voz na sua orador em tempo real.

Para uma rápida demonstração (sem criar projeto do Visual Studio por conta própria conforme mostrado abaixo):

Obter a versão mais recente [amostras do SDK de voz de serviços cognitivos](https://github.com/Azure-Samples/cognitive-services-speech-sdk) do GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este projeto, precisa de:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Uma chave de subscrição para o serviço de voz. [Obter uma gratuitamente](get-started.md).
* Um orador (ou o headset) disponível.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra `Program.cs` e substitua o código gerado automaticamente por este exemplo:

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Localizar e substituir a cadeia de caracteres `YourSubscriptionKey` com a sua chave de subscrição de serviços de voz.

1. Encontre e substitua a cadeia de carateres `YourServiceRegion` pela [região](regions.md) associada à subscrição. Por exemplo, se estiver a utilizar a versão de avaliação gratuita, a região é `westus`.

1. Guarde as alterações ao projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

1. Na barra de menus, selecione **Compilar** > **Compilar Solução**. Agora o código deverá ser compilado sem erros.

    ![Captura de ecrã da aplicação Visual Studio, com a opção Compilar Solução realçada](media/sdk/qs-csharp-dotnet-windows-08-build.png "Compilação bem-sucedida")

1. Na barra de menus, selecione **Depurar** > **Iniciar Depuração** ou prima **F5** para iniciar a aplicação.

    ![Captura de ecrã da aplicação Visual Studio, com a opção Iniciar Depuração realçada](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Iniciar a depuração da aplicação")

1. Será apresentada uma janela de consola, pedindo-lhe que introduza algum texto. Escreva algumas palavras ou uma frase. O texto que introduziu é transmitido para os serviços de voz e sintetizado para conversão de voz, que desempenha na sua orador.

    ![Captura de ecrã a mostrar o resultado da consola após o reconhecimento bem-sucedido](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "Resultado da consola após o reconhecimento bem-sucedido")

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Explore C# exemplos no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar os tipos de voz](how-to-customize-voice-font.md)
- [Exemplos de voz de registo](record-custom-voice-samples.md)

---
title: 'Início rápido: sintetizar fala C# , (.NET Core)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Saiba como sintetizar a fala em C# no .NET Core no Windows usando o SDK de fala
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: 670d32a93df9e2a0363163079b50c7306f81975e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502913"
---
> [!NOTE]
> O .NET Core é uma plataforma .NET de código-fonte aberto para várias plataformas, que implementa a especificação [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Criar um recurso de fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=dotnetcore)
> * [Criar um projeto de exemplo vazio](../../../../quickstarts/create-project.md?tabs=dotnetcore)
## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra `Program.cs` e substitua todo o código no mesmo pelo seguinte.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnetcore/text-to-speech/helloworld/Program.cs#code)]

1. No mesmo ficheiro, substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.

1. Substitua também a cadeia de caracteres `YourServiceRegion` pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

1. Guarde as alterações ao projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

1. Compile a aplicação. Na barra de menus, escolha **Compilar** > **Compilar solução**. O código deve ser compilado sem erros.

    ![Captura de tela do aplicativo do Visual Studio, com a opção de solução de compilação realçada](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Compilação bem-sucedida")

1. Inicie a aplicação. Na barra de menus, escolha **Depurar** > **Iniciar Depuração** ou prima **F5**.

    ![Captura de tela do aplicativo do Visual Studio, com a opção Iniciar Depuração realçada](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Iniciar o aplicativo na depuração")

1. Uma janela de console é exibida, solicitando que você digite algum texto. Digite algumas palavras ou uma frase. O texto digitado é transmitido para os serviços de fala e sintetizado para fala, que é reproduzido no seu palestrante.

    ![Captura de tela da saída do console após a síntese bem-sucedida](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "Saída do console após síntese bem-sucedida")

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Consultar também

- [Criar uma voz personalizada](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Registrar amostras de voz personalizadas](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)

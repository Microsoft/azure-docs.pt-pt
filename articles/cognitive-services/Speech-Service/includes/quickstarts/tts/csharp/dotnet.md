---
title: 'Quickstart: Discurso sintetizador, C# (Windows) - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Utilize este guia para criar uma aplicação de consola de texto para falar utilizando a estrutura .NET para Windows e o Speech SDK. Quando terminar, pode sintetizar o discurso a partir do texto e ouvir o discurso no seu orador em tempo real.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: 89f491aae91787ec6b67b9aa2daee262f3bab62e
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81275597"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Criar um recurso de fala azure](../../../../get-started.md)
> * [Crie o seu ambiente de desenvolvimento e crie um projeto vazio](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra **Program.cs** e substitua o código gerado automaticamente por esta amostra:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/text-to-speech/helloworld/Program.cs#code)]

1. Encontre a `YourSubscriptionKey`corda e substitua-a pela chave de subscrição do serviço Speech.

1. Encontre a `YourServiceRegion`corda e substitua-a pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à sua subscrição. Por exemplo, se estiver a usar a subscrição de teste gratuito, a região é `westus`.

1. A partir da barra de menus, escolha **File** > **Save All**.

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

1. A partir da barra de menus, escolha **Build Build** > **Solution** para construir a aplicação. Agora o código deverá ser compilado sem erros.

1. Escolha **Debug** > **Start Debugging** (ou selecione **F5)** para iniciar a aplicação **Helloworld.**

1. Introduza uma frase ou frase em inglês. A aplicação transmite o seu texto ao serviço da Fala, que envia um discurso sintetizado para a aplicação para reproduzir no seu altifalante.

   ![Interface de utilizador da síntese da fala](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Consulte também

- [Criar uma voz personalizada](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Gravar amostras de voz personalizadas](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)

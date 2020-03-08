---
title: 'Quickstart: Sinthesize C# speech, (Windows) - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Utilize este guia para criar uma aplicação de consola de texto para falar utilizando a estrutura .NET para Windows e o Speech SDK. Quando terminar, pode sintetizar o discurso a partir do texto e ouvir o discurso no seu orador em tempo real.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/27/2019
ms.author: yinhew
ms.openlocfilehash: b615ba8085650e9aa686fb4a229d9752c4f6e2ce
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925845"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Criar um recurso de fala azure](../../../../get-started.md)
> * [Crie o seu ambiente de desenvolvimento e crie um projeto vazio](../../../../quickstarts/setup-platform.md?tabs=dotnet)

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra **Program.cs** e substitua o código gerado automaticamente por esta amostra:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/text-to-speech/helloworld/Program.cs#code)]

1. Encontre o string `YourSubscriptionKey`e substitua-o pela chave de subscrição do serviço Speech.

1. Encontre a cadeia `YourServiceRegion`e substitua-a pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à sua subscrição. Por exemplo, se estiver a usar a subscrição de teste gratuito, a região é `westus`.

1. Na barra de menus, escolha **File** > **Save All**.

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

1. A partir da barra de menus, escolha **construir** > **Construir Solução** para construir a aplicação. Agora o código deverá ser compilado sem erros.

1. Escolha **debug** > **Iniciar Debugging** (ou selecione **F5)** para iniciar a aplicação **helloworld.**

1. Introduza uma frase ou frase em inglês. A aplicação transmite o seu texto ao serviço da Fala, que envia um discurso sintetizado para a aplicação para reproduzir no seu altifalante.

   ![Interface de utilizador da síntese da fala](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Consulte também

- [Criar uma voz personalizada](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Gravar amostras de voz personalizadas](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)

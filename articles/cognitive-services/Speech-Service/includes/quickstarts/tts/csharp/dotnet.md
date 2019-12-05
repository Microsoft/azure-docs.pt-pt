---
title: 'Início rápido: sintetizar fala C# , (Windows)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Use este guia para criar um aplicativo de console de conversão de texto em fala usando o .NET Framework para Windows e o SDK de fala. Quando terminar, você pode sintetizar a fala a partir do texto e ouvir a fala em seu palestrante em tempo real.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/27/2019
ms.author: yinhew
ms.openlocfilehash: 38dac73d253245fce86ac419c7a4eb3a106df3f0
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818132"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Criar um recurso de fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Criar um projeto de exemplo vazio](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra **Program.cs** e substitua o código gerado automaticamente por este exemplo:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/text-to-speech/helloworld/Program.cs#code)]

1. Localize a cadeia de caracteres `YourSubscriptionKey`e substitua-a pela sua chave de assinatura do serviço de fala.

1. Localize a cadeia de caracteres `YourServiceRegion`e substitua-a pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à sua assinatura. Por exemplo, se você estiver usando a assinatura de avaliação gratuita, a região será `westus`.

1. Na barra de menus, escolha **arquivo** > **salvar tudo**.

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

1. Na barra de menus, escolha **compilar** > **Compilar solução** para compilar o aplicativo. Agora o código deverá ser compilado sem erros.

1. Escolha **depurar** > **Iniciar Depuração** (ou selecione **F5**) para iniciar o aplicativo **HelloWorld** .

1. Insira uma frase ou sentença em inglês. O aplicativo transmite o texto para o serviço de fala, que envia a fala sintetizada para que o aplicativo Jogue no seu palestrante.

   ![Interface do usuário de síntese de fala](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Ver também

- [Criar uma voz personalizada](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Registrar amostras de voz personalizadas](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)

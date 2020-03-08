---
title: 'Quickstart: Sinthesize C# speech, (.NET Core) - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Aprenda a sintetizar o discurso em C# baixo do Núcleo .NET no Windows usando o SDK de Fala
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: ba6a373f678f8d34b34a669b3fbb3b570c90ed0d
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925688"
---
> [!NOTE]
> O .NET Core é uma plataforma .NET de código-fonte aberto para várias plataformas, que implementa a especificação [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Criar um recurso de fala azure](../../../../get-started.md)
> * [Crie o seu ambiente de desenvolvimento e crie um projeto vazio](../../../../quickstarts/setup-platform.md?tabs=dotnetcore)

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra `Program.cs` e substitua todo o código no mesmo pelo seguinte.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnetcore/text-to-speech/helloworld/Program.cs#code)]

1. No mesmo ficheiro, substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.

1. Substitua também a cadeia de carateres `YourServiceRegion`pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

1. Guarde as alterações ao projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

1. Compile a aplicação. Na barra de menus, escolha **Compilar** > **Compilar solução**. O código deve ser compilado sem erros.

    ![Screenshot da aplicação Visual Studio, com a opção Build Solution em destaque](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Construção bem sucedida")

1. Inicie a aplicação. Na barra de menus, escolha **Depurar** > **Iniciar Depuração** ou prima **F5**.

    ![Screenshot da aplicação Visual Studio, com a opção Start Debugging em destaque](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Inicie a aplicação em depuração")

1. Aparece uma janela de consola, levando-o a escrever algum texto. Escreva algumas palavras ou uma frase. O texto que escreveu é transmitido ao serviço da Fala e sintetizado para a fala, que reproduz no seu altifalante.

    ![Screenshot da saída da consola após síntese bem sucedida](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "Saída de consola após síntese bem sucedida")

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Consulte também

- [Criar uma voz personalizada](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Gravar amostras de voz personalizadas](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)

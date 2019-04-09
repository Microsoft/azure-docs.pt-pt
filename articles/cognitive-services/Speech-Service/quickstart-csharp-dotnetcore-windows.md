---
title: 'Início rápido: Reconhecer a conversão de voz, C# (.NET Core) - serviços de voz'
titleSuffix: Azure Cognitive Services
description: Aprender a reconhecer a voz em C# em .NET Core no Windows ou macOS com o SDK de voz
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: wolfma
ms.openlocfilehash: 938efe79d4f9f0b9003fcf83196df80d71d16e75
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009432"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-core"></a>Início rápido: Reconhecer a conversão de voz com o SDK de voz para .NET Core

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, vai criar um C# consola de aplicação para .NET Core no Windows ou macOS, utilizando os serviços cognitivos [SDK de voz](speech-sdk.md). Converta voz em texto em tempo real a partir do microfone do seu PC. A aplicação é compilada com o [pacote NuGet de SDK de Voz](https://aka.ms/csspeech/nuget) e o Microsoft Visual Studio 2017 (qualquer edição).

> [!NOTE]
> O .NET Core é uma plataforma .NET de código-fonte aberto para várias plataformas, que implementa a especificação [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

Precisa de uma chave de subscrição de serviços de voz para concluir este início rápido. Pode obter uma gratuitamente. Ver [experimentar gratuitamente os serviços de voz](get-started.md) para obter detalhes.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [SDK .NET core](https://dotnet.microsoft.com/download)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Uma chave de subscrição do Azure para o serviço de voz. [Obter uma gratuitamente](get-started.md).

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-dotnetcore-create-proj.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra `Program.cs` e substitua todo o código no mesmo pelo seguinte.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore/helloworld/Program.cs#code)]

1. No mesmo ficheiro, substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.

1. Substitua também a cadeia de carateres `YourServiceRegion`pela [região](regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

1. Guarde as alterações ao projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

1. Compile a aplicação. Na barra de menus, escolha **Compilar** > **Compilar solução**. O código deve ser compilado sem erros.

    ![Captura de ecrã da aplicação Visual Studio, com a opção Compilar Solução realçada](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Compilação bem-sucedida")

1. Inicie a aplicação. Na barra de menus, escolha **Depurar** > **Iniciar Depuração** ou prima **F5**.

    ![Captura de ecrã da aplicação Visual Studio, com a opção Iniciar Depuração realçada](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Iniciar a depuração da aplicação")

1. É apresentada uma janela de consola que lhe pede para dizer algo. Diga uma expressão ou uma frase em inglês. Sua fala é transmitida para os serviços de voz e transcrito para texto, que aparece na janela da mesma.

    ![Captura de ecrã a mostrar o resultado da consola após o reconhecimento bem-sucedido](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "Resultado da consola após o reconhecimento bem-sucedido")

## <a name="next-steps"></a>Passos Seguintes

Exemplos adicionais, tais como a leitura de voz de um arquivo de áudio, estão disponíveis no GitHub.

> [!div class="nextstepaction"]
> [Explore C# exemplos no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de idioma](how-to-customize-language-model.md)

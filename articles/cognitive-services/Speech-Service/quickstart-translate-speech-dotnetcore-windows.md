---
title: 'Início rápido: Traduzir a conversão de voz, C# (.NET Core Windows)'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, irá criar um aplicativo simples do .NET Core para capturar a voz do utilizador, convertê-lo em outro idioma e o texto na linha de comando de saída. Este guia foi concebido para utilizadores do Windows.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 03/13/2019
ms.author: erhopf
ms.openlocfilehash: 8f246e5b3d96855bd40ff50e2f382d8576fee021
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466112"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-net-core"></a>Início rápido: Traduzir a conversão de voz com o SDK de voz para .NET Core

Também estão disponíveis para inícios Rápidos [voz em texto](quickstart-csharp-dotnet-windows.md) e [voz](quickstart-text-to-speech-dotnetcore.md).

Neste início rápido, irá criar uma aplicação .NET Core simple que captura a voz do utilizador do microfone do seu computador, traduz-se a conversão de voz e converte o texto traduzido para a linha de comandos em tempo real. Esta aplicação foi concebida para ser executado no Windows de 64 bits e baseia-se com o [pacote NuGet do SDK de voz](https://aka.ms/csspeech/nuget) e Microsoft Visual Studio 2017.

Para obter uma lista completa dos idiomas disponíveis para tradução de voz, consulte [suporte de idiomas](language-support.md).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [SDK .NET Core](https://dotnet.microsoft.com/download)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Uma chave de subscrição do Azure para o serviço de voz. [Obter uma gratuitamente](get-started.md).

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-dotnetcore-create-proj.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra `Program.cs` e substitua todo o código no mesmo pelo seguinte.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-dotnetcore/helloworld/Program.cs#code)]

1. No mesmo ficheiro, substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.

1. Substitua também a cadeia de carateres `YourServiceRegion`pela [região](regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

1. Guarde as alterações ao projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

1. Compile a aplicação. Na barra de menus, escolha **Compilar** > **Compilar solução**. O código deve ser compilado sem erros.

    ![Captura de ecrã da aplicação Visual Studio, com a opção Compilar Solução realçada](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Compilação bem-sucedida")

1. Inicie a aplicação. Na barra de menus, escolha **Depurar** > **Iniciar Depuração** ou prima **F5**.

    ![Captura de ecrã da aplicação Visual Studio, com a opção Iniciar Depuração realçada](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Iniciar a depuração da aplicação")

1. É apresentada uma janela de consola que lhe pede para dizer algo. Diga uma expressão ou uma frase em inglês. Sua fala é transmitida para o serviço de voz, traduzida e transcrito para texto, que aparece na janela da mesma.

    ![Captura de ecrã da saída da consola após a tradução de bem sucedida](media/sdk/qs-translate-csharp-dotnetcore-windows-output.png "saída da consola após a tradução de bem sucedida")


## <a name="next-steps"></a>Passos Seguintes

Exemplos adicionais, tais como ler voz de um arquivo de áudio e de saída do texto traduzido como fala sintetizada, estão disponíveis no GitHub.

> [!div class="nextstepaction"]
> [Explore C# exemplos no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de idioma](how-to-customize-language-model.md)

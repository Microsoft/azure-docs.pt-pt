---
title: 'Início rápido: Traduzir a fala C# , (.NET Framework Windows)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você criará um aplicativo simples de .NET Framework para capturar a fala do usuário, convertê-lo em outro idioma e enviar o texto para a linha de comando. Este guia foi projetado para usuários do Windows.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: e62756ab428135ca3d5d9107f37e61813097d21f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553456"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-net-framework"></a>Início rápido: Traduza a fala com o SDK de fala para .NET Framework

Os guias de início rápido também estão disponíveis para [conversão de fala em texto](quickstart-csharp-dotnet-windows.md) e [texto em fala](quickstart-text-to-speech-dotnet-windows.md).

Neste guia de início rápido, você criará um aplicativo .NET Framework simples que captura a fala do usuário do microfone do seu computador, traduz a fala e transcreve o texto traduzido para a linha de comando em tempo real. Este aplicativo foi projetado para ser executado em Windows de 64 bits e é criado com o [pacote NuGet do SDK de fala](https://aka.ms/csspeech/nuget) e Microsoft Visual Studio 2017.

Para obter uma lista completa de idiomas disponíveis para tradução de fala, consulte [suporte a idiomas](language-support.md).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Uma chave de assinatura do Azure para o serviço de fala. [Obtenha um gratuitamente](get-started.md).

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra `Program.cs` e substitua todo o código no mesmo pelo seguinte.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. No mesmo ficheiro, substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.

1. Substitua também a cadeia de carateres `YourServiceRegion`pela [região](regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

1. Guarde as alterações ao projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

1. Compile a aplicação. Na barra de menus, escolha **Compilar** > **Compilar solução**. O código deve ser compilado sem erros.

    ![Captura de ecrã da aplicação Visual Studio, com a opção Compilar Solução realçada](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Compilação bem-sucedida")

1. Inicie a aplicação. Na barra de menus, escolha **Depurar** > **Iniciar Depuração** ou prima **F5**.

    ![Captura de ecrã da aplicação Visual Studio, com a opção Iniciar Depuração realçada](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Iniciar a depuração da aplicação")

1. É apresentada uma janela de consola que lhe pede para dizer algo. Diga uma expressão ou uma frase em inglês. Sua fala é transmitida para o serviço de fala, traduzida e transcrita para texto, que aparece na mesma janela.

    ![Captura de tela da saída do console após tradução bem-sucedida](media/sdk/qs-translate-csharp-dotnetcore-windows-output.png "Saída do console após tradução bem-sucedida")

## <a name="next-steps"></a>Passos Seguintes

Exemplos adicionais, como a leitura de fala de um arquivo de áudio e a saída de texto traduzido como fala sintetizada, estão disponíveis no GitHub.

> [!div class="nextstepaction"]
> [Explorar C# exemplos no github](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de idioma](how-to-customize-language-model.md)

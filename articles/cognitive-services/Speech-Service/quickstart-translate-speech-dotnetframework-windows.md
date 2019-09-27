---
title: 'Início rápido: Traduzir a fala C# , (.NET Framework Windows)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você criará um aplicativo .NET Framework para capturar a fala do usuário, convertê-lo em outro idioma e enviar o texto para a linha de comando. Este guia foi projetado para usuários do Windows.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: erhopf
ms.openlocfilehash: bb710a3e3adb13aa3999c13043c8bb93f6b885f1
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327350"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-net-framework-windows"></a>Início rápido: Traduzir a fala com o SDK de fala para .NET Framework (Windows)

Os guias de início rápido também estão disponíveis para [reconhecimento de fala](quickstart-csharp-dotnet-windows.md) e [síntese de fala](quickstart-text-to-speech-dotnet-windows.md).

Neste guia de início rápido, você criará um aplicativo .NET Framework que captura a fala do usuário do microfone do seu computador, traduz a fala e transcreve o texto traduzido para a linha de comando em tempo real. Este aplicativo pode ser executado em Windows de 32 bits ou 64 bits e é compilado com o [pacote NuGet do SDK de fala](https://aka.ms/csspeech/nuget) e Microsoft Visual Studio 2019.

Para obter uma lista completa de idiomas disponíveis para tradução de fala, consulte [suporte a idiomas](language-support.md).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Uma chave de assinatura do Azure para o serviço de fala. [Obtenha um gratuitamente](get-started.md).

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra **Program.cs**e substitua todo o código com o seguinte.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Localize a cadeia de caracteres `YourSubscriptionKey` e substitua-a pela sua chave de assinatura.

1. Localize a cadeia de caracteres `YourServiceRegion` e substitua-a pela [região](regions.md) associada à sua assinatura. Por exemplo, se você estiver usando a assinatura de avaliação gratuita, a região será `westus`.

1. Na barra de menus, escolha **arquivo** > **salvar tudo**.

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

1. Na barra de menus, escolha **compilar** > **Compilar solução** para compilar o aplicativo. Agora o código deverá ser compilado sem erros.

1. Escolha **depurar** > **Iniciar Depuração** (ou selecione **F5**) para iniciar o aplicativo **HelloWorld** .

1. Fale uma frase ou frase em inglês no microfone do seu dispositivo. O aplicativo transmite sua fala para o serviço de fala, que traduz a fala em texto em outro idioma (neste caso, alemão). O serviço de fala envia o texto traduzido de volta para o aplicativo, que exibe a tradução na janela.

   ![Interface do usuário de tradução de fala](media/sdk/qs-translate-csharp-dotnetcore-windows-output.png)

## <a name="next-steps"></a>Passos seguintes

Exemplos adicionais, como a leitura de fala de um arquivo de áudio e a saída de texto traduzido como fala sintetizada, estão disponíveis no GitHub.

> [!div class="nextstepaction"]
> [Explorar C# exemplos no github](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Treinar um modelo para Fala Personalizada](how-to-custom-speech-train-model.md)

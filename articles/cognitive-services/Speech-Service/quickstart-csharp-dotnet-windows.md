---
title: 'Início rápido: Reconhecer fala, .NET Framework (Windows)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Utilize este guia para criar uma aplicação de consola de voz em texto com o .NET framework para Windows e o SDK de Voz. Quando terminar, pode utilizar o microfone do seu computador para converter voz em texto em tempo real.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: wolfma
ms.openlocfilehash: cb140647394858fbc0a9a00ea125365d5b7a08d5
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327054"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Início rápido: Reconhecer a fala com o SDK de fala para .NET Framework (Windows)

Os guias de início rápido também estão disponíveis para [síntese de fala](quickstart-text-to-speech-dotnet-windows.md) e [tradução de fala](quickstart-translate-speech-dotnetframework-windows.md).

Se desejar, escolha uma linguagem de programação e/ou ambiente diferente:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Utilize este guia para criar uma aplicação de consola de voz em texto com o .NET framework para Windows e o SDK de Voz. Quando terminar, pode utilizar o microfone do seu computador para converter voz em texto em tempo real.

Para uma demonstração rápida (sem criar o projeto do Visual Studio por conta própria, conforme descrito neste artigo), obtenha os exemplos mais recentes do [SDK de fala dos serviços cognitivas](https://github.com/Azure-Samples/cognitive-services-speech-sdk) do github.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este projeto, precisa de:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Uma chave de assinatura para o serviço de fala. [Obtenha um gratuitamente](get-started.md).
* Acesso ao microfone do seu computador.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra **Program.cs** e substitua o código gerado automaticamente por este exemplo:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Localize a cadeia de caracteres `YourSubscriptionKey` e substitua-a pela sua chave de assinatura dos serviços de fala.

1. Localize a cadeia de caracteres `YourServiceRegion` e substitua-a pela [região](regions.md) associada à sua assinatura. Por exemplo, se você estiver usando a assinatura de avaliação gratuita, a região será `westus`.

1. Na barra de menus, escolha **arquivo** > **salvar tudo**.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

1. Na barra de menus, escolha **compilar** > **Compilar solução** para compilar o aplicativo. Agora o código deverá ser compilado sem erros.

1. Escolha **depurar** > **Iniciar Depuração** (ou selecione **F5**) para iniciar o aplicativo **HelloWorld** .

1. Fale uma frase ou frase em inglês no microfone do seu dispositivo. O aplicativo transmite sua fala para os serviços de fala, que envia o texto transcrevedo de volta para o aplicativo para exibição.

   ![Interface do usuário de reconhecimento de fala](media/sdk/qs-csharp-dotnet-windows-10-console-output.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explorar C# exemplos no github](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Treinar um modelo para Fala Personalizada](how-to-custom-speech-train-model.md)

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
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 47f955734070be4adfe7f58da98265b976e643d5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68554167"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Início rápido: Reconhecer a fala com o SDK de fala para .NET Framework (Windows)

Os guias de início rápido também estão disponíveis para conversão de [texto em fala](quickstart-text-to-speech-dotnet-windows.md) e [de fala](quickstart-translate-speech-dotnetframework-windows.md).

Se desejar, escolha uma linguagem de programação e/ou ambiente diferente:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Utilize este guia para criar uma aplicação de consola de voz em texto com o .NET framework para Windows e o SDK de Voz. Quando terminar, pode utilizar o microfone do seu computador para converter voz em texto em tempo real.

Para uma demonstração rápida (sem compilar o projeto do Visual Studio, conforme mostrado abaixo):

Obtenha os exemplos mais recentes do [SDK de fala dos serviços cognitivas](https://github.com/Azure-Samples/cognitive-services-speech-sdk) do github.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este projeto, precisa de:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Uma chave de assinatura para o serviço de fala. [Obtenha um gratuitamente](get-started.md).
* Acesso ao microfone do seu computador

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra `Program.cs` e substitua o código gerado automaticamente por este exemplo:

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Localize e substitua a cadeia `YourSubscriptionKey` de caracteres pela sua chave de assinatura dos serviços de fala.

1. Encontre e substitua a cadeia de carateres `YourServiceRegion` pela [região](regions.md) associada à subscrição. Por exemplo, se estiver a utilizar a versão de avaliação gratuita, a região é `westus`.

1. Guarde as alterações ao projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

1. Na barra de menus, selecione **Compilar** > **Compilar Solução**. Agora o código deverá ser compilado sem erros.

    ![Captura de ecrã da aplicação Visual Studio, com a opção Compilar Solução realçada](media/sdk/qs-csharp-dotnet-windows-08-build.png "Compilação bem-sucedida")

1. Na barra de menus, selecione **Depurar** > **Iniciar Depuração** ou prima **F5** para iniciar a aplicação.

    ![Captura de ecrã da aplicação Visual Studio, com a opção Iniciar Depuração realçada](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Iniciar a depuração da aplicação")

1. Será apresentada uma janela de consola, que lhe pede para falar. Agora, diga algo em inglês. Sua fala é transmitida para os serviços de fala e transcrita para texto em tempo real. O resultado é impresso na consola.

    ![Captura de ecrã a mostrar o resultado da consola após o reconhecimento bem-sucedido](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "Resultado da consola após o reconhecimento bem-sucedido")

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Explorar C# exemplos no github](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de idioma](how-to-customize-language-model.md)

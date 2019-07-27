---
title: 'Início rápido: Sintetizar fala, C++ (Windows)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Saiba como sintetizar a fala no C++ Windows desktop usando o SDK de fala
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: yinhew
ms.openlocfilehash: 6936d8f93221402fca9b250d09a842ce753e0e12
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559305"
---
# <a name="quickstart-synthesize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Início rápido: Sintetizar fala no C++ Windows usando o SDK de fala

Os guias de início rápido também estão disponíveis para [reconhecimento](quickstart-cpp-windows.md) de fala e [tradução de fala](quickstart-translate-speech-cpp-windows.md).

Neste artigo, vai criar uma aplicação de consola C++ para Windows. Você usa o SDK de [fala](speech-sdk.md) dos serviços cognitivas para sintetizar a fala de texto em tempo real e reproduzir a fala no palestrante do seu PC. A aplicação é compilada com o [pacote NuGet de SDK de Voz](https://aka.ms/csspeech/nuget) e o Microsoft Visual Studio 2017 (qualquer edição).

O recurso descrito dentro deste artigo está disponível no [Speech SDK 1.5.0](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/1.5.0).

Para obter uma lista completa de idiomas/vozes disponíveis para síntese de fala, consulte [suporte a idiomas](language-support.md#text-to-speech).

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de uma chave de assinatura dos serviços de fala para concluir este guia de início rápido. Pode obter uma gratuitamente. Consulte [Experimente os serviços de fala gratuitamente](get-started.md) para obter detalhes.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra o ficheiro de origem *helloworld.cpp*. Substitua todo o código abaixo da instrução de inclusão inicial (`#include "stdafx.h"` ou `#include "pch.h"`) pelo seguinte:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/cpp-windows/helloworld/helloworld.cpp#code)]

1. No mesmo ficheiro, substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.

1. Substitua a cadeia de carateres `YourServiceRegion` pela [região](regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

1. Guarde as alterações ao projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

1. Compile a aplicação. Na barra de menus, escolha **Compilar** > **Compilar solução**. O código deve ser compilado sem erros.

   ![Captura de ecrã da aplicação Visual Studio, com a opção Compilar Solução realçada](media/sdk/qs-cpp-windows-06-build.png)

1. Inicie a aplicação. Na barra de menus, escolha **Depurar** > **Iniciar Depuração** ou prima **F5**.

   ![Captura de ecrã da aplicação Visual Studio, com a opção Iniciar Depuração realçada](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Uma janela de console é exibida, solicitando que você digite algum texto. Digite algumas palavras ou uma frase. O texto digitado é transmitido para os serviços de fala e sintetizado para fala, que é reproduzido no seu palestrante.

   ![Captura de tela da saída do console após a síntese bem-sucedida](media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>Passos Seguintes

Exemplos adicionais, como como salvar a fala em um arquivo de áudio, estão disponíveis no GitHub.

> [!div class="nextstepaction"]
> [Explorar C++ exemplos no github](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar fontes de voz](how-to-customize-voice-font.md)
- [Gravar amostras de voz](record-custom-voice-samples.md)

---
title: 'Início rápido: Sintetizar a conversão de voz, C++ (Windows) - serviços de voz'
titleSuffix: Azure Cognitive Services
description: Saiba como sintetizar voz em C++ no ambiente de trabalho do Windows com o SDK de voz
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 4/03/2019
ms.author: yinhew
ms.openlocfilehash: a9613f0839735457cf172fdacb0ef6a943ca27de
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020771"
---
# <a name="quickstart-synthesize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Início rápido: Sintetizar voz em C++ no Windows com o SDK de voz

Neste artigo, vai criar uma aplicação de consola C++ para Windows. Utiliza os serviços cognitivos [SDK de voz](speech-sdk.md) sintetizar a voz do texto em tempo real e reproduzir a conversão de voz no orador do seu PC. A aplicação é compilada com o [pacote NuGet de SDK de Voz](https://aka.ms/csspeech/nuget) e o Microsoft Visual Studio 2017 (qualquer edição).

A funcionalidade descrita neste artigo está disponível a partir [SDK de voz 1.5.0](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/1.5.0).

Para obter uma lista completa de idiomas/vozes disponíveis para síntese de fala, consulte [suporte de idiomas](language-support.md#text-to-speech).

## <a name="prerequisites"></a>Pré-requisitos

Precisa de uma chave de subscrição de serviços de voz para concluir este início rápido. Pode obter uma gratuitamente. Ver [experimentar gratuitamente os serviços de voz](get-started.md) para obter detalhes.

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

1. É apresentada uma janela de console, pedindo-lhe que introduza algum texto. Escreva algumas palavras ou uma frase. O texto que introduziu é transmitido para os serviços de voz e sintetizado para conversão de voz, que desempenha na sua orador.

   ![Captura de ecrã da saída da consola depois de síntese com êxito](media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>Passos Seguintes

Exemplos adicionais, tais como salvar fala num arquivo de áudio, estão disponíveis no GitHub.

> [!div class="nextstepaction"]
> [Explore exemplos de C++ no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar os tipos de voz](how-to-customize-voice-font.md)
- [Exemplos de voz de registo](record-custom-voice-samples.md)

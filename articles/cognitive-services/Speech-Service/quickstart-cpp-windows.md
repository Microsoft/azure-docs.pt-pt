---
title: 'Início rápido: Reconhecer a conversão de voz, C++ (Windows) - serviços de voz'
titleSuffix: Azure Cognitive Services
description: Aprender a reconhecer voz em C++ no ambiente de trabalho do Windows com o SDK de voz
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: wolfma
ms.openlocfilehash: d6504b97bbc6c1ba4bcd02da54a29501c164439f
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447995"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Início rápido: Reconhecer voz em C++ no Windows com o SDK de voz

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, vai criar uma aplicação de consola C++ para Windows. Utilize o[SDK de Voz](speech-sdk.md) dos Serviços Cognitivos para a conversão de voz em texto em tempo real, a partir do microfone do seu PC. A aplicação é compilada com o [pacote NuGet de SDK de Voz](https://aka.ms/csspeech/nuget) e o Microsoft Visual Studio 2017 (qualquer edição).

## <a name="prerequisites"></a>Pré-requisitos

Precisa de uma chave de subscrição do serviço de Voz para concluir este Guia de Início Rápido. Pode obter uma gratuitamente. Veja [Experimente o serviço de Voz gratuitamente](get-started.md) para obter detalhes.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra o ficheiro de origem *helloworld.cpp*. Substitua todo o código abaixo da instrução de inclusão inicial (`#include "stdafx.h"` ou `#include "pch.h"`) pelo seguinte:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. No mesmo ficheiro, substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.

1. Substitua a cadeia de carateres `YourServiceRegion` pela [região](regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

1. Guarde as alterações ao projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

1. Compile a aplicação. Na barra de menus, escolha **Compilar** > **Compilar solução**. O código deve ser compilado sem erros.

   ![Captura de ecrã da aplicação Visual Studio, com a opção Compilar Solução realçada](media/sdk/qs-cpp-windows-06-build.png)

1. Inicie a aplicação. Na barra de menus, escolha **Depurar** > **Iniciar Depuração** ou prima **F5**.

   ![Captura de ecrã da aplicação Visual Studio, com a opção Iniciar Depuração realçada](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. É apresentada uma janela de consola que lhe pede para dizer algo. Diga uma expressão ou uma frase em inglês. A sua voz é transmitida ao serviço de Voz e convertida para texto que é apresentado na mesma janela.

   ![Captura de ecrã da saída da consola após o reconhecimento bem-sucedido](media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>Passos Seguintes

Exemplos adicionais, tais como a leitura de voz de um arquivo de áudio, estão disponíveis no GitHub.

> [!div class="nextstepaction"]
> [Explore exemplos de C++ no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de idioma](how-to-customize-language-model.md)

---
title: 'Início rápido: Reconhecer fala, C++ (Windows)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer o Speech in C++ no Windows desktop usando o SDK de fala
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: b79d5aeb20301a2e5359ea712017b40ca7279bf0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559541"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Início rápido: Reconhecer a fala C++ no Windows usando o SDK de fala

Os guias de início rápido também estão disponíveis para conversão de [texto em fala](quickstart-text-to-speech-cpp-windows.md) e [de fala](quickstart-translate-speech-cpp-windows.md).

Se desejar, escolha uma linguagem de programação e/ou ambiente diferente:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, vai criar uma aplicação de consola C++ para Windows. Utilize o[SDK de Voz](speech-sdk.md) dos Serviços Cognitivos para a conversão de voz em texto em tempo real, a partir do microfone do seu PC. A aplicação é compilada com o [pacote NuGet de SDK de Voz](https://aka.ms/csspeech/nuget) e o Microsoft Visual Studio 2017 (qualquer edição).

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de uma chave de assinatura dos serviços de fala para concluir este guia de início rápido. Pode obter uma gratuitamente. Consulte [Experimente os serviços de fala gratuitamente](get-started.md) para obter detalhes.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

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

1. É apresentada uma janela de consola que lhe pede para dizer algo. Diga uma expressão ou uma frase em inglês. Sua fala é transmitida para os serviços de fala e transcrita para texto, que aparece na mesma janela.

   ![Captura de ecrã da saída da consola após o reconhecimento bem-sucedido](media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>Passos Seguintes

Exemplos adicionais, como como ler a fala de um arquivo de áudio, estão disponíveis no GitHub.

> [!div class="nextstepaction"]
> [Explorar C++ exemplos no github](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de idioma](how-to-customize-language-model.md)

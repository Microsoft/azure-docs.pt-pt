---
title: 'Início rápido: Traduzir fala, C++ (Windows)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você criará C++ um aplicativo simples para capturar a fala do usuário, convertê-lo em outro idioma e enviar o texto para a linha de comando. Este guia foi projetado para usuários do Windows.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 099d167e14d58f2239b4e86cb01bf0ab3621daa7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553555"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c"></a>Início rápido: Traduza a fala com o SDK de fala paraC++

Os guias de início rápido também estão disponíveis para [reconhecimento de fala](quickstart-cpp-windows.md) e [conversão de texto em fala](quickstart-text-to-speech-cpp-windows.md).

Neste guia de início rápido, você criará C++ um aplicativo simples que captura a fala do usuário do microfone do seu computador, traduz a fala e transcreve o texto traduzido para a linha de comando em tempo real. Este aplicativo foi projetado para ser executado em Windows de 64 bits e é criado com o [pacote NuGet do SDK de fala](https://aka.ms/csspeech/nuget) e Microsoft Visual Studio 2017.

Para obter uma lista completa de idiomas disponíveis para tradução de fala, consulte [suporte a idiomas](language-support.md).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Uma chave de assinatura do Azure para o serviço de fala. [Obtenha um gratuitamente](get-started.md).

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra o ficheiro de origem *helloworld.cpp*. Substitua todo o código abaixo da instrução de inclusão inicial (`#include "stdafx.h"` ou `#include "pch.h"`) pelo seguinte:

    [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/cpp-windows/helloworld/helloworld.cpp#code)]

1. No mesmo ficheiro, substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.

1. Substitua a cadeia de carateres `YourServiceRegion` pela [região](regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

1. Guarde as alterações ao projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

1. Compile a aplicação. Na barra de menus, escolha **Compilar** > **Compilar solução**. O código deve ser compilado sem erros.

   ![Captura de ecrã da aplicação Visual Studio, com a opção Compilar Solução realçada](media/sdk/qs-cpp-windows-06-build.png)

1. Inicie a aplicação. Na barra de menus, escolha **Depurar** > **Iniciar Depuração** ou prima **F5**.

   ![Captura de ecrã da aplicação Visual Studio, com a opção Iniciar Depuração realçada](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. É apresentada uma janela de consola que lhe pede para dizer algo. Diga uma expressão ou uma frase em inglês. Sua fala é transmitida para o serviço de fala, traduzida e transcrita para texto, que aparece na mesma janela.

   ![Captura de tela da saída do console após tradução bem-sucedida](media/sdk/qs-translate-cpp-windows-output.png)

## <a name="next-steps"></a>Passos Seguintes

Exemplos adicionais, como a leitura de fala de um arquivo de áudio e a saída de texto traduzido como fala sintetizada, estão disponíveis no GitHub.

> [!div class="nextstepaction"]
> [Explorar C++ exemplos no github](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de idioma](how-to-customize-language-model.md)

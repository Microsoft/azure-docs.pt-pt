---
title: 'Início rápido: Traduzir fala, C++ (Windows)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você criará um C++ aplicativo para capturar a fala do usuário, convertê-lo em outro idioma e enviar o texto para a linha de comando. Este guia foi projetado para usuários do Windows.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/24/2019
ms.author: erhopf
ms.openlocfilehash: 6ba4e44efc7ff24aa48f9f16840b2248423f7241
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382664"
---
# <a name="quickstart-translate-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Início rápido: Traduzir a fala C++ no Windows usando o SDK de fala

Os guias de início rápido também estão disponíveis para [reconhecimento de fala](quickstart-cpp-windows.md) e [síntese de fala](quickstart-text-to-speech-cpp-windows.md).

Neste guia de início rápido, você criará um C++ aplicativo que captura a fala do usuário do microfone do seu computador, traduz a fala e transcreve o texto traduzido para a linha de comando em tempo real. Esse aplicativo é criado com o [pacote NuGet do SDK de fala](https://aka.ms/csspeech/nuget) e Microsoft Visual Studio 2019 (qualquer edição).

Para obter uma lista completa de idiomas disponíveis para tradução de fala, consulte [suporte a idiomas](language-support.md).

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de uma chave de assinatura dos serviços de fala para concluir este guia de início rápido. Pode obter uma gratuitamente. Consulte [Experimente os serviços de fala gratuitamente](get-started.md) para obter detalhes.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra o ficheiro de origem **helloworld.cpp**.

1. Substitua todo o código pelo trecho a seguir:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/cpp-windows/helloworld/helloworld.cpp?range=2-#code)]

1. No mesmo ficheiro, substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.

1. Substitua a cadeia de carateres `YourServiceRegion` pela [região](regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

1. Na barra de menus, escolha **arquivo** > **salvar tudo**.

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

1. Na barra de menus, selecione **criar** > **solução de compilação** para compilar o aplicativo. Agora o código deverá ser compilado sem erros.

1. Escolha **depurar** > **Iniciar Depuração** (ou pressione **F5**) para iniciar o aplicativo **HelloWorld** .

1. Diga uma expressão ou uma frase em inglês. O aplicativo transmite sua fala para os serviços de fala, que converte e transcreve em texto (neste caso, para francês e alemão). Em seguida, os serviços de fala enviam o texto de volta para o aplicativo para exibição.

   ![Saída do console após tradução de fala bem-sucedida](media/sdk/qs-translate-cpp-windows-output.png)

## <a name="next-steps"></a>Passos Seguintes

Exemplos adicionais, como como ler a fala de um arquivo de áudio ou transformar o texto traduzido em fala sintetizada, estão disponíveis no GitHub.

> [!div class="nextstepaction"]
> [Explorar C++ exemplos no github](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Treinar um modelo para Fala Personalizada](how-to-custom-speech-train-model.md)

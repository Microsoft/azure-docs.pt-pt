---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: db87f31f4702d3487a11a6d833755105455d74c8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400652"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Criar um recurso azure speech<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Crie o seu ambiente de desenvolvimento e crie um projeto vazio](../../../../quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp)
> * Certifique-se de que tem acesso a um microfone para captura de áudio

## <a name="source-code"></a>Código de origem

Crie um ficheiro de origem C++ chamado *helloworld.cpp*e colhe o seguinte código nele.

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-microphone/helloworld/helloworld.cpp#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Explicação de código

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>Construir e executar app

1. A partir da barra de menus, selecione **Build Build** > **Solution** para construir a aplicação. Agora o código deverá ser compilado sem erros.

1. Escolha **Debug** > **Start Debugging** (ou prima <kbd>F5)</kbd>para iniciar a aplicação **Helloworld.**

1. Diga uma expressão ou uma frase em inglês. A aplicação transmite o seu discurso ao serviço da Fala, que transcreve para texto e envia-o de volta para a aplicação para exibição.

   ![Saída de consola após reconhecimento bem sucedido](~/articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
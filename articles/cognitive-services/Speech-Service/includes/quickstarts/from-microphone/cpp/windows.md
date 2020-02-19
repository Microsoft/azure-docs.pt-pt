---
title: 'Quickstart: Reconhecer o discurso C++ a partir de um microfone, (Windows) - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer C++ o discurso no Windows Desktop usando o SDK de Discurso
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: wolfma
ms.openlocfilehash: 5cb7cfc0b4d888406f8574ab620667f160bfa118
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77445620"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * [Criar um recurso de fala azure](../../../../get-started.md)
> * [Configurar o seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=windows)
> * [Criar um projeto de amostra vazia](../../../../quickstarts/create-project.md?tabs=windows)
> * Certifique-se de que tem acesso a um microfone para captura de áudio

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra o ficheiro de origem **helloworld.cpp**.

1. Substitua todo o código pelo seguinte corte:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-microphone/helloworld/helloworld.cpp#code)]

1. No mesmo ficheiro, substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.

1. Substitua a `YourServiceRegion` de cadeia sou da **região** associada à sua subscrição (por exemplo, `westus` para a subscrição de teste gratuito). [](https://aka.ms/speech/sdkregion)

1. Na barra de menus, escolha **File** > **Save All**.

> [!NOTE]
> O SDK do Discurso não irá reconhecer o uso de en-us para a língua, consulte [especificar a linguagem fonte para a fala a texto](../../../../how-to-specify-source-language.md) para obter informações sobre a escolha da língua de origem.

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

1. A partir da barra de menus, selecione **Build** > **Build Solution** para construir a aplicação. Agora o código deverá ser compilado sem erros.

1. Escolha **debug** > **Iniciar depuração** (ou prima **F5)** para iniciar a aplicação **helloworld.**

1. Diga uma expressão ou uma frase em inglês. A aplicação transmite o seu discurso ao serviço da Fala, que transcreve para texto e envia-o de volta para a aplicação para exibição.

   ![Saída de consola após reconhecimento bem sucedido](~/articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]
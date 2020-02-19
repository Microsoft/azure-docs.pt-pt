---
title: 'Quickstart: Reconhecer o discurso C++ de um ficheiro áudio, (Windows) - Serviço de fala'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: erhopf
ms.openlocfilehash: cfe649f56ff57548f6fa711abf67f2925d74a2ea
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77446764"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Criar um recurso azure speech](../../../../get-started.md)
> * [Configurar o seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=windows)
> * [Criar um projeto de amostra vazia](../../../../quickstarts/create-project.md?tabs=windows)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra o ficheiro de origem **helloworld.cpp**.

1. Substitua todo o código pelo seguinte corte:
   
   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-file/helloworld/helloworld.cpp#code)]

1. No mesmo ficheiro, substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.

1. Substitua a `YourServiceRegion` de cadeia sou da **região** associada à sua subscrição (por exemplo, `westus` para a subscrição de teste gratuito). [](https://aka.ms/speech/sdkregion)

1. Substitua a cadeia `whatstheweatherlike.wav` pelo seu próprio nome de ficheiro.

1. Na barra de menus, escolha **File** > **Save All**.

> [!NOTE]
> O SDK do Discurso não irá reconhecer o uso de en-us para a língua, consulte [especificar a linguagem fonte para a fala a texto](../../../../how-to-specify-source-language.md) para obter informações sobre a escolha da língua de origem.

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

1. A partir da barra de menus, selecione **Build** > **Build Solution** para construir a aplicação. Agora o código deverá ser compilado sem erros.

1. Escolha **debug** > **Iniciar depuração** (ou prima **F5)** para iniciar a aplicação **helloworld.**

1. O seu ficheiro áudio é transmitido para o serviço de Fala e a primeira expressão no ficheiro é transcrita para texto, que aparece na mesma janela.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]
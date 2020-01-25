---
title: 'Início rápido: reconhecer a fala de um arquivo C++ de áudio, (Windows)-serviço de fala'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: erhopf
ms.openlocfilehash: cc73464c52fd970f7353f1dfce77b909be969346
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748785"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Criar um recurso de fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=windows)
> * [Criar um projeto de exemplo vazio](../../../../quickstarts/create-project.md?tabs=windows)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra o ficheiro de origem **helloworld.cpp**.

1. Substitua todo o código pelo trecho a seguir:
   
   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-file/helloworld/helloworld.cpp#code)]

1. No mesmo ficheiro, substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.

1. Substitua a cadeia de carateres `YourServiceRegion` pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

1. Substitua a cadeia de caracteres `whatstheweatherlike.wav` pelo seu próprio nome de arquivo.

1. Na barra de menus, escolha **arquivo** > **salvar tudo**.

> [!NOTE]
> O SDK de fala usará como padrão o reconhecimento do uso de en-US para a linguagem, consulte [especificar o idioma de origem de fala para texto](../../../../how-to-specify-source-language.md) para obter informações sobre como escolher o idioma de origem.

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

1. Na barra de menus, selecione **compilar** > **Compilar solução** para compilar o aplicativo. Agora o código deverá ser compilado sem erros.

1. Escolha **depurar** > **Iniciar Depuração** (ou pressione **F5**) para iniciar o aplicativo **HelloWorld** .

1. O arquivo de áudio é transmitido para o serviço de fala e o primeiro expressão no arquivo é transcrita para texto, que aparece na mesma janela.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]
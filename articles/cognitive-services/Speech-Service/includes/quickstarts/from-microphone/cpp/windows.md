---
title: 'Início rápido: reconhecer a fala de um C++ microfone, (Windows)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer o Speech in C++ no Windows desktop usando o SDK de fala
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: wolfma
ms.openlocfilehash: dd31ae7b0fbbd9c411cf1bb511551bfba57a1838
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74796217"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * [Criar um recurso de fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=windows)
> * [Criar um projeto de exemplo vazio](../../../../quickstarts/create-project.md?tabs=windows)
> * Verifique se você tem acesso a um microfone para captura de áudio

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra o ficheiro de origem **helloworld.cpp**.

1. Substitua todo o código pelo trecho a seguir:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-microphone/helloworld/helloworld.cpp#code)]

1. No mesmo ficheiro, substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.

1. Substitua a cadeia de carateres `YourServiceRegion` pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

1. Na barra de menus, escolha **arquivo** > **salvar tudo**.

> [!NOTE]
> O SDK de fala usará como padrão o reconhecimento do uso de en-US para a linguagem, consulte [especificar o idioma de origem de fala para texto](../../../../how-to-specify-source-language.md) para obter informações sobre como escolher o idioma de origem.

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

1. Na barra de menus, selecione **compilar** > **Compilar solução** para compilar o aplicativo. Agora o código deverá ser compilado sem erros.

1. Escolha **depurar** > **Iniciar Depuração** (ou pressione **F5**) para iniciar o aplicativo **HelloWorld** .

1. Diga uma expressão ou uma frase em inglês. O aplicativo transmite sua fala para os serviços de fala, o que transcreve para o texto e o envia de volta para o aplicativo para exibição.

   ![Saída do console após reconhecimento bem-sucedido](~/articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]
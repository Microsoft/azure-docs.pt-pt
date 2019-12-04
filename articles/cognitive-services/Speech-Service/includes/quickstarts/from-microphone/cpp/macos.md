---
title: 'Início rápido: reconhecer a fala de um C++ microfone, (MacOS)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer a fala no C++ MacOS usando o SDK de fala
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 6f937683490df29e39bd2dc0a39cfc76d64ef41d
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74796202"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * [Criar um recurso de fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=macos)
> * [Criar um projeto de exemplo vazio](../../../../quickstarts/create-project.md?tabs=macos)
> * Verifique se você tem acesso a um microfone para captura de áudio

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Crie um ficheiro de origem C++ com o nome `helloworld.cpp` e cole o código seguinte no mesmo.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/macos/from-microphone/helloworld.cpp#code)]

1. Nesse novo arquivo, substitua a cadeia de caracteres `YourSubscriptionKey` pela sua chave de assinatura dos serviços de fala.

1. Substitua a cadeia de carateres `YourServiceRegion` pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

> [!NOTE]
> O SDK de fala usará como padrão o reconhecimento do uso de en-US para a linguagem, consulte [especificar o idioma de origem de fala para texto](../../../../how-to-specify-source-language.md) para obter informações sobre como escolher o idioma de origem.

## <a name="build-the-app"></a>Criar a aplicação

> [!NOTE]
> Certifique-se de introduz os comandos abaixo como uma _única linha de comandos_. A maneira mais fácil de o fazer é copiar o comando com o botão **Copiar** junto a cada comando e, em seguida, colá-lo na sua linha de comandos da shell.

* Execute o comando a seguir para compilar o aplicativo.

  ```sh
  g++ helloworld.cpp -o helloworld --std=c++14 -F${SPEECHSDK_ROOT} -framework MicrosoftCognitiveServicesSpeech
  ```

## <a name="run-the-app"></a>Executar a aplicação

1. Configure o caminho da biblioteca do carregador para apontar para a biblioteca do SDK de Voz.

    ```sh
    export DYLD_FRAMEWORK_PATH="$DYLD_FRAMEWORK_PATH:$SPEECHSDK_ROOT"
    ```

1. Execute a aplicação.

   ```sh
   ./helloworld
   ```

1. Na janela da consola, é apresentado um aviso a pedir-lhe que diga algo. Diga uma expressão ou uma frase em inglês. Sua fala é transmitida para os serviços de fala e transcrita para texto, que aparece na mesma janela.

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]
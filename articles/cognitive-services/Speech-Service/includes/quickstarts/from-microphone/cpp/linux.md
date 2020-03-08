---
title: 'Quickstart: Reconheça o discurso C++ de um microfone, (Linux) - Serviço de fala'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: wolfma
ms.openlocfilehash: 1ba1f2a61a293880e81dde0ce1f93460a555fa62
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925015"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * [Criar um recurso de fala azure](../../../../get-started.md)
> * [Crie o seu ambiente de desenvolvimento e crie um projeto vazio](../../../../quickstarts/setup-platform.md?tabs=linux)
> * Certifique-se de que tem acesso a um microfone para captura de áudio

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Crie um ficheiro de origem C++ com o nome `helloworld.cpp` e cole o código seguinte no mesmo.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/linux/from-microphone/helloworld.cpp#code)]

1. Neste novo ficheiro, substitua a cadeia `YourSubscriptionKey` pela sua chave de subscrição do serviço de Voz.

1. Substitua a `YourServiceRegion` de cadeia sou da **região** associada à sua subscrição (por exemplo, `westus` para a subscrição de teste gratuito). [](https://aka.ms/speech/sdkregion)

> [!NOTE]
> O SDK do Discurso não irá reconhecer o uso de en-us para a língua, consulte [especificar a linguagem fonte para a fala a texto](../../../../how-to-specify-source-language.md) para obter informações sobre a escolha da língua de origem.

## <a name="build-the-app"></a>Criar a aplicação

> [!NOTE]
> Certifique-se de introduz os comandos abaixo como uma _única linha de comandos_. A maneira mais fácil de o fazer é copiar o comando com o botão **Copiar** junto a cada comando e, em seguida, colá-lo na sua linha de comandos da shell.

* Num sistema **x64** (64 bits), execute o comando seguinte para criar a aplicação.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libasound.so.2
  ```

* Num sistema **x86** (32 bits), execute o comando seguinte para criar a aplicação.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libasound.so.2
  ```

* Num sistema **ARM64** (64-bit), execute o seguinte comando para construir a aplicação.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/arm64" -l:libasound.so.2
  ```

## <a name="run-the-app"></a>Executar a aplicação

1. Configure o caminho da biblioteca do carregador para apontar para a biblioteca do SDK de Voz.

   * Num sistema **x64** (64 bits), introduza o comando seguinte.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * Num sistema **x86** (32 bits), introduza o comando seguinte.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

   * Num sistema **ARM64** (64 bits), introduza o seguinte comando.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/arm64"
     ```

1. Execute a aplicação.

   ```sh
   ./helloworld
   ```

1. Na janela da consola, é apresentado um aviso a pedir-lhe que diga algo. Diga uma expressão ou uma frase em inglês. A sua voz é transmitida ao serviço de Voz e convertida para texto que é apresentado na mesma janela.

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]

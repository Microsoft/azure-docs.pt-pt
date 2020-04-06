---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: 8d3f3bfcf1f175d5dbb05e661efbcf28e9c4a974
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671279"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Criar um recurso azure speech<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Crie o seu ambiente de desenvolvimento e crie um projeto vazio](../../../../quickstarts/setup-platform.md?tabs=linux&pivots=programming-language-cpp)
> * Certifique-se de que tem acesso a um microfone para captura de áudio

## <a name="source-code"></a>Código de origem

Crie um ficheiro de origem C++ chamado *helloworld.cpp*e colhe o seguinte código nele.

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/linux/from-microphone/helloworld.cpp#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Explicação de código

[!INCLUDE [code explanation](../code-explanation.md)]

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

[!INCLUDE [footer](../footer.md)]

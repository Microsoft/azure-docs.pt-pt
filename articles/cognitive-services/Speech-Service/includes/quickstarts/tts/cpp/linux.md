---
title: 'Quickstart: Discurso sintetizador, C++ (Linux) - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Aprenda a sintetizar o discurso em C++ no Linux usando o Speech SDK
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: fdc8fc929cef9d5b7db6e24364e94fbefbdbb44e
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671603"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Criar um recurso de fala azure](../../../../get-started.md)
> * [Crie o seu ambiente de desenvolvimento e crie um projeto vazio](../../../../quickstarts/setup-platform.md?tabs=linux&pivots=programming-language-cpp)

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Crie um ficheiro de origem C++ com o nome `helloworld.cpp` e cole o código seguinte no mesmo.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/linux/text-to-speech/helloworld.cpp#code)]

1. Neste novo ficheiro, substitua a cadeia `YourSubscriptionKey` pela sua chave de subscrição do serviço de Voz.

1. Substitua a cadeia de carateres `YourServiceRegion` pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

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

1. Na janela da consola, aparece uma solicitação, levando-o a escrever algum texto. Escreva algumas palavras ou uma frase. O texto que escreveu é transmitido ao serviço da Fala e sintetizado para a fala, que reproduz no seu altifalante.

   ```text
   Type some text that you want to speak...
   > hello
   Speech synthesized to speaker for text [hello]
   Press enter to exit...
   ```

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Consulte também

- [Criar uma voz personalizada](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Gravar amostras de voz personalizadas](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)

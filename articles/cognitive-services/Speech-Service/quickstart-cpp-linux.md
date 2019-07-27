---
title: 'Início rápido: Reconhecer fala, C++ (Linux)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer o Speech in C++ no Linux usando o SDK de fala
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: dfbf40ef422903069352e64340fe5e3cb960a105
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559452"
---
# <a name="quickstart-recognize-speech-in-c-on-linux-by-using-the-speech-sdk"></a>Início rápido: Reconhecer a fala C++ no Linux usando o SDK de fala

Os guias de início rápido também estão disponíveis para [conversão de texto em fala](quickstart-text-to-speech-cpp-linux.md).

Se desejar, alterne para uma linguagem de programação e/ou ambiente diferente:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você cria um C++ aplicativo de console para Linux (Ubuntu 16, 4, Ubuntu 18, 4, Debian 9). Utilize o[SDK de Voz](speech-sdk.md) dos Serviços Cognitivos para a conversão de voz em texto em tempo real, a partir do microfone do seu PC. A aplicação é criada com o [SDK de Voz para Linux](https://aka.ms/csspeech/linuxbinary) e o compilador de C++ da sua distribuição Linux (por exemplo, `g++`).

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de uma chave de assinatura dos serviços de fala para concluir este guia de início rápido. Pode obter uma gratuitamente. Consulte [Experimente os serviços de fala gratuitamente](get-started.md) para obter detalhes.

## <a name="install-speech-sdk"></a>Instalar o SDK de Voz

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

A versão atual do SDK de Voz dos Serviços Cognitivos é `1.6.0`.

O SDK de Voz para Linux pode ser utilizado para criar aplicações de 64 e 32 bits. As bibliotecas e os arquivos de cabeçalho necessários podem ser baixados como um https://aka.ms/csspeech/linuxbinary arquivo tar do.

Transfira e instale o SDK da seguinte forma:

1. Certifique-se de que as dependências do SDK estão instaladas.

   * No Ubuntu:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

   * No Debian 9:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.2 libasound2 wget
     ```

1. Escolha um diretório para o qual os ficheiros do SDK de Voz serão extraídos e defina a variável de ambiente `SPEECHSDK_ROOT` para apontar para esse diretório. Esta variável facilita a referência ao diretório em comandos futuros. Por exemplo, se pretender utilizar o diretório `speechsdk` no diretório-raiz, utilize um comando semelhante ao seguinte:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Crie o diretório se este ainda não existir.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Transfira e extraia o arquivo `.tar.gz` que contém os binários do SDK de Voz:

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. Valide os conteúdos do diretório de nível superior do pacote extraído:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   A lista de diretórios deve conter o aviso de terceiros e os ficheiros de licença, bem como um diretório `include` com ficheiros de cabeçalho (`.h`) e um diretório `lib` com as bibliotecas.

   [!INCLUDE [Linux Binary Archive Content](../../../includes/cognitive-services-speech-service-linuxbinary-content.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Crie um ficheiro de origem C++ com o nome `helloworld.cpp` e cole o código seguinte no mesmo.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-linux/helloworld.cpp#code)]

1. Nesse novo arquivo, substitua a cadeia de `YourSubscriptionKey` caracteres pela sua chave de assinatura dos serviços de fala.

1. Substitua a cadeia de carateres `YourServiceRegion` pela [região](regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

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

1. Execute a aplicação.

   ```sh
   ./helloworld
   ```

1. Na janela da consola, é apresentado um aviso a pedir-lhe que diga algo. Diga uma expressão ou uma frase em inglês. Sua fala é transmitida para os serviços de fala e transcrita para texto, que aparece na mesma janela.

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Explorar C++ exemplos no github](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de idioma](how-to-customize-language-model.md)

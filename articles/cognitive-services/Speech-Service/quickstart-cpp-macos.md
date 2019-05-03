---
title: 'Início rápido: Reconhecer a conversão de voz, C++ (macOS) - serviços de voz'
titleSuffix: Azure Cognitive Services
description: Aprender a reconhecer voz em C++ no macOS com o SDK de voz
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/03/2019
ms.author: wolfma
ms.openlocfilehash: b602a26c83be1ffd4bd55a8eff9ff8d83aac5919
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020819"
---
# <a name="quickstart-recognize-speech-in-c-on-macos-by-using-the-speech-sdk"></a>Início rápido: Reconhecer voz em C++ no macOS com o SDK de voz

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, vai criar uma aplicação de consola C++ para macOS 10.13 e superior. Utiliza os serviços cognitivos [SDK de voz](speech-sdk.md) a transcrição de voz para texto em tempo real do microfone do seu Mac. A aplicação baseia-se com o [SDK de voz para macOS](https://aka.ms/csspeech/macosbinary) e o compilador de C++ do seu Mac predefinido (por exemplo, `g++`).

## <a name="prerequisites"></a>Pré-requisitos

Precisa de uma chave de subscrição de serviços de voz para concluir este início rápido. Pode obter uma gratuitamente. Ver [experimentar gratuitamente os serviços de voz](get-started.md) para obter detalhes.

## <a name="install-speech-sdk"></a>Instalar o SDK de Voz

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

A versão atual do SDK de Voz dos Serviços Cognitivos é `1.5.0`.

O SDK de voz para macOS, pode ser baixado como um pacote zipado framework de https://aka.ms/csspeech/macosbinary.

Transfira e instale o SDK da seguinte forma:

1. Escolha um diretório para o qual os ficheiros do SDK de Voz serão extraídos e defina a variável de ambiente `SPEECHSDK_ROOT` para apontar para esse diretório. Esta variável facilita a referência ao diretório em comandos futuros. Por exemplo, se pretender utilizar o diretório `speechsdk` no diretório-raiz, utilize um comando semelhante ao seguinte:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Crie o diretório se este ainda não existir.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Baixe e extraia o `.zip` arquivo que contém a estrutura do SDK de voz:

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. Valide os conteúdos do diretório de nível superior do pacote extraído:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   A listagem de diretórios deve conter o aviso de terceiros e arquivos de licença, bem como um `MicrosoftCognitiveServicesSpeech.framework` diretório.

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Crie um ficheiro de origem C++ com o nome `helloworld.cpp` e cole o código seguinte no mesmo.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-macos/helloworld.cpp#code)]

1. Neste novo ficheiro, substitua a cadeia de caracteres `YourSubscriptionKey` com a sua chave de subscrição de serviços de voz.

1. Substitua a cadeia de carateres `YourServiceRegion` pela [região](regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

## <a name="build-the-app"></a>Criar a aplicação

> [!NOTE]
> Certifique-se de introduz os comandos abaixo como uma _única linha de comandos_. A maneira mais fácil de o fazer é copiar o comando com o botão **Copiar** junto a cada comando e, em seguida, colá-lo na sua linha de comandos da shell.

* Execute o seguinte comando para criar a aplicação.

  ```sh
  g++ helloworld.cpp -o helloworld --std=c++11 -F${SPEECHSDK_ROOT} -framework MicrosoftCognitiveServicesSpeech
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

1. Na janela da consola, é apresentado um aviso a pedir-lhe que diga algo. Diga uma expressão ou uma frase em inglês. Sua fala é transmitida para os serviços de voz e transcrito para texto, que aparece na janela da mesma.

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Explore exemplos de C++ no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de idioma](how-to-customize-language-model.md)


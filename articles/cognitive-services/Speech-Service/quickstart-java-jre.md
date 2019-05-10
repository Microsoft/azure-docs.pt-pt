---
title: 'Início rápido: Reconhecer a conversão de voz, o Java (Windows, Linux) - serviços de voz'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, irá aprender a criar uma aplicação Java simples que captura e converte voz do utilizador do microfone do seu computador.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: fmegen
ms.openlocfilehash: 05475dbfb7dc1ee3f5de1a9b858eaf212b674eac
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65466380"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-java"></a>Início rápido: Reconhecer a conversão de voz com o SDK de voz para Java

Também estão disponíveis para inícios Rápidos [fala-para--tradução de voz](quickstart-translate-speech-java-jre.md) e [Assistente de voz em primeiro lugar virtual](quickstart-virtual-assistant-java-jre.md).

Se assim o desejar, escolha uma linguagem de programação diferente e/ou o ambiente:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, vai criar uma aplicação de consola Java com o [SDK de Voz](speech-sdk.md). Converta voz em texto em tempo real a partir do microfone do seu PC. A aplicação baseia-se com o pacote Maven do SDK de voz e o IDE de Java de Eclipse (v4.8) no Windows de 64 bits, 64-bit Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9), ou no macOS 10.13 ou posterior. É executada num ambiente de tempo de execução Java 8 de 64 bits (JRE).

> [!NOTE]
> Para o SDK de Dispositivos de Voz e o dispositivo Roobo, veja [SDK de Dispositivos de Voz](speech-devices-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* Sistema operativo: 64-bit Windows, 64 bits do Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9) ou macOS 10.13 ou posterior
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) ou [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Uma chave de subscrição do Azure para o serviço de voz. [Obter uma gratuitamente](get-started.md).

Se estiver a executar o Linux, certifique-se de que estas dependências estão instaladas antes de iniciar o Eclipse.

* On Ubuntu:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* No Debian 9:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

Se estiver a executar Windows (64-bit), certifique-se de que instalou o Microsoft Visual C++ redistribuível para a sua plataforma.
* [Baixe o Microsoft Visual C++ Redistributable para Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="create-and-configure-project"></a>Criar e configurar o projeto

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Para adicionar uma nova classe vazia ao seu projeto Java, selecione **Ficheiro** > **Novo** > **Classe**.

1. Na janela **Nova Classe Java**, introduza **speechsdk.quickstart** no campo **Pacote**, e **Principal** no campo **Nome**.

   ![Captura de ecrã da janela Nova Classe Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Substitua todo o código em `Main.java` pelo seguinte fragmento:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.

1. Substitua a cadeia de carateres `YourServiceRegion` pela [região](regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

1. Guarde as alterações ao projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

Prima F11 ou selecione **Executar** > **Depurar**.
Os próximos 15 segundos de entrada de voz do microfone serão reconhecidos e registados na janela da consola.

![Captura de ecrã da saída da consola após o reconhecimento bem-sucedido](media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>Passos Seguintes

Exemplos adicionais, tais como a leitura de voz de um arquivo de áudio, estão disponíveis no GitHub.

> [!div class="nextstepaction"]
> [Explore exemplos de Java no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Quickstart: Translate speech, Java (Windows, Linux)](quickstart-translate-speech-java-jre.md)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de idioma](how-to-customize-language-model.md)

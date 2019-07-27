---
title: 'Início rápido: Reconhecer fala, Java (Windows, Linux)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você aprenderá a criar um aplicativo Java simples que captura e transcreve a fala do usuário do microfone do seu computador.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 498e41b08133113be9789ef49291b8e2bb0f3705
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68554107"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-java"></a>Início rápido: Reconhecer a fala com o SDK de fala para Java

Os guias de início rápido também estão disponíveis para [conversão de fala em fala](quickstart-translate-speech-java-jre.md) e [assistente virtual de voz primeiro](quickstart-virtual-assistant-java-jre.md).

Se desejar, escolha uma linguagem de programação e/ou ambiente diferente:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, vai criar uma aplicação de consola Java com o [SDK de Voz](speech-sdk.md). Converta voz em texto em tempo real a partir do microfone do seu PC. O aplicativo é criado com o pacote do SDK de fala do Maven e o eclipse Java IDE (v 4.8) em Windows de 64 bits, Linux de 64 bits (Ubuntu 16, 4, Ubuntu 18, 4, Debian 9) ou no macOS 10,13 ou posterior. É executada num ambiente de tempo de execução Java 8 de 64 bits (JRE).

> [!NOTE]
> Para o SDK de Dispositivos de Voz e o dispositivo Roobo, veja [SDK de Dispositivos de Voz](speech-devices-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* Sistema Operacional: Windows de 64 bits, 64-bit Linux (Ubuntu 16, 4, Ubuntu 18, 4, Debian 9) ou macOS 10,13 ou posterior
* [Java IDE Eclipse](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) ou [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Uma chave de assinatura do Azure para o serviço de fala. [Obtenha um gratuitamente](get-started.md).

Se você estiver executando o Linux, verifique se essas dependências estão instaladas antes de iniciar o eclipse.

* No Ubuntu:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* No Debian 9:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

Se você estiver executando o Windows (64 bits), certifique-se de ter instalado C++ o Microsoft Visual redistribuível para sua plataforma.
* [Baixe o Microsoft C++ Visual redistribuível para Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

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

Exemplos adicionais, como como ler a fala de um arquivo de áudio, estão disponíveis no GitHub.

> [!div class="nextstepaction"]
> [Explorar exemplos de Java no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Quickstart: Traduzir fala, Java (Windows, Linux)](quickstart-translate-speech-java-jre.md)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de idioma](how-to-customize-language-model.md)

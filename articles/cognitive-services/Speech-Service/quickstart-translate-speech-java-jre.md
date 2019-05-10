---
title: 'Início rápido: Traduzir a conversão de voz, o Java (Windows, Linux) - serviços de voz'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, irá criar uma aplicação Java simples para capturar a voz do utilizador, convertê-lo em outro idioma e o texto na linha de comando de saída. Este guia foi concebido para utilizadores do Windows e Linux.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 69cb48df4e765250247605fb5c4e1f421554fd20
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467022"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-java"></a>Início rápido: Traduzir a conversão de voz com o SDK de voz para Java

Também estão disponíveis para inícios Rápidos [voz em texto](quickstart-java-jre.md) e [Assistente de voz em primeiro lugar virtual](quickstart-virtual-assistant-java-jre.md).

Neste início rápido, irá criar uma aplicação Java simples que captura a voz do utilizador do microfone do seu computador, traduz-se a conversão de voz e converte o texto traduzido para a linha de comandos em tempo real. Esta aplicação foi concebida para ser executado no Windows de 64 bits ou 64 bits Linux (Ubuntu 16.04, 18.04 Ubuntu, Debian 9), ou no macOS 10.13 ou posterior. Baseia-se com o pacote Maven do SDK de voz e o IDE Java de Eclipse.

Para obter uma lista completa dos idiomas disponíveis para tradução de voz, consulte [suporte de idiomas](language-support.md).

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

> [!NOTE]
> Para o SDK de Dispositivos de Voz e o dispositivo Roobo, veja [SDK de Dispositivos de Voz](speech-devices-sdk.md).

## <a name="create-and-configure-project"></a>Criar e configurar o projeto

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Para adicionar uma nova classe vazia ao seu projeto Java, selecione **Ficheiro** > **Novo** > **Classe**.

1. Na janela **Nova Classe Java**, introduza **speechsdk.quickstart** no campo **Pacote**, e **Principal** no campo **Nome**.

   ![Captura de ecrã da janela Nova Classe Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Substitua todo o código em `Main.java` pelo seguinte fragmento:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.

1. Substitua a cadeia de carateres `YourServiceRegion` pela [região](regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

1. Guarde as alterações ao projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

Prima F11 ou selecione **Executar** > **Depurar**.

A conversão de voz de entrada do microfone será transcrito para o alemão e com sessão iniciada na janela da consola. Prima "Enter" para parar a captura de voz.

![Captura de ecrã da saída da consola após o reconhecimento bem-sucedido](media/sdk/qs-translate-java-jre-output.png)

## <a name="next-steps"></a>Passos Seguintes

Exemplos adicionais, tais como ler voz de um arquivo de áudio e de saída do texto traduzido como fala sintetizada, estão disponíveis no GitHub.

> [!div class="nextstepaction"]
> [Explore exemplos de Java no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Quickstart: Reconhecer a conversão de voz, Java (Windows, Linux)](quickstart-java-jre.md)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de idioma](how-to-customize-language-model.md)

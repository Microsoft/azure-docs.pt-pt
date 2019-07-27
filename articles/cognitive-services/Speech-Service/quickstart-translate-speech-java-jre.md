---
title: 'Início rápido: Traduzir fala, Java (Windows, Linux)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você criará um aplicativo Java simples para capturar a fala do usuário, convertê-lo em outro idioma e enviar o texto para a linha de comando. Este guia foi projetado para usuários do Windows e do Linux.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: cbb86dd4b24cb325b8ea6708ebc2ffc89a697757
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553403"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-java"></a>Início rápido: Traduzir a conversão de voz com o SDK de voz para Java

Os guias de início rápido também estão disponíveis para o assistente virtual de [fala-para-texto](quickstart-java-jre.md) e [voz-primeiro](quickstart-virtual-assistant-java-jre.md).

Neste guia de início rápido, você criará um aplicativo Java simples que captura a fala do usuário do microfone do seu computador, traduz a fala e transcreve o texto traduzido para a linha de comando em tempo real. Este aplicativo foi projetado para ser executado em Windows de 64 bits ou em Linux de 64 bits (Ubuntu 16, 4, Ubuntu 18, 4, Debian 9) ou no macOS 10,13 ou posterior. Ele é criado com o pacote do SDK do Speech e o Java IDE do eclipse.

Para obter uma lista completa de idiomas disponíveis para tradução de fala, consulte [suporte a idiomas](language-support.md).

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

A entrada de fala do microfone será transcrita em alemão e registrada na janela do console. Pressione "Enter" para parar de capturar a fala.

![Captura de ecrã da saída da consola após o reconhecimento bem-sucedido](media/sdk/qs-translate-java-jre-output.png)

## <a name="next-steps"></a>Passos seguintes

Exemplos adicionais, como a leitura de fala de um arquivo de áudio e a saída de texto traduzido como fala sintetizada, estão disponíveis no GitHub.

> [!div class="nextstepaction"]
> [Explorar exemplos de Java no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Quickstart: Reconhecer fala, Java (Windows, Linux)](quickstart-java-jre.md)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de idioma](how-to-customize-language-model.md)

---
title: Criar um tradutor de IoT DevKit com as funções do Azure e os serviços cognitivos | Documentos da Microsoft
description: Utilizar o microfone num IoT DevKit para receber uma mensagem de voz e, em seguida, utilizar os serviços cognitivos do Azure para processá-lo para o texto traduzido em inglês
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 12/19/2018
ms.author: liydu
ms.openlocfilehash: 394cb2d77d77772bd7ead82f0937a186472f5229
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57533379"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>Utilizar o IoT DevKit AZ3166 com as funções do Azure e os serviços cognitivos para tornar um tradutor lingüístico

Neste artigo, saiba como tornar a IoT DevKit como um tradutor lingüístico usando [serviços cognitivos do Azure](https://azure.microsoft.com/services/cognitive-services/). Regista a voz e traduz-se aparecem no ecrã DevKit de texto em inglês.

O [MXChip IoT DevKit](https://aka.ms/iot-devkit) é uma área de compatível de Arduino tudo-em-um com periféricos avançados e sensores. Pode desenvolver para ele usando [Bancada de trabalho de dispositivo do Azure IoT](https://aka.ms/iot-workbench) ou [ferramentas do Azure IoT](https://aka.ms/azure-iot-tools) pacote de extensão no Visual Studio Code. O [catálogo de projetos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) contém aplicativos de exemplo para ajudá-lo a soluções de IoT de protótipo.

## <a name="before-you-begin"></a>Antes de começar

Para concluir os passos neste tutorial, primeiro faça as seguintes tarefas:

* Preparar seu DevKit ao seguir os passos em [ligar IoT DevKit AZ3166 hub do IoT do Azure na cloud](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

## <a name="create-azure-cognitive-service"></a>Criar serviço cognitivo do Azure

1. No portal do Azure, clique em **criar um recurso** e procure **voz**. Preencha o formulário para criar o serviço de voz.
  ![Serviço de voz](media/iot-hub-arduino-iot-devkit-az3166-translator/speech-service.png)

1. Vá para o serviço de voz acabou de criar, clique **chaves** secção para copiar e tome nota do **chave1** para aceder ao DevKit ao mesmo.
  ![Copie chaves](media/iot-hub-arduino-iot-devkit-az3166-translator/copy-keys.png)

## <a name="open-sample-project"></a>Projeto de exemplo aberto

1. Certificar-se de que é o IoT DevKit **não ligado** para o seu computador. Inicie o VS Code primeiro e, em seguida, ligue o DevKit para o seu computador.

1. Clique em `F1` para abrir a paleta de comandos, escreva e selecione **Bancada de trabalho de dispositivo do Azure IoT: Abrir os exemplos...** . Em seguida, selecione **IoT DevKit** como quadro.

1. Na página de exemplos da bancada de trabalho de IoT, encontrar **DevKit Translator** e clique em **exemplo aberto**. Em seguida, seleciona o caminho predefinido para transferir o código de exemplo.
  ![Exemplo aberto](media/iot-hub-arduino-iot-devkit-az3166-translator/open-sample.png)

## <a name="use-speech-service-with-azure-functions"></a>Utilizar o serviço de voz com as funções do Azure

1. No VS Code, clique em `F1`, introduza e selecione **Bancada de trabalho de dispositivo do Azure IoT: Aprovisionar serviços do Azure...** . ![Aprovisionar serviços do Azure](media/iot-hub-arduino-iot-devkit-az3166-translator/provision.png)

1. Siga os passos para concluir o aprovisionamento do IoT Hub do Azure e as funções do Azure.
  ![Passos de aprovisionamento](media/iot-hub-arduino-iot-devkit-az3166-translator/provision-steps.png)

  Tome nota do nome de dispositivo do IoT Hub do Azure que criou.

1. Abra `Functions\DevKitTranslatorFunction.cs` e Atualize as seguintes linhas de código com o nome do dispositivo e a chave de serviço de voz que anotou para baixo.
  ```csharp
  // Subscription Key of Speech Service
  const string speechSubscriptionKey = "";

  // Region of the speech service, see https://docs.microsoft.com/azure/cognitive-services/speech-service/regions for more details.
  const string speechServiceRegion = "";

  // Device ID
  const string deviceName = "";
  ```

1. Clique em `F1`, introduza e selecione **Bancada de trabalho de dispositivo do Azure IoT: Implemente no Azure...** . Se o código de VS solicita a confirmação para reimplantação, clique em **Sim**.
  ![Implementar o aviso](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-warning.png)

1. Certifique-se de que a implementação é efetuada com êxito.
  ![Implementar com êxito](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-success.png)

1. No portal do Azure, aceda a **aplicações de funções** secção, localize a aplicação de função do Azure que acabou de criar. Clique em `devkit_translator`, em seguida, clique em **<> / obter URL da função** para copiar o URL.
  ![Copie o URL da função](media/iot-hub-arduino-iot-devkit-az3166-translator/get-function-url.png)

1. Cole o URL em `azure_config.h` ficheiro.
  ![Azure config](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-config.png)

  > [!NOTE]
  > Se a aplicação de funções não funcionar corretamente, verifique isso [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) secção para resolvê-lo.

## <a name="build-and-upload-device-code"></a>Criar e carregar o código de dispositivo

1. Mudar o DevKit ao **modo de configuração** por:
  * Mantenha premida botão **A**.
  * Prima e versão **repor** botão.

  Verá a tela exibe o ID de DevKit e **configuração**.

  ![Modo de configuração de DevKit](media/iot-hub-arduino-iot-devkit-az3166-translator/devkit-configuration-mode.png)

1. Clique em `F1`, introduza e selecione **Bancada de trabalho de dispositivo do Azure IoT: Configurar as definições de dispositivos... > cadeia de ligação do dispositivo de configuração**. Selecione **selecionar o IoT Hub dispositivo Connection String** para configurá-lo para o DevKit.
  ![Configurar a cadeia de ligação](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string.png)

1. Verá a notificação depois de estar concluído com êxito.
  ![Configurar o sucesso da cadeia de ligação](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string-success.png)

1. Clique em `F1` novamente, escreva e selecione **Bancada de trabalho de dispositivo do Azure IoT: Carregar o código de dispositivo**. Ele começa a compilação e carregar o código para DevKit.
  ![Carregamento de dispositivo](media/iot-hub-arduino-iot-devkit-az3166-translator/device-upload.png)

## <a name="test-the-project"></a>Testar o projeto

Após a inicialização de aplicação, siga as instruções no ecrã DevKit. O idioma de origem do padrão é chinês.

Para selecionar outro idioma para tradução:

1. Prima A de botão para entrar no modo de configuração.

2. Prima o botão B de se deslocar para todos com suporte de idiomas de origem.

3. Pressione um botão para confirmar a sua escolha de idioma de origem.

4. Prima e mantenha premido o botão B ao mesmo tempo falando, em seguida, B de botão para iniciar a tradução de versão.

5. O texto traduzido em inglês mostra no ecrã.

![Desloque-se para selecionar idioma](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![Resultado de tradução](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

No ecrã do resultado da conversão, pode:

- Prima botões A e B, desloque-se e selecione o idioma de origem.

- Prima o botão de B para falar. Para enviar a voz e obter o texto de tradução, largue o botão de B.

## <a name="how-it-works"></a>Como funciona

![mini-Solution-Voice-to-tweet-Diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

O IoT DevKit regista a voz, em seguida, publica uma solicitação HTTP para acionar as funções do Azure. As funções do Azure chama o tradutor de voz do serviço cognitivo API para fazer a tradução. Depois das funções do Azure obtém o texto de tradução, envia uma mensagem de C2D no dispositivo. Em seguida, a tradução é apresentada no ecrã.

## <a name="problems-and-feedback"></a>Problemas e comentários

Se tiver problemas, consulte a [IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou contacte-nos com os seguintes canais:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passos Seguintes

Aprendeu como utilizar o IoT DevKit como um tradutor ao utilizar as funções do Azure e os serviços cognitivos. Este procedimento, ficou a saber como:

> [!div class="checklist"]
> * Utilizar tarefas do Visual Studio Code para automatizar as provisões de cloud
> * Configurar a cadeia de ligação do dispositivo IoT do Azure
> * Implementar a função do Azure
> * Testar a tradução de mensagem de voz

Avance para os outros tutoriais para saber mais:

> [!div class="nextstepaction"]
> [Ligar a IoT DevKit AZ3166 ao solution accelerator do monitoramento remoto do Azure IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)

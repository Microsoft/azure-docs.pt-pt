---
title: Faça tradutor c/Azure Funções, Serviços Cognitivos, IoT DevKit
description: Utilize o microfone num IoT DevKit para receber uma mensagem de voz e, em seguida, use os Serviços Cognitivos Azure para processá-lo em texto traduzido em inglês
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 12/19/2018
ms.author: liydu
ms.openlocfilehash: 8e840a1ae7161ea3e7b370889a1f0fb648ca120e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953363"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>Use IoT DevKit AZ3166 com Funções Azure e Serviços Cognitivos para fazer um tradutor de línguas

Neste artigo, aprende-se a fazer do IoT DevKit como tradutor de línguas utilizando [os Serviços Cognitivos Azure.](https://azure.microsoft.com/services/cognitive-services/) Grava a sua voz e traduz-a para texto inglês mostrado no ecrã DevKit.

O [MXChip IoT DevKit](https://aka.ms/iot-devkit) é uma placa compatível com Arduino com periféricos ricos e sensores. Pode desenvolver-se para ele utilizando a bancada de trabalho do [dispositivo Azure IoT](https://aka.ms/iot-workbench) ou o pacote de extensão [Azure IoT Tools](https://aka.ms/azure-iot-tools) no Código do Estúdio Visual. O catálogo de [projetos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) contém aplicações de amostra para ajudá-lo a protótipo de soluções IoT.

## <a name="before-you-begin"></a>Antes de começar

Para completar os passos neste tutorial, faça primeiro as seguintes tarefas:

* Prepare o seu DevKit seguindo os passos do [Connect IoT DevKit AZ3166 para o Azure IoT Hub na nuvem](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

## <a name="create-azure-cognitive-service"></a>Criar serviço cognitivo azure

1. No portal Azure, clique em **Criar um recurso** e procurar o **Discurso.** Preencha o formulário para criar o Serviço de Fala.
  ![Serviço de voz](media/iot-hub-arduino-iot-devkit-az3166-translator/speech-service.png)

1. Vá ao serviço de Fala que acabou de criar, clique na secção **Keys** para copiar e anote a **Chave1** para o DevKit aceder ao mesmo.
  ![Chaves de cópia](media/iot-hub-arduino-iot-devkit-az3166-translator/copy-keys.png)

## <a name="open-sample-project"></a>Projeto de amostra aberta

1. Certifique-se de que o seu IoT DevKit não está **ligado** ao computador. Inicie primeiro o Código VS e, em seguida, ligue o DevKit ao seu computador.

1. Clique `F1` para abrir a paleta de comando, digite e selecione Bancada de Trabalho do **Dispositivo Azure IoT: Exemplos abertos...**. Em seguida, selecione **IoT DevKit** como placa.

1. Na página IoT Workbench Exemplos, encontre **o Tradutor DevKit** e clique em **Amostra Aberta**. Em seguida, seleciona o caminho predefinido para descarregar o código da amostra.
  ![Amostra aberta](media/iot-hub-arduino-iot-devkit-az3166-translator/open-sample.png)

## <a name="use-speech-service-with-azure-functions"></a>Use o Serviço de Fala com Funções Azure

1. No Código VS, clique em escrever `F1`, digitar e selecione Bancada de Trabalho do Dispositivo **Azure IoT: Serviços De Provision Azure...**. ![Serviços De prestação de serviços Azure](media/iot-hub-arduino-iot-devkit-az3166-translator/provision.png)

1. Siga os passos para terminar o fornecimento das funções Azure IoT Hub e Azure.
   ![Medidas de provisão](media/iot-hub-arduino-iot-devkit-az3166-translator/provision-steps.png)

   Tome nota do nome do dispositivo Azure IoT Hub que criou.

1. Abra `Functions\DevKitTranslatorFunction.cs` e atualize as seguintes linhas de código com o nome do dispositivo e a tecla Do Serviço de Fala que você observou.
   ```csharp
   // Subscription Key of Speech Service
   const string speechSubscriptionKey = "";

   // Region of the speech service, see https://docs.microsoft.com/azure/cognitive-services/speech-service/regions for more details.
   const string speechServiceRegion = "";

   // Device ID
   const string deviceName = "";
   ```

1. Clique, escreva `F1`e selecione bancada de trabalho do **dispositivo Azure IoT: Desdobre para Azure...**. Se o Código VS pedir confirmação para redistribuição, clique **em Sim**.
   ![Lançar aviso](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-warning.png)

1. Certifique-se de que o destacamento é bem sucedido.
   ![Implementar sucesso](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-success.png)

1. No portal Azure, vá à secção **'Apps' de funções,** encontre a app Azure Function acabade ser criada. Clique `devkit_translator`e, em seguida, clique em **</> Obter URL de função** para copiar o URL.
   ![URL de função de cópia](media/iot-hub-arduino-iot-devkit-az3166-translator/get-function-url.png)

1. Colá-lo `azure_config.h` no ficheiro.
   ![Azure config](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-config.png)

   > [!NOTE]
   > Se a aplicação Função não funcionar corretamente, verifique esta secção [de PERGUNTAS FREQUENTEs](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) para resolvê-la.

## <a name="build-and-upload-device-code"></a>Construir e carregar código de dispositivo

1. Mude o DevKit para o modo de **configuração** por:
   * Segure o botão **A**.
   * Prima e liberte o botão **Reset.**

   Verá que o ecrã exibe o ID e **configuração**de DevKit .

   ![Modo de configuração DevKit](media/iot-hub-arduino-iot-devkit-az3166-translator/devkit-configuration-mode.png)

1. Clique, escreva `F1`e selecione bancada de trabalho do **dispositivo Azure IoT: Configurar as definições do dispositivo... > Cadeia de Ligação ao Dispositivo Config**. **Selecione Selecione IoT Hub Device Connection String** para configurá-lo para o DevKit.
   ![Configurar a cadeia de ligação](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string.png)

1. Verá a notificação assim que for feita com sucesso.
   ![Configurar o sucesso da cadeia de ligação](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string-success.png)

1. Clique `F1` novamente, escreva e selecione bancada de trabalho do **dispositivo Azure IoT: Carregar código do dispositivo**. Começa a compilar e a enviar o código para o DevKit.
   ![Upload do dispositivo](media/iot-hub-arduino-iot-devkit-az3166-translator/device-upload.png)

## <a name="test-the-project"></a>Testar o projeto

Após a inicialização da aplicação, siga as instruções no ecrã DevKit. A língua de origem padrão é o chinês.

Para selecionar outro idioma para tradução:

1. Prima o botão A para introduzir o modo de configuração.

2. Prima o botão B para deslocar todas as línguas de origem suportadas.

3. Prima o botão A para confirmar a sua escolha de linguagem fonte.

4. Pressione e segure o botão B durante a intervenção e, em seguida, liberte o botão B para iniciar a tradução.

5. O texto traduzido em inglês mostra no ecrã.

![Percorra para selecionar o idioma](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![Resultado da tradução](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

No ecrã de resultados da tradução, pode:

- Pressione os botões A e B para deslocar e selecionar a linguagem fonte.

- Pressione o botão B para falar. Para enviar a voz e obter o texto de tradução, liberte o botão B.

## <a name="how-it-works"></a>Como funciona

![mini-solução-voz-tweet-diagrama](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

O IoT DevKit grava a sua voz e depois publica um pedido HTTP para acionar funções Azure. A Azure Functions chama o tradutor de discurso de serviço cognitivo API para fazer a tradução. Depois de o Azure Functions receber o texto de tradução, envia uma mensagem C2D para o dispositivo. Em seguida, a tradução é exibida no ecrã.

## <a name="problems-and-feedback"></a>Problemas e feedback

Se encontrar problemas, consulte o [IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou contacte-nos utilizando os seguintes canais:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passos seguintes

Aprendeu a usar o IoT DevKit como tradutor utilizando funções azure e serviços cognitivos. Neste como-fazer, aprendeu a:

> [!div class="checklist"]
> * Utilize a tarefa do Código do Estúdio Visual para automatizar as provisões em nuvem
> * Configure cadeia de ligação do dispositivo Azure IoT
> * Implementar a função Azure
> * Testar a tradução da mensagem de voz

Avance para os outros tutoriais para aprender:

> [!div class="nextstepaction"]
> [Ligue ioT DevKit AZ3166 ao acelerador de solução de monitorização remota Azure IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)

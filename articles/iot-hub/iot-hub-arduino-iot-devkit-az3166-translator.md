---
title: Fazer funções de tradutor c/Azure, Serviços Cognitivos, IoT DevKit
description: Use o microfone num IoT DevKit para receber uma mensagem de voz e, em seguida, use os Serviços Cognitivos Azure para processá-lo em texto traduzido em inglês
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 12/19/2018
ms.author: liydu
ms.custom: devx-track-csharp
ms.openlocfilehash: be26c6fe03dac9b9ff9dbff4a2bdce391ec0837e
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147863"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>Use IoT DevKit AZ3166 com Funções Azure e Serviços Cognitivos para fazer um tradutor de idiomas

Neste artigo, você aprende a fazer IoT DevKit como tradutor de linguagem usando [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/). Grava a sua voz e traduz-a para texto em inglês mostrado no ecrã DevKit.

O [MXChip IoT DevKit](https://aka.ms/iot-devkit) é um tabuleiro compatível com Arduino com periféricos e sensores ricos. Você pode desenvolver para ele usando [a bancada de trabalho do dispositivo Azure IoT](https://aka.ms/iot-workbench) ou o pacote de extensão [Azure IoT Tools](https://aka.ms/azure-iot-tools) em Visual Studio Code. O [catálogo de projetos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) contém aplicações de amostras para ajudá-lo a protótipo soluções IoT.

## <a name="before-you-begin"></a>Antes de começar

Para completar os passos neste tutorial, primeiro faça as seguintes tarefas:

* Prepare o seu DevKit seguindo os passos no [Connect IoT DevKit AZ3166 para Azure IoT Hub na nuvem](./iot-hub-arduino-iot-devkit-az3166-get-started.md).

## <a name="create-azure-cognitive-service"></a>Criar Serviço Cognitivo Azure

1. No portal Azure, clique em **Criar um recurso** e procurar por **Discurso.** Preencha o formulário para criar um Serviço de Voz.
  ![Serviço de voz](media/iot-hub-arduino-iot-devkit-az3166-translator/speech-service.png)

1. Vá ao serviço Desemaste que acabou de criar, clique na secção **Chaves** para copiar e anote a **Chave1** para o DevKit aceder ao mesmo.
  ![Chaves de cópia](media/iot-hub-arduino-iot-devkit-az3166-translator/copy-keys.png)

## <a name="open-sample-project"></a>Projeto de amostra aberta

1. Certifique-se de que o seu IoT DevKit não está **ligado** ao computador. Inicie primeiro o Código VS e, em seguida, ligue o DevKit ao seu computador.

1. Clique `F1` para abrir a paleta de comando, escreva e selecione **Azure IoT Device Workbench: Open Examples...**. Em seguida, selecione **IoT DevKit** como placa.

1. Na página IoT Workbench Examples, encontre o **Tradutor DevKit** e clique em **Open Sample**. Em seguida, seleciona o caminho predefinido para descarregar o código de amostra.
  ![Amostra aberta](media/iot-hub-arduino-iot-devkit-az3166-translator/open-sample.png)

## <a name="use-speech-service-with-azure-functions"></a>Utilizar o serviço de fala com funções Azure

1. Em Código VS, `F1` clique, escreva e selecione **a bancada de trabalho do dispositivo Azure IoT: Provision Azure Services...**. ![ Prestação de serviços Azure](media/iot-hub-arduino-iot-devkit-az3166-translator/provision.png)

1. Siga os passos para terminar o provisionamento do Azure IoT Hub e das Funções Azure.
   ![Etapas de provisão](media/iot-hub-arduino-iot-devkit-az3166-translator/provision-steps.png)

   Anote o nome do dispositivo Hub IoT do Azure que criou.

1. Abra `Functions\DevKitTranslatorFunction.cs` e atualize as seguintes linhas de código com o nome do dispositivo e a tecla de Serviço de Fala que anotado.
   ```csharp
   // Subscription Key of Speech Service
   const string speechSubscriptionKey = "";

   // Region of the speech service, see https://docs.microsoft.com/azure/cognitive-services/speech-service/regions for more details.
   const string speechServiceRegion = "";

   // Device ID
   const string deviceName = "";
   ```

1. Clique, `F1` escreva e selecione **a bancada de trabalho do dispositivo Azure IoT: Implementar para Azure...**. Se o Código VS pedir confirmação para a redistribuição, clique em **Sim**.
   ![Alerta de implantação](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-warning.png)

1. Certifique-se de que a implementação foi concluída com êxito.
   ![Implementar sucesso](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-success.png)

1. No portal Azure, vá à secção **De Aplicações de Funções,** encontre a aplicação Azure Function acaba de ser criada. Clique, `devkit_translator` clique ** em</> Obter URL de função** para copiar o URL.
   ![URL de função de cópia](media/iot-hub-arduino-iot-devkit-az3166-translator/get-function-url.png)

1. Cole o URL em `azure_config.h` ficheiro.
   ![Azure config](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-config.png)

   > [!NOTE]
   > Se a aplicação Função não funcionar corretamente, verifique esta secção [de PERGUNTAS Frequentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) para resolvê-la.

## <a name="build-and-upload-device-code"></a>Construir e carregar código de dispositivo

1. Mude o DevKit para **o modo de configuração** por:
   * Mantenha premir o botão **A**.
   * Pressione e liberte o botão **Reset.**

   Verá que o ecrã apresenta o ID e **a Configuração**de DevKit .

   ![Modo de configuração DevKit](media/iot-hub-arduino-iot-devkit-az3166-translator/devkit-configuration-mode.png)

1. Clique `F1` em , escreva e selecione **Azure IoT Device Workbench: Configurações do dispositivo de configuração... > cadeia de ligação do dispositivo Config**. **Selecione Selecione a cadeia de ligação do dispositivo do hub IoT** para configucioná-la ao DevKit.
   ![Cadeia de ligação configurada](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string.png)

1. Verá a notificação assim que for feita com sucesso.
   ![Configurar o sucesso das cordas de ligação](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string-success.png)

1. Clique `F1` novamente, escreva e selecione **a bancada de trabalho do dispositivo Azure IoT: Código do dispositivo de upload**. Começa a compilar e a enviar o código para o DevKit.
   ![Upload do dispositivo](media/iot-hub-arduino-iot-devkit-az3166-translator/device-upload.png)

## <a name="test-the-project"></a>Testar o projeto

Após a inicialização da aplicação, siga as instruções no ecrã DevKit. O idioma de origem predefinido é o chinês.

Para selecionar outro idioma para tradução:

1. Prima o botão A para entrar no modo de configuração.

2. Prima o botão B para percorrer todos os idiomas de origem suportados.

3. Prima o botão A para confirmar a sua escolha de linguagem de origem.

4. Mantenha o botão B premido enquanto fala e, em seguida, solte o botão B para iniciar a tradução.

5. O texto traduzido em inglês é apresentado no ecrã.

![Desloque-se para selecionar o idioma](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![Resultado da tradução](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

No ecrã de resultados da tradução, pode:

- Premir os botões A e B para percorrer e selecionar o idioma de origem.

- Premir o botão B para falar. Para enviar a voz e obter o texto traduzido, solte o botão B.

## <a name="how-it-works"></a>Como funciona

![mini-solução-voz-tweet-diagrama](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

O IoT DevKit grava a sua voz e publica um pedido HTTP para ativar as Funções Azure. Azure Functions chama o tradutor de fala de serviço cognitivo API para fazer a tradução. Depois de o Azure Functions receber o texto de tradução, envia uma mensagem C2D para o dispositivo. Em seguida, a tradução é apresentada no ecrã.

## <a name="problems-and-feedback"></a>Problemas e feedback

Se encontrar problemas, consulte o [IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou contacte-nos utilizando os seguintes canais:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passos seguintes

Aprendeu a usar o IoT DevKit como tradutor utilizando funções Azure e Serviços Cognitivos. Neste como fazer, aprendeu a:

> [!div class="checklist"]
> * Utilize a tarefa Visual Studio Code para automatizar provisões em nuvem
> * Configurar a cadeia de ligação do dispositivo Azure IoT
> * Implementar a Função Azure
> * Teste a tradução da mensagem de voz

Avance para os outros tutoriais para aprender:

> [!div class="nextstepaction"]
> [Ligue o acelerador de solução de monitorização remota IoT DevKit AZ3166 a Azure IoT](./iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring.md)
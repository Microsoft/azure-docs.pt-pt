---
title: Tornar o tradutor w/Azure Functions, serviços cognitivas, IoT DevKit
description: Use o microfone em um DevKit IoT para receber uma mensagem de voz e, em seguida, use os serviços cognitivas do Azure para processá-las no texto traduzido em inglês
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 12/19/2018
ms.author: liydu
ms.openlocfilehash: 8e840a1ae7161ea3e7b370889a1f0fb648ca120e
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953363"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>Usar IoT DevKit AZ3166 com Azure Functions e serviços cognitivas para criar um tradutor de idioma

Neste artigo, você aprenderá a tornar o IoT DevKit como um tradutor de idioma usando os [Serviços cognitivas do Azure](https://azure.microsoft.com/services/cognitive-services/). Ele registra sua voz e a traduz para o texto em Inglês mostrado na tela DevKit.

O [MXChip IOT devkit](https://aka.ms/iot-devkit) é uma placa compatível com Arduino tudo em um com periféricos avançados e sensores. Você pode desenvolver para ele usando o [Azure IOT Device Workbench](https://aka.ms/iot-workbench) ou o pacote de extensão das [Ferramentas do azure IOT](https://aka.ms/azure-iot-tools) no Visual Studio Code. O [Catálogo de projetos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) contém aplicativos de exemplo para ajudá-lo a criar protótipos de soluções de IOT.

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas deste tutorial, primeiro execute as seguintes tarefas:

* Prepare seu DevKit seguindo as etapas em [conectar IOT DEVKIT AZ3166 ao Hub IOT do Azure na nuvem](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

## <a name="create-azure-cognitive-service"></a>Criar serviço de cognitiva do Azure

1. No portal do Azure, clique em **criar um recurso** e pesquise por **fala**. Preencha o formulário para criar o serviço de fala.
  ](media/iot-hub-arduino-iot-devkit-az3166-translator/speech-service.png) do ![Speech Service

1. Vá para o serviço de fala que você acabou de criar, clique na seção **chaves** para copiar e anote a tecla **key1** para que o devkit acesse a mesma.
  ![copiar chaves](media/iot-hub-arduino-iot-devkit-az3166-translator/copy-keys.png)

## <a name="open-sample-project"></a>Abrir projeto de exemplo

1. Verifique se o DevKit de IoT **não está conectado** ao seu computador. Inicie VS Code primeiro e, em seguida, conecte o DevKit ao seu computador.

1. Clique em `F1` para abrir a paleta de comandos, digite e selecione **Azure IOT Device Workbench: abrir exemplos...** . Em seguida, selecione **IOT devkit** como placa.

1. Na página exemplos do IoT Workbench, encontre o **Tradutor devkit** e clique em **abrir exemplo**. Em seguida, seleciona o caminho padrão para baixar o código de exemplo.
  ![abrir](media/iot-hub-arduino-iot-devkit-az3166-translator/open-sample.png) de exemplo

## <a name="use-speech-service-with-azure-functions"></a>Usar o serviço de fala com Azure Functions

1. Em VS Code, clique em `F1`, digite e selecione **Azure IOT Device Workbench: provisionar serviços do Azure...** . ![provisionar serviços do Azure](media/iot-hub-arduino-iot-devkit-az3166-translator/provision.png)

1. Siga as etapas para concluir o provisionamento do Hub IoT do Azure e Azure Functions.
   etapas de provisionamento de ![](media/iot-hub-arduino-iot-devkit-az3166-translator/provision-steps.png)

   Anote o nome do dispositivo do Hub IoT do Azure que você criou.

1. Abra `Functions\DevKitTranslatorFunction.cs` e atualize as linhas de código a seguir com o nome do dispositivo e a chave do serviço de fala anotada.
   ```csharp
   // Subscription Key of Speech Service
   const string speechSubscriptionKey = "";

   // Region of the speech service, see https://docs.microsoft.com/azure/cognitive-services/speech-service/regions for more details.
   const string speechServiceRegion = "";

   // Device ID
   const string deviceName = "";
   ```

1. Clique em `F1`, digite e selecione **Azure IOT Device Workbench: implantar no Azure...** . Se VS Code solicitar confirmação para reimplantação, clique em **Sim**.
   ![](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-warning.png) de aviso de implantação

1. Verifique se a implantação foi bem-sucedida.
   ![implantar êxito](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-success.png)

1. Em portal do Azure, vá para função aplicativos seção, localize o aplicativo de **funções** do Azure que acabou de ser criado. Clique em `devkit_translator`, em seguida, clique em **</> obter URL de função** para copiar a URL.
   ![URL da função de cópia](media/iot-hub-arduino-iot-devkit-az3166-translator/get-function-url.png)

1. Cole a URL no arquivo `azure_config.h`.
   ![](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-config.png) de configuração do Azure

   > [!NOTE]
   > Se o aplicativo de funções não funcionar corretamente, consulte esta seção de [perguntas frequentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) para resolvê-lo.

## <a name="build-and-upload-device-code"></a>Compilar e carregar o código do dispositivo

1. Alterne o DevKit para o **modo de configuração** por:
   * Mantenha o botão **A**.
   * Pressione e solte o botão **Redefinir** .

   Você verá que a tela exibe a ID e a **configuração**do devkit.

   ![Modo de configuração do DevKit](media/iot-hub-arduino-iot-devkit-az3166-translator/devkit-configuration-mode.png)

1. Clique em `F1`, digite e selecione **Azure IOT Device Workbench: definir configurações do dispositivo... Cadeia de conexão do dispositivo > config**. Selecione **selecionar cadeia de conexão do dispositivo do Hub IOT** para configurá-lo para o devkit.
   ![configurar a cadeia de conexão](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string.png)

1. Você verá a notificação assim que ela for concluída com êxito.
   ![configurar o êxito da cadeia de conexão](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string-success.png)

1. Clique em `F1` novamente, digite e selecione **Azure IOT Device Workbench: carregar o código do dispositivo**. Ele inicia a compilação e carrega o código para DevKit.
   ](media/iot-hub-arduino-iot-devkit-az3166-translator/device-upload.png) de carregamento de dispositivo ![

## <a name="test-the-project"></a>Testar o projeto

Após a inicialização do aplicativo, siga as instruções na tela DevKit. O idioma de origem padrão é chinês.

Para selecionar outro idioma para tradução:

1. Pressione o botão A para entrar no modo de instalação.

2. Pressione o botão B para rolar todos os idiomas de origem com suporte.

3. Pressione o botão A para confirmar sua escolha de idioma de origem.

4. Pressione e segure o botão B enquanto fala e, em seguida, solte o botão B para iniciar a tradução.

5. O texto traduzido em inglês é mostrado na tela.

![Rolar para o idioma selecionado](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![Resultado da tradução](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

Na tela resultado da tradução, você pode:

- Pressione os botões A e B para rolar e selecionar o idioma de origem.

- Pressione o botão B para falar. Para enviar a voz e obter o texto de tradução, solte o botão B.

## <a name="how-it-works"></a>Como funciona

![mini-solução-voz-para-tweet-diagrama](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

O IoT DevKit registra sua voz e, em seguida, posta uma solicitação HTTP para disparar Azure Functions. Azure Functions chama a API do tradutor de fala do serviço cognitiva para fazer a tradução. Depois que Azure Functions Obtém o texto de tradução, ele envia uma mensagem C2D para o dispositivo. Em seguida, a tradução é exibida na tela.

## <a name="problems-and-feedback"></a>Problemas e comentários

Se você tiver problemas, consulte as [perguntas frequentes do IOT devkit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou entre em contato conosco usando os seguintes canais:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passos seguintes

Você aprendeu a usar o IoT DevKit como um tradutor usando Azure Functions e serviços cognitivas. Neste "como", você aprendeu a:

> [!div class="checklist"]
> * Use Visual Studio Code tarefa para automatizar provisões de nuvem
> * Configurar a cadeia de conexão do dispositivo IoT do Azure
> * Implantar a função do Azure
> * Testar a tradução de mensagens de voz

Avance para os outros tutoriais para aprender:

> [!div class="nextstepaction"]
> [Conectar o IoT DevKit AZ3166 ao acelerador de solução de monitoramento remoto de IoT do Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)

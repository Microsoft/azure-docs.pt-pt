---
title: IoT DevKit para nuvem – conectar IoT MXChip DevKit ao Hub IoT do Azure | Microsoft Docs
description: Neste tutorial, saiba como enviar o status de sensores no IoT DevKit AZ3166 para o acelerador de solução de monitoramento remoto de IoT do Azure.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: 24e31bfa916df969368dce736cf841ed4fdfe2c5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73484043"
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-remote-monitoring-solution-accelerator"></a>Conectar o MXChip IoT DevKit ao acelerador de solução de monitoramento remoto do Azure IoT

Neste tutorial, você aprenderá a executar um aplicativo de exemplo em seu DevKit para enviar dados de sensor para o acelerador de solução de monitoramento remoto de IoT do Azure.

O [MXChip IOT devkit](https://aka.ms/iot-devkit) é uma placa compatível com Arduino tudo em um com periféricos avançados e sensores. Você pode desenvolver para ele usando a [extensão Visual Studio Code para Arduino](https://aka.ms/arduino). E vem com um catálogo de [projetos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) crescente para orientá-lo em soluções de Internet das coisas de protótipos (IOT) que aproveitam os serviços Microsoft Azure.

## <a name="what-you-need"></a>Do que precisa

Conclua o [Guia de introdução](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) para:

* Ter seu DevKit conectado ao Wi-Fi
* Preparar o ambiente de desenvolvimento

Uma subscrição ativa do Azure. Se você não tiver um, poderá se registrar por meio de um destes dois métodos:

* Ativar uma [avaliação gratuita de 30 dias Microsoft Azure conta](https://azure.microsoft.com/free/)

* Solicite seu [crédito do Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) se você for assinante do MSDN ou do Visual Studio

## <a name="create-an-azure-iot-remote-monitoring-solution-accelerator"></a>Criar um acelerador de solução de monitoramento remoto de IoT do Azure

1. Acesse o [site do Azure IOT Solution Accelerators](https://www.azureiotsolutions.com/) e clique em **criar uma nova solução**.

   ![Selecione o tipo de acelerador de solução do Azure IoT](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)

   > [!WARNING]
   > Por padrão, este exemplo cria um hub IoT S2 depois de criar um acelerador de solução de monitoramento remoto de IoT. Se esse Hub IoT não for usado com um grande número de dispositivos, é altamente recomendável fazer o downgrade de S2 para S1 e excluir o acelerador de solução de monitoramento remoto de IoT para que o Hub IoT relacionado também possa ser excluído, quando você não precisar mais dele. 

2. Selecione **monitoramento remoto**.

3. Insira um nome de solução, selecione uma assinatura e uma região e, em seguida, clique em **criar solução**. A solução pode demorar um pouco para ser provisionada.
  
   ![Criar solução](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. Após a conclusão do provisionamento, clique em **Iniciar**. Alguns dispositivos simulados são criados para a solução durante o processo de provisionamento. Clique em **dispositivos** para verificá-los.

   ![Dashboard](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  
   ![Consola](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. Clique em **Adicionar um dispositivo**.

6. Clique em **Adicionar novo** para **dispositivo personalizado**.
  
   ![Adicionar um novo dispositivo](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. Clique em **deixe-me definir minha própria ID de dispositivo**, digite `AZ3166`e, em seguida, clique em **criar**.
  
   ![Criar dispositivo com ID](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. Anote o nome de **host do Hub IOT**e clique em **concluído**.

## <a name="open-the-remotemonitoring-sample"></a>Abrir o exemplo de RemoteMonitoring

1. Desconecte o DevKit do seu computador, se ele estiver conectado.

2. Iniciar VS Code.

3. Conecte o DevKit ao seu computador. VS Code detecta automaticamente seu DevKit e abre as seguintes páginas:

   * A página de introdução do DevKit.
   * Exemplos de Arduino: exemplos práticos para começar a usar o DevKit.

4. Expanda a seção **exemplos do ARDUINO** no lado esquerdo, navegue até **exemplos para MXCHIP AZ3166 > AzureIoT**e selecione **RemoteMonitoring**. Ele abre uma nova janela VS Code com uma pasta do projeto.

   > [!NOTE]
   > Se você fechar o painel, poderá reabri-lo. Use `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para abrir a paleta de comandos, digite **Arduino**e, em seguida, localize e selecione **Arduino: exemplos**.

## <a name="provision-required-azure-services"></a>Provisionar os serviços do Azure necessários

Na janela da solução, execute a tarefa por meio de `Ctrl+P` (macOS: `Cmd+P`) inserindo `task cloud-provision` na caixa de texto fornecida.

No VS Code terminal, uma linha de comando interativa orienta você pelo provisionamento dos serviços do Azure necessários.

![Provisionar recursos do Azure](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>Compilar e carregar o código do dispositivo

1. Use `Ctrl+P` (macOS: `Cmd + P`) e digite **config da tarefa-Device-Connection**.

2. O terminal pergunta se você deseja usar uma cadeia de conexão que recupera da etapa `task cloud-provision`. Você também pode inserir sua própria cadeia de conexão de dispositivo clicando em ' criar nova... '

3. O terminal solicita que você insira o modo de configuração. Para fazer isso, mantenha o botão A pressionado, em seguida, pressione e solte o botão redefinir. A tela exibe a ID de DevKit e ' Configuration '.

   ![Cadeia de conexão de entrada](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. Depois que `task config-device-connection` for concluído, clique em `F1` para carregar VS Code comandos e selecione `Arduino: Upload`. VS Code inicia a verificação e o carregamento do esboço Arduino.
  
   ![Verificação e upload do esboço Arduino](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

O DevKit reinicia e inicia a execução do código.

## <a name="test-the-project"></a>Testar o projeto

Quando o aplicativo de exemplo é executado, o DevKit envia dados de sensor por Wi-Fi para o acelerador de solução de monitoramento remoto do Azure IoT. Para ver o resultado, siga estas etapas:

1. Vá para o acelerador de solução de monitoramento remoto do Azure IoT e clique em **painel**.

2. No console da solução de monitoramento remoto, você verá o status do sensor DevKit.

   ![Dados do sensor no acelerador de solução de monitoramento remoto do Azure IoT](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>Alterar ID do dispositivo

Se você quiser alterar o **AZ3166** codificado para uma ID de dispositivo personalizada no código, modifique a linha de código exibida no [exemplo de monitoramento remoto](https://github.com/Microsoft/devkit-sdk/blob/master/AZ3166/src/libraries/AzureIoT/examples/RemoteMonitoring/RemoteMonitoring.ino#L23).

## <a name="problems-and-feedback"></a>Problemas e comentários

Se você tiver problemas, consulte [as perguntas frequentes do kit de desenvolvedor do IOT](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou entre em contato conosco usando os seguintes canais:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu como conectar um dispositivo DevKit ao acelerador de solução de monitoramento remoto de IoT do Azure e visualizar os dados do sensor, aqui estão as próximas etapas sugeridas:

* [Visão geral dos aceleradores de soluções do Azure IoT](https://docs.microsoft.com/azure/iot-suite/)

* [Conectar um dispositivo MXChip IoT DevKit ao aplicativo IoT Central do Azure](/azure/iot-central/core/howto-connect-devkit)

* [Kit para desenvolvedores de IoT](https://microsoft.github.io/azure-iot-developer-kit/) 

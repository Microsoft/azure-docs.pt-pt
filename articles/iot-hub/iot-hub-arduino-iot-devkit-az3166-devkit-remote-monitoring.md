---
title: Ligue MXChip IoT DevKit à Monitorização Remota Do Hub Azure IoT
description: Neste tutorial, aprenda a enviar o estado dos sensores no IoT DevKit AZ3166 para o acelerador de solução de monitorização remota Azure IoT.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: 598e361949b000724645c841910b1682a7bbb1a3
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258461"
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-remote-monitoring-solution-accelerator"></a>Ligue o MXChip IoT DevKit ao acelerador de solução de monitorização remota Azure IoT

Neste tutorial, aprende a executar uma aplicação de amostra no seu DevKit para enviar dados de sensores para o seu acelerador de solução de monitorização remota Azure IoT.

O [MXChip IoT DevKit](https://aka.ms/iot-devkit) é uma placa compatível com Arduino com periféricos ricos e sensores. Pode desenvolver-se para ele utilizando [a extensão do Código do Estúdio Visual para a Arduino.](https://aka.ms/arduino) E vem com um catálogo de [projetos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) em crescimento para guiá-lo protótipo de soluções Internet of Things (IoT) que aproveitam os serviços do Microsoft Azure.

## <a name="what-you-need"></a>Do que precisa

Termine o [Guia de Início de Partida](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) para:

* Tenha o seu DevKit ligado ao Wi-Fi
* Preparar o ambiente de desenvolvimento

Uma subscrição ativa do Azure. Se não tiver um, pode registar-se através de um destes dois métodos:

* Ativar uma [conta gratuita de 30 dias do Microsoft Azure](https://azure.microsoft.com/free/)

* Reclame o seu [crédito Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) se for assinante da MSDN ou do Visual Studio

## <a name="create-an-azure-iot-remote-monitoring-solution-accelerator"></a>Criar um acelerador de solução de monitorização remota Azure IoT

1. Vá ao site de aceleradores de [soluções Azure IoT](https://www.azureiotsolutions.com/) e clique **em Criar uma nova solução**.

   ![Selecione tipo de acelerador de solução Azure IoT](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)

   > [!WARNING]
   > Por padrão, esta amostra cria um Hub S2 IoT depois de criar um acelerador de solução de monitorização remota IoT. Se este hub IoT não for utilizado com um número maciço de dispositivos, recomendamos vivamente que o desdowngrade de S2 para S1 e apague o acelerador de solução de monitorização remota IoT para que o hub IoT relacionado também possa ser eliminado, quando já não precisar. 

2. Selecione **monitorização remota**.

3. Introduza um nome de solução, selecione uma subscrição e uma região e, em seguida, clique em **Criar solução**. A solução pode demorar algum tempo a ser prevista.
  
   ![Criar solução](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. Depois de fornecer acabamentos, clique em **Lançamento**. Alguns dispositivos simulados são criados para a solução durante o processo de fornecimento. Clique em **DISPOSITIVOS** para verificar.

   ![Dashboard](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  
   ![Consola](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. Clique em **ADICIONAR Um DISPOSITIVO**.

6. Clique em **adicionar novo** para **dispositivo personalizado**.
  
   ![Adicionar um novo dispositivo](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. Clique **em deixar-me definir o meu próprio ID de dispositivo,** introduzir `AZ3166`e clicar em **Criar**.
  
   ![Criar dispositivo com ID](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. Tome nota do nome de **anfitrião IoT Hub,** e clique **em Done**.

## <a name="open-the-remotemonitoring-sample"></a>Abra a amostra de Monitorização Remota

1. Desligue o DevKit do seu computador, se estiver ligado.

2. Inicie o VS Code.

3. Ligue o DevKit ao seu computador. O Código VS deteta automaticamente o seu DevKit e abre as seguintes páginas:

   * A página de introdução do DevKit.
   * Arduino Exemplos: Amostras práticas para começar com o DevKit.

4. Expandir a secção **DE EXEMPLOS ARDUINO** do lado esquerdo, navegar em **Exemplos para MXCHIP AZ3166 > AzureIoT,** e selecione **RemoteMonitoring**. Abre uma nova janela vs Código com uma pasta de projeto.

   > [!NOTE]
   > Se por acaso fechar o painel, pode reabri-lo. Utilize `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para abrir a paleta de comando, digite **Arduino,** e depois encontre e selecione **Arduino: Exemplos**.

## <a name="provision-required-azure-services"></a>Prestação exigia serviços Azure

Na janela de solução, `Ctrl+P` passe a `Cmd+P`sua tarefa `task cloud-provision` através (macOS: ) através da introdução na caixa de texto fornecida.

No terminal vs Código, uma linha de comando interativa guia-o através do fornecimento dos serviços Azure necessários.

![Provision Recursos Azure](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>Construir e carregar o código do dispositivo

1. Utilização `Ctrl+P` (macOS) `Cmd + P`e tipo de tarefa **config-device-connection**.

2. O terminal pergunta se pretende utilizar uma corda de `task cloud-provision` ligação que recupera do degrau. Também pode inserir a sua própria cadeia de ligação ao dispositivo clicando em 'Create New...'

3. O terminal pede-lhe para entrar no modo de configuração. Para tal, segure o botão A e, em seguida, pressione e liberte o botão de reset. O ecrã apresenta o ID de DevKit e 'Configuração'.

   ![Cadeia de ligação de entrada](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. Depois `task config-device-connection` de terminar, clique para `F1` carregar `Arduino: Upload`comandos vs Código e selecione . O Código VS começa a verificar e a carregar o esboço arduino.
  
   ![Verificação e upload do esboço arduino](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

O DevKit reinicia e começa a executar o código.

## <a name="test-the-project"></a>Testar o projeto

Quando a aplicação da amostra funciona, o DevKit envia dados de sensores sobre WiFi para o seu acelerador de solução de monitorização remota Azure IoT. Para ver o resultado, siga estes passos:

1. Vá ao seu acelerador de solução de monitorização remota Azure IoT e clique em **DASHBOARD**.

2. Na consola de solução de monitorização remota, verá o seu estado de sensor DevKit.

   ![Dados do sensor no acelerador de solução de monitorização remota Azure IoT](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>Alterar id do dispositivo

Se pretender alterar o **AZ3166** codificado com código rígido para um ID de dispositivo personalizado no código, modifique a linha de código apresentada no exemplo de [monitorização remota](../iot-accelerators/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoring-v2.md).

## <a name="problems-and-feedback"></a>Problemas e feedback

Se encontrar problemas, consulte [as FAQs do kit de desenvolvimento IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou contacte-nos utilizando os seguintes canais:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a ligar um dispositivo DevKit ao seu acelerador de solução de monitorização remota Azure IoT e visualizar os dados do sensor, aqui estão os próximos passos sugeridos:

* [Visão geral dos aceleradores de soluções Azure IoT](https://docs.microsoft.com/azure/iot-suite/)

* [Ligue um dispositivo MXChip IoT DevKit à sua aplicação Central Azure IoT](/azure/iot-central/core/howto-connect-devkit)

* [Kit de desenvolvimento ioT](https://microsoft.github.io/azure-iot-developer-kit/) 

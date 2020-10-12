---
title: Ligue o MXChip IoT DevKit à monitorização remota do Hub IoT Azure IoT
description: Neste tutorial, aprenda a enviar o estado dos sensores no IoT DevKit AZ3166 para o acelerador de solução de monitorização remota Azure IoT.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: d75e7e7b4870f46c978fd70039441c8851844082
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87337964"
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-remote-monitoring-solution-accelerator"></a>Ligue o acelerador de solução de monitorização remota MXChip IoT a Azure IoT

Neste tutorial, aprende a executar uma aplicação de amostra no seu DevKit para enviar dados de sensores para o seu acelerador de solução de monitorização remota Azure IoT.

O [MXChip IoT DevKit](https://aka.ms/iot-devkit) é um tabuleiro compatível com Arduino com periféricos e sensores ricos. Pode desenvolver-se para ele utilizando a [extensão visual Studio Code para Arduino.](https://aka.ms/arduino) E vem com um catálogo de [projetos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) em crescimento para guiá-lo protótipo de soluções Internet of Things (IoT) que tiram partido dos serviços microsoft Azure.

## <a name="what-you-need"></a>O que precisa

Termine o [Guia de Início](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) para:

* Tenha o seu DevKit ligado a Wi-Fi
* Preparar o ambiente de desenvolvimento

Uma subscrição ativa do Azure. Se não tiver um, pode registar-se através de um destes dois métodos:

* Ativar uma [conta gratuita de 30 dias da Microsoft Azure](https://azure.microsoft.com/free/)

* Reclame o seu [crédito Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) se for assinante da MSDN ou do Visual Studio

## <a name="create-an-azure-iot-remote-monitoring-solution-accelerator"></a>Criar um acelerador de solução de monitorização remota Azure IoT

1. Vá ao [site de aceleradores de solução Azure IoT](https://www.azureiotsolutions.com/) e clique em **Criar uma nova solução.**

   ![Selecione tipo de acelerador de solução Azure IoT](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)

   > [!WARNING]
   > Por predefinição, esta amostra cria um Hub S2 IoT depois de criar um acelerador de solução de monitorização remota IoT. Se este hub IoT não for utilizado com um grande número de dispositivos, recomendamos vivamente que o desclasse de S2 para S1 e elimine o acelerador de símio de monitorização remota IoT para que o linked IoT Hub também possa ser eliminado, quando já não precisar. 

2. Selecione **monitorização remota**.

3. Introduza um nome de solução, selecione uma subscrição e uma região e, em seguida, clique em **Criar solução**. A solução pode demorar algum tempo a ser a provisionada.
  
   ![Criar solução](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. Depois de a provisionar acabamentos, clique em **Lançamento**. Alguns dispositivos simulados são criados para a solução durante o processo de provisionamento. Clique **em DISPOSITIVOS** para os verificar.

   ![Dashboard](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  
   ![Consola](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. Clique **em ADICIONAR UM DISPOSITIVO**.

6. Clique **em Adicionar Novo** para Dispositivo **Personalizado**.
  
   ![Adicionar um novo dispositivo](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. Clique **em Deixar-me definir o meu próprio ID de dispositivo,** introduzir `AZ3166` e, em seguida, clicar em **Criar**.
  
   ![Criar dispositivo com ID](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. Tome nota do **nome de anfitrião do IoT Hub**e clique em **'Fazer'.**

## <a name="open-the-remotemonitoring-sample"></a>Abra a amostra remoteMonitoring

1. Desligue o DevKit do computador, se estiver ligado.

2. Inicie o VS Code.

3. Ligue o DevKit ao seu computador. O Código VS deteta automaticamente o seu DevKit e abre as seguintes páginas:

   * A página de introdução do DevKit.
   * Exemplos Arduino: Amostras práticas para começar com o DevKit.

4. Expanda a secção **ARDUINO EXAMPLES** do lado esquerdo, navegue **por Exemplos para MXCHIP AZ3166 > AzureIoT,** e selecione **RemoteMonitoring**. Abre uma nova janela vs Code com uma pasta de projeto.

   > [!NOTE]
   > Se por acaso fechar o painel, pode reabri-la. Utilize `Ctrl+Shift+P` (macOS: `Cmd+Shift+P` ) para abrir a paleta de comando, **digite Arduino,** e depois encontre e selecione **Arduino: Exemplos**.

## <a name="provision-required-azure-services"></a>Prestação exigida aos serviços da Azure

Na janela da solução, passe a sua tarefa através `Ctrl+P` (macOS: `Cmd+P` ) introduzindo `task cloud-provision` na caixa de texto fornecida.

No terminal de Código VS, uma linha de comando interativa guia-o através do fornecimento dos serviços Azure necessários.

![Fornecimento de recursos Azure](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>Construa e carreie o código do dispositivo

1. Utilizar `Ctrl+P` (macOS:) `Cmd + P` e tipo de **ligação config-dispositivo de tarefa**.

2. O terminal pergunta se pretende utilizar uma corda de ligação que recupere do `task cloud-provision` degrau. Também pode inserir a sua própria cadeia de ligação do dispositivo clicando em 'Criar Novo...'

3. O terminal solicita-lhe que entre no modo de configuração. Para tal, mantenha premida o botão A, em seguida, pressione e liberte o botão de reset. O ecrã apresenta o ID de DevKit e 'Configuração'.

   ![Cadeia de ligação de entrada](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. Depois de `task config-device-connection` terminar, clique `F1` para carregar os comandos do Código VS e selecione `Arduino: Upload` . O Código VS começa a verificar e a carregar o esboço do Arduino.
  
   ![Verificação e upload do esboço de Arduino](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

O DevKit reinicia e começa a executar o código.

## <a name="test-the-project"></a>Testar o projeto

Quando a aplicação da amostra é executada, o DevKit envia dados de sensores sobre Wi-Fi para o seu acelerador de solução de monitorização remota Azure IoT. Para ver o resultado, siga estes passos:

1. Vá ao acelerador de solução de monitorização remota Azure IoT e clique no **DASHBOARD**.

2. Na consola de solução de monitorização remota, verá o estado do sensor DevKit.

   ![Dados do sensor no acelerador de solução de monitorização remota Azure IoT](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>Alterar iD do dispositivo

Se pretender alterar o **AZ3166** codificado com código para um ID de dispositivo personalizado, modifique a linha de código exibida no exemplo de [monitorização remota](../iot-accelerators/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoring-v2.md).

## <a name="problems-and-feedback"></a>Problemas e feedback

Se encontrar problemas, consulte [o kit de desenvolvimento IoT FAQs](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou contacte-nos utilizando os seguintes canais:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a ligar um dispositivo DevKit ao seu acelerador de solução de monitorização remota Azure IoT e visualizar os dados do sensor, eis os próximos passos sugeridos:

* [Visão geral dos aceleradores de solução Azure IoT](https://docs.microsoft.com/azure/iot-suite/)

* [Kit de desenvolvimento IoT](https://microsoft.github.io/azure-iot-developer-kit/) 

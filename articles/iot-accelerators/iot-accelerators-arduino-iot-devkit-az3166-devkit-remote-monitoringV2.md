---
title: Ligar o IoT DevKit para monitorização remota solution accelerator - Azure | Documentos da Microsoft
description: Este guia de procedimentos, saiba como enviar telemetria a partir de sensores no dispositivo de IoT DevKit AZ3166 as para monitorização remota do solution accelerator para monitorização e de visualização.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: isacabe
ms.openlocfilehash: 0f444a4ce41a700af4c55795bca95dbbf95003a4
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56750026"
---
# <a name="connect-an-iot-devkit-device-to-the-remote-monitoring-solution-accelerator"></a>Ligue um dispositivo de IoT DevKit ao acelerador de solução de monitorização remota

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Este guia de procedimentos mostra como executar um aplicativo de exemplo no seu dispositivo IoT DevKit. O código de exemplo envia a telemetria de sensores no dispositivo DevKit do solution accelerator.

O [MXChip IoT DevKit](https://aka.ms/iot-devkit) é uma área de compatível de Arduino tudo-em-um com periféricos avançados e sensores. Pode desenvolver para ele usando [Bancada de trabalho de dispositivo do Azure IoT](https://aka.ms/iot-workbench) ou [ferramentas do Azure IoT](https://aka.ms/azure-iot-tools) pacote de extensão no Visual Studio Code. O [catálogo de projetos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) contém aplicativos de exemplo para ajudá-lo a soluções de IoT de protótipo.

## <a name="before-you-begin"></a>Antes de começar

Para concluir os passos neste tutorial, primeiro faça as seguintes tarefas:

* Preparar seu DevKit ao seguir os passos em [ligar IoT DevKit AZ3166 hub do IoT do Azure na cloud](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

## <a name="open-sample-project"></a>Projeto de exemplo aberto

Para abrir o exemplo de monitorização remota no VS Code:

1. Certifique-se de que o seu IoT DevKit não é para o seu computador. Inicie o VS Code primeiro e, em seguida, ligue o DevKit para o seu computador.

1. Clique em `F1` para abrir a paleta de comandos, escreva e selecione **Bancada de trabalho de dispositivo do Azure IoT: Abrir os exemplos...** . Em seguida, selecione **IoT DevKit** como quadro.

1. Encontrar **monitorização remota** e clique em **exemplo aberto**. Uma nova janela do VS Code abre-se de que mostra a pasta do projeto:

  ![Bancada de trabalho de IoT, selecione exemplo de monitorização remota](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-the-device"></a>Configurar o dispositivo

Para configurar a cadeia de ligação de dispositivos do IoT Hub no seu dispositivo DevKit:

1. Mude a IoT DevKit em **modo de configuração**:

    * Mantenha premida botão **A**.
    * Push e de versão da **repor** botão.

1. A tela exibe o ID de DevKit e `Configuration`.

    ![Modo de configuração de IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

1. Prima **F1** para abrir a paleta de comandos, escreva e selecione **Bancada de trabalho de dispositivo do Azure IoT: Configurar as definições de dispositivos... > cadeia de ligação do dispositivo de configuração**.

1. Cole a cadeia de ligação que copiou anteriormente e prima **Enter** para configurar o dispositivo.

## <a name="build-the-code"></a>Compilar o código

Para criar e carregar o código de dispositivo:

1. Prima `F1` para abrir a paleta de comandos, escreva e selecione **Bancada de trabalho de dispositivo do Azure IoT: Carregar o código de dispositivo**:

1. O VS Code compila e carrega o código para o seu dispositivo DevKit:

    ![Bancada de trabalho de IoT: Dispositivo - > carregado](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

1. O dispositivo de DevKit reinicia e execute o código que carregou.

## <a name="test-the-sample"></a>O exemplo de teste

Para verificar se o aplicativo de exemplo que carregou para o dispositivo DevKit está a funcionar, conclua os seguintes passos:

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>Ver a telemetria enviada para a solução de monitorização remota

Quando a aplicação de exemplo é executado, o dispositivo de DevKit envia telemetria dos seus dados de sensores por Wi-Fi do solution accelerator. Para ver a telemetria:

1. Aceda ao seu dashboard de solução e clique em **Device Explorer**.

1. Clique no nome do dispositivo do seu dispositivo DevKit. no separador da direita, pode ver a telemetria da DevKit em tempo real:

    ![Dados de sensores no Azure IoT Suite](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="control-the-devkit-device"></a>Controlar o dispositivo DevKit

O acelerador de solução de monitorização remota permite-lhe controlar remotamente o seu dispositivo. O código de exemplo implementa três métodos que pode ver na **método** secção ao selecionar o dispositivo na **Device Explorer** página:

![Métodos de IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

Para alterar a cor de um dos LEDs DevKit, utilize o **LedColor** método:

1. Selecione o nome do dispositivo da lista de dispositivos e clique nas **tarefas**:

    ![Criar uma tarefa](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

1. Configurar as tarefas com os seguintes valores e clique em **aplicar**:

    * Selecione a tarefa: **Executar o método**
    * Nome do método: **LedColor**
    * Nome da tarefa: **ChangeLedColor**

    ![Definições da tarefa](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

1. Após alguns segundos, altera a cor do RGB LED (abaixo do botão A) na sua DevKit:

    ![Vermelho IoT DevKit por instrutor](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se planear avançar para os tutoriais, deixe o acelerador de soluções de Monitorização Remota implementado.

Se já não precisar do solution accelerator, elimine-o partir da página de soluções aprovisionado, selecioná-la e, em seguida, clicando em Eliminar solução:

![Eliminar solução](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>Problemas e comentários

Caso se depare com problemas, consulte [perguntas frequentes sobre do IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou contacte-nos com os seguintes canais:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como ligar um dispositivo de DevKit ao seu acelerador de solução de monitorização remota, seguem-se alguns passos seguintes sugeridos:

* [Azure descrição geral do Aceleradores de solução IoT](https://docs.microsoft.com/azure/iot-accelerators/)
* [Personalizar a IU](iot-accelerators-remote-monitoring-customize.md)
* [Ligar o IoT DevKit à sua aplicação do Azure IoT Central](../iot-central/howto-connect-devkit.md)
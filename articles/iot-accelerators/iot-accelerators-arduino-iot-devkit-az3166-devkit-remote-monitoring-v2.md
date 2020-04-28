---
title: Ligue ioT DevKit à solução de monitorização remota - Azure [ Microsoft Docs
description: Neste guia de como fazer, aprende-se a enviar telemetria a partir dos sensores do dispositivo IoT DevKit AZ3166 para o acelerador de soluções de monitorização remota para monitorização e visualização.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: isacabe
ms.openlocfilehash: 6e9f9c89cf2e5e40d37a1532e688490aae294181
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73888870"
---
# <a name="connect-an-iot-devkit-device-to-the-remote-monitoring-solution-accelerator"></a>Ligue um dispositivo IoT DevKit ao acelerador de solução de monitorização remota

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Este guia de como fazer mostra como executar uma aplicação de amostra no seu dispositivo IoT DevKit. O código da amostra envia telemetria dos sensores do dispositivo DevKit para o seu acelerador de solução.

O [MXChip IoT DevKit](https://aka.ms/iot-devkit) é uma placa compatível com Arduino com periféricos ricos e sensores. Pode desenvolver-se para ele utilizando a bancada de trabalho do [dispositivo Azure IoT](https://aka.ms/iot-workbench) ou o pacote de extensão [Azure IoT Tools](https://aka.ms/azure-iot-tools) no Código do Estúdio Visual. O catálogo de [projetos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) contém aplicações de amostra para ajudá-lo a protótipo de soluções IoT.

## <a name="before-you-begin"></a>Antes de começar

Para completar os passos neste tutorial, faça primeiro as seguintes tarefas:

* Prepare o seu DevKit seguindo os passos do [Connect IoT DevKit AZ3166 para o Azure IoT Hub na nuvem](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

## <a name="open-sample-project"></a>Projeto de amostra aberta

Para abrir a amostra de monitorização remota no Código VS:

1. Certifique-se de que o seu IoT DevKit não está no seu computador. Inicie primeiro o Código VS e, em seguida, ligue o DevKit ao seu computador.

1. Clique `F1` para abrir a paleta de comando, digite e selecione Bancada de Trabalho do **Dispositivo Azure IoT: Exemplos abertos...**. Em seguida, selecione **IoT DevKit** como placa.

1. Encontre **a monitorização remota** e clique em Amostra **Aberta**. Abre-se uma nova janela vs Código mostrando a pasta do projeto:

   ![Bancada de trabalho IoT, selecione exemplo de monitorização remota](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-the-device"></a>Configure o dispositivo

Para configurar a cadeia de ligação do dispositivo IoT Hub no seu dispositivo DevKit:

1. Mude o IoT DevKit para o modo de **configuração:**

    * Segure o botão **A**.
    * Pressione e liberte o botão **Reset.**

1. O ecrã apresenta o ID `Configuration`DevKit e .

    ![Modo de configuração IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

1. Prima **F1** para abrir a paleta de comando, digite e selecione bancada de trabalho do **dispositivo Azure IoT: Configurar definições de dispositivo... > Cadeia de Ligação ao Dispositivo Config**.

1. Colar a corda de ligação que copiou anteriormente e prima **Enter** para configurar o dispositivo.

## <a name="build-the-code"></a>Compilar o código

Para construir e carregar o código do dispositivo:

1. Prima `F1` para abrir a paleta de comando, digite e selecione bancada de trabalho do **dispositivo Azure IoT: Código do dispositivo de upload:**

1. O Código VS compila e envia o código para o seu dispositivo DevKit:

    ![Bancada de trabalho IoT: Dispositivo - > Carregado](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

1. O dispositivo DevKit reinicia e executa o código que carregou.

## <a name="test-the-sample"></a>Testar a amostra

Para verificar se a aplicação de amostra que fez para o dispositivo DevKit está a funcionar, complete os seguintes passos:

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>Ver a telemetria enviada para a solução de Monitorização Remota

Quando a aplicação da amostra funciona, o dispositivo DevKit envia telemetria dos seus dados de sensores sobre Wi-Fi para o seu acelerador de soluções. Para ver a telemetria:

1. Vá ao seu painel de solução e clique no **Device Explorer**.

1. Clique no nome do dispositivo do seu dispositivo DevKit. no separador direito, pode ver a telemetria do DevKit em tempo real:

    ![Dados de sensores na Suite Azure IoT](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="control-the-devkit-device"></a>Controlar o dispositivo DevKit

O acelerador de solução de monitorização remota permite controlar o seu dispositivo remotamente. O código da amostra implementa três métodos que pode ver na secção **Método** quando seleciona o dispositivo na página Do Explorador do **Dispositivo:**

![Métodos IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

Para alterar a cor de um dos LEDs DevKit, utilize o método **LedColor:**

1. Selecione o nome do dispositivo na lista do dispositivo e clique nos **Trabalhos:**

    ![Criar um Trabalho](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

1. Configure os Trabalhos utilizando os seguintes valores e clique **Aplicar:**

   * Selecionar Trabalho: **Executar método**
   * Nome do método: **LedColor**
   * Nome do trabalho: **ChangeLedColor**

     ![Definições da tarefa](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

1. Após alguns segundos, a cor do LED RGB (abaixo do botão A) no seu DevKit muda:

    ![IoT DevKit vermelho liderado](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se planear avançar para os tutoriais, deixe o acelerador de soluções de Monitorização Remota implementado.

Se já não precisar do acelerador de soluções, elimine-o na página Soluções aprovisionadas ao selecioná-lo e, em seguida, clique em Eliminar Solução:

![Eliminar solução](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>Problemas e feedback

Se tiver algum problema, consulte [as FAQs IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou contacte-nos utilizando os seguintes canais:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a ligar um dispositivo DevKit ao seu acelerador de solução de monitorização remota, aqui estão alguns passos seguintes sugeridos:

* [Visão geral dos aceleradores de soluções Azure IoT](https://docs.microsoft.com/azure/iot-accelerators/)
* [Personalizar a IU](iot-accelerators-remote-monitoring-customize.md)
* [Ligue ioT DevKit à sua aplicação Central Azure IoT](../iot-central/core/howto-connect-devkit.md)
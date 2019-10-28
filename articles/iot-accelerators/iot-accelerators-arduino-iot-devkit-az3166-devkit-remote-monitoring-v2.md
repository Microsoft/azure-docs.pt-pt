---
title: Conectar o IoT DevKit ao acelerador de solução de monitoramento remoto – Azure | Microsoft Docs
description: Neste guia de instruções, você aprende a enviar telemetria dos sensores no dispositivo IoT DevKit AZ3166 o para o acelerador de solução de monitoramento remoto para monitoramento e visualização.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: isacabe
ms.openlocfilehash: 542a0780c0525ee7ceddfd36c3a4f7c348d0574f
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72930867"
---
# <a name="connect-an-iot-devkit-device-to-the-remote-monitoring-solution-accelerator"></a>Conectar um dispositivo IoT DevKit ao acelerador de solução de monitoramento remoto

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Este guia de instruções mostra como executar um aplicativo de exemplo em seu dispositivo IoT DevKit. O código de exemplo envia telemetria dos sensores no dispositivo DevKit para o Solution Accelerator.

O [MXChip IOT devkit](https://aka.ms/iot-devkit) é uma placa compatível com Arduino tudo em um com periféricos avançados e sensores. Você pode desenvolver para ele usando o [Azure IOT Device Workbench](https://aka.ms/iot-workbench) ou o pacote de extensão das [Ferramentas do azure IOT](https://aka.ms/azure-iot-tools) no Visual Studio Code. O [Catálogo de projetos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) contém aplicativos de exemplo para ajudá-lo a criar protótipos de soluções de IOT.

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas deste tutorial, primeiro execute as seguintes tarefas:

* Prepare seu DevKit seguindo as etapas em [conectar IOT DEVKIT AZ3166 ao Hub IOT do Azure na nuvem](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

## <a name="open-sample-project"></a>Abrir projeto de exemplo

Para abrir o exemplo de monitoramento remoto no VS Code:

1. Verifique se seu DevKit de IoT não está no seu computador. Inicie VS Code primeiro e, em seguida, conecte o DevKit ao seu computador.

1. Clique em `F1` para abrir a paleta de comandos, digite e selecione **Azure IOT Device Workbench: abrir exemplos...** . Em seguida, selecione **IOT devkit** como placa.

1. Localize **monitoramento remoto** e clique em **abrir exemplo**. Uma nova janela VS Code é aberta mostrando a pasta do projeto:

   ![IoT Workbench, selecione o exemplo de monitoramento remoto](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-the-device"></a>Configurar o dispositivo

Para configurar a cadeia de conexão do dispositivo do Hub IoT em seu dispositivo DevKit:

1. Alterne o DevKit IoT para o **modo de configuração**:

    * Mantenha o botão **A**.
    * Enviar por push e liberar o botão de **reinicialização** .

1. A tela exibe a ID DevKit e a `Configuration`.

    ![Modo de configuração do IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

1. Pressione **F1** para abrir a paleta de comandos, digite e selecione **Azure IOT Device Workbench: definir configurações do dispositivo... Cadeia de conexão do dispositivo > config**.

1. Cole a cadeia de conexão que você copiou anteriormente e pressione **Enter** para configurar o dispositivo.

## <a name="build-the-code"></a>Compilar o código

Para compilar e carregar o código do dispositivo:

1. Pressione `F1` para abrir a paleta de comandos, digite e selecione **Azure IOT Device Workbench: carregar código do dispositivo**:

1. VS Code compila e carrega o código em seu dispositivo DevKit:

    ![IoT Workbench: dispositivo-> carregado](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

1. O dispositivo DevKit reinicia e executa o código que você carregou.

## <a name="test-the-sample"></a>Testar o exemplo

Para verificar se o aplicativo de exemplo carregado no dispositivo DevKit está funcionando, conclua as seguintes etapas:

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>Exibir a telemetria enviada para a solução de monitoramento remoto

Quando o aplicativo de exemplo é executado, o dispositivo DevKit envia telemetria de seus dados de sensores por Wi-Fi para o Solution Accelerator. Para ver a telemetria:

1. Vá para o painel da solução e clique em **Device Explorer**.

1. Clique no nome do dispositivo do seu dispositivo DevKit. na guia à direita, você pode ver a telemetria do DevKit em tempo real:

    ![Dados do sensor em Azure IoT Suite](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="control-the-devkit-device"></a>Controlar o dispositivo DevKit

O acelerador de solução de monitoramento remoto permite controlar seu dispositivo remotamente. O código de exemplo implementa três métodos que podem ser exibidos na seção **método** quando você seleciona o dispositivo na página **Device Explorer** :

![Métodos de IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

Para alterar a cor de um dos LEDs DevKit, use o método **LedColor** :

1. Selecione o nome do dispositivo na lista de dispositivos e clique nos **trabalhos**:

    ![Criar um trabalho](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

1. Configure os trabalhos usando os seguintes valores e clique em **aplicar**:

   * Selecionar trabalho: **método Run**
   * Nome do método: **LedColor**
   * Nome do trabalho: **ChangeLedColor**

     ![Definições da tarefa](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

1. Após alguns segundos, a cor do LED RGB (abaixo do botão A) em seu DevKit é alterada:

    ![LED vermelho de IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se planear avançar para os tutoriais, deixe o acelerador de soluções de Monitorização Remota implementado.

Se você não precisar mais do Solution Accelerator, exclua-o da página soluções provisionadas, selecionando-o e clicando em excluir solução:

![Eliminar solução](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>Problemas e comentários

Se você tiver problemas, consulte [as perguntas frequentes do IOT devkit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou entre em contato conosco usando os seguintes canais:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu como conectar um dispositivo DevKit ao acelerador de solução de monitoramento remoto, aqui estão algumas próximas etapas sugeridas:

* [Visão geral dos aceleradores de soluções do Azure IoT](https://docs.microsoft.com/azure/iot-accelerators/)
* [Personalizar a IU](iot-accelerators-remote-monitoring-customize.md)
* [Conectar o IoT DevKit ao seu aplicativo IoT Central do Azure](../iot-central/core/howto-connect-devkit.md)
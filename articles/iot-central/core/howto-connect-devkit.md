---
title: Ligue um dispositivo DevKit à sua aplicação Azure IoT Central [ Microsoft Docs
description: Como desenvolvedor de dispositivos, aprenda a ligar um dispositivo MXChip IoT DevKit à sua aplicação Azure IoT Central utilizando ioT Plug e Play (pré-visualização).
author: liydu
ms.author: liydu
ms.date: 12/03/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: bcf1dd2f89cf049d7da5b56170b2c13874c83ba4
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756804"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Ligue um dispositivo MXChip IoT DevKit à sua aplicação Central Azure IoT

*Este artigo aplica-se aos desenvolvedores de dispositivos.*

Este artigo mostra-lhe como ligar um dispositivo MXChip IoT DevKit (DevKit) a uma aplicação Azure IoT Central. O dispositivo utiliza o modelo certificado IoT Plug and Play (pré-visualização) para o dispositivo DevKit para configurar a sua ligação à IoT Central.

Neste artigo de como fazer, você:

- Obtenha os dados de ligação da sua aplicação IoT Central.
- Prepare o dispositivo e ligue-o à sua aplicação IoT Central.
- Veja a telemetria e as propriedades do dispositivo na IoT Central.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste artigo, precisa dos seguintes recursos:

- Um [dispositivo DevKit](https://aka.ms/iot-devkit-purchase).
- Uma aplicação IoT Central. Pode seguir os passos na [Create a IoT Central application](./quick-deploy-iot-central.md).

## <a name="get-device-connection-details"></a>Obter detalhes de ligação do dispositivo

1. Na sua aplicação Azure IoT Central, selecione o separador **Modelos** de Dispositivo e selecione **+ Novo**. Na secção Utilize um modelo de **dispositivo pré-configurado,** selecione **MXChip IoT DevKit**.

    ![Modelo de dispositivo para MXChip IoT DevKit](media/howto-connect-devkit/device-template.png)

1. Selecione **A seguir: Personalizar** e **criar**.

1. Selecione o separador **Dispositivos.** Na lista de dispositivos, selecione **MXChip IoT DevKit** e selecione **+ Novo** para criar um novo dispositivo a partir do modelo.

    ![Novo dispositivo](media/howto-connect-devkit/new-device.png)

1. Na janela pop-up, introduza `SampleDevKit` o ID `MXChip IoT DevKit - Sample`do **dispositivo** como e o nome do **dispositivo** como . Certifique-se de que a opção **Simulada** está desligada. Em seguida, selecione **Criar**.

    ![ID do dispositivo e nome](media/howto-connect-devkit/device-id-name.png)

1. Selecione o dispositivo que criou e, em seguida, selecione **Connect**. Tome nota do âmbito de **identificação,** **id do dispositivo**e da tecla **primária**. Precisa destes valores mais tarde neste artigo de como fazer.

    ![Informações de ligação ao dispositivo](media/howto-connect-devkit/device-connection-info.png)

## <a name="prepare-the-device"></a>Preparar o dispositivo

1. Descarregue o mais recente [firmware de plug e play central azure IoT (pré-visualização)](https://github.com/Azure-Samples/mxchip-iot-devkit-pnp/raw/master/bin/iotc_devkit.bin) para o dispositivo DevKit a partir do GitHub.

1. Ligue o dispositivo DevKit à sua máquina de desenvolvimento utilizando um cabo USB. No Windows, abre-se uma janela de explorador de ficheiros numa unidade mapeada para o armazenamento no dispositivo DevKit. Por exemplo, a unidade pode chamar-se **AZ3166 (D:)**.

1. Arraste o ficheiro **iotc_devkit.bin** para a janela de acionamento. Quando a cópia estiver completa, o dispositivo reinicia com o novo firmware.

    > [!NOTE]
    > Se vir erros no ecrã como **o No Wi-Fi,** isto deve-se ao facto de o DevKit ainda não ter sido ligado ao Wi-Fi.

1. No DevKit, segure o **botão B,** pressione e liberte o botão **Reset** e, em seguida, desbloqueie o **botão B**. O dispositivo encontra-se agora no modo de ponto de acesso. Para confirmar, o ecrã apresenta "IoT DevKit - AP" e o endereço IP do portal de configuração.

1. No seu computador ou tablet, ligue-se ao nome da rede Wi-Fi mostrado no ecrã do dispositivo. A rede Wi-Fi começa com **AZ seguido** do endereço MAC. Quando se liga a esta rede, não tem acesso à Internet. Este estado é esperado e só se conecta a esta rede por um curto período de tempo enquanto configura o dispositivo.

1. Abra o seu navegador [http://192.168.0.1/](http://192.168.0.1/)web e navegue para . Os seguintes ecrãs da página web:

    ![Config UI](media/howto-connect-devkit/config-ui.png)

    Na página web, introduza:

    - O nome da sua rede Wi-Fi (SSID).
    - A sua senha de rede WiFi.
    - Os detalhes da ligação: introduza o ID do **dispositivo,** **o ID Scope**e a Chave Primária **SAS** de que tomou uma nota anteriormente.

    > [!NOTE]
    > Atualmente, o IoT DevKit só pode ligar a Wi-Fi de 2,4 GHz, 5 GHz não é suportado devido a restrições de hardware.

1. Escolha **o Dispositivo Configurado,** o dispositivo DevKit reinicia e executa a aplicação:

    ![Reiniciar UI](media/howto-connect-devkit/reboot-ui.png)

    O ecrã DevKit apresenta uma confirmação de que a aplicação está em execução:

    ![DevKit correndo](media/howto-connect-devkit/devkit-running.png)

O DevKit regista primeiro um novo dispositivo na aplicação IoT Central e depois começa a enviar dados.

## <a name="view-the-telemetry"></a>Ver a telemetria

Neste passo, vê a telemetria na sua aplicação Azure IoT Central.

Na sua aplicação IoT Central, selecione o separador **Dispositivos,** selecione o dispositivo que adicionou. No separador **Overview,** pode ver a telemetria do dispositivo DevKit:

![Visão geral do dispositivo central IoT](media/howto-connect-devkit/mxchip-overview-page.png)

## <a name="review-the-code"></a>Rever o código

Para rever o código ou modificá-lo e compilar, vá ao [Código amostras](https://docs.microsoft.com/samples/azure-samples/mxchip-iot-devkit-pnp/sample/).

## <a name="next-steps"></a>Passos seguintes

Se é um desenvolvedor de dispositivos, alguns sugeriu que os próximos passos são para:

- Ler sobre [conectividade do dispositivo na Central Azure IoT](./concepts-get-connected.md)
- Saiba como monitorizar a [conectividade do dispositivo utilizando o Azure CLI](./howto-monitor-devices-azure-cli.md)

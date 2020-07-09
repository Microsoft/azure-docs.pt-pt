---
title: Ligue um dispositivo DevKit à sua aplicação Azure IoT Central / Microsoft Docs
description: Como desenvolvedor de dispositivos, aprenda a ligar um dispositivo MXChip IoT DevKit à sua aplicação Azure IoT Central utilizando ioT Plug e Play (pré-visualização).
author: liydu
ms.author: liydu
ms.date: 12/03/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: bcf1dd2f89cf049d7da5b56170b2c13874c83ba4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81756804"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Ligue um dispositivo MXChip IoT DevKit à sua aplicação Azure IoT Central

*Este artigo aplica-se aos desenvolvedores de dispositivos.*

Este artigo mostra-lhe como ligar um dispositivo MXChip IoT DevKit (DevKit) a uma aplicação Azure IoT Central. O dispositivo utiliza o modelo IoT Plug and Play (pré-visualização) certificado para o dispositivo DevKit para configurar a sua ligação à IoT Central.

Neste artigo de como fazer, você:

- Obtenha os detalhes da ligação da sua aplicação IoT Central.
- Prepare o dispositivo e ligue-o à sua aplicação IoT Central.
- Veja a telemetria e as propriedades do dispositivo na IoT Central.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste artigo, precisa dos seguintes recursos:

- Um [dispositivo DevKit](https://aka.ms/iot-devkit-purchase).
- Uma aplicação IoT Central. Pode seguir os passos na [Criação de uma aplicação IoT Central](./quick-deploy-iot-central.md).

## <a name="get-device-connection-details"></a>Obtenha detalhes da ligação do dispositivo

1. Na sua aplicação Azure IoT Central, selecione o **separador Modelos de Dispositivo** e selecione **+ Novo**. Na secção **Utilize um modelo de dispositivo pré-configurado,** selecione **MXChip IoT DevKit**.

    ![Modelo de dispositivo para MXChip IoT DevKit](media/howto-connect-devkit/device-template.png)

1. Selecione **Seguinte: Personalize** e, em seguida, **Crie**.

1. Selecione **Devices.** Na lista de dispositivos, selecione **MXChip IoT DevKit** e selecione **+ Novo** para criar um novo dispositivo a partir do modelo.

    ![Novo dispositivo](media/howto-connect-devkit/new-device.png)

1. Na janela pop-up, introduza o **ID** do dispositivo como `SampleDevKit` e o Nome do **Dispositivo** como `MXChip IoT DevKit - Sample` . Certifique-se de que a opção **Simulada** está desligada. Em seguida, selecione **Criar**.

    ![Identificação do dispositivo e nome](media/howto-connect-devkit/device-id-name.png)

1. Selecione o dispositivo criado e, em seguida, selecione **Connect**. Tome nota do **ID Scope,** **ID do dispositivo**e da chave **primária**. Precisa destes valores mais tarde neste artigo de como fazer.

    ![Informações de ligação do dispositivo](media/howto-connect-devkit/device-connection-info.png)

## <a name="prepare-the-device"></a>Prepare o dispositivo

1. Descarregue o mais recente [firmware Azure IoT Central Plug and Play (pré-visualização)](https://github.com/Azure-Samples/mxchip-iot-devkit-pnp/raw/master/bin/iotc_devkit.bin) para o dispositivo DevKit do GitHub.

1. Ligue o dispositivo DevKit à sua máquina de desenvolvimento utilizando um cabo USB. No Windows, uma janela do explorador de ficheiros abre-se numa unidade mapeada para o armazenamento no dispositivo DevKit. Por exemplo, a unidade pode chamar-se **AZ3166 (D:)**.

1. Arraste o ficheiro **iotc_devkit.bin** para a janela de acionamento. Quando a cópia estiver concluída, o dispositivo reinicia com o novo firmware.

    > [!NOTE]
    > Se vir erros no ecrã como **No Wi-Fi, isto deve-se**ao facto de o DevKit ainda não estar ligado ao Wi-Fi.

1. No DevKit, mantenha premido **o botão B,** pressione e solte o botão **Reset** e, em seguida, liberte o **botão B**. O dispositivo encontra-se agora no modo ponto de acesso. Para confirmar, o ecrã apresenta "IoT DevKit - AP" e o endereço IP do portal de configuração.

1. No seu computador ou tablet, ligue-se ao nome da rede Wi-Fi mostrado no ecrã do dispositivo. A rede WiFi começa com **AZ-** seguido pelo endereço MAC. Quando se liga a esta rede, não tem acesso à Internet. Este estado é esperado, e você só se conecta a esta rede por um curto período de tempo enquanto configura o dispositivo.

1. Abra o seu navegador web e navegue para [http://192.168.0.1/](http://192.168.0.1/) . As seguintes páginas web exibem:

    ![Config UI](media/howto-connect-devkit/config-ui.png)

    Na página web, insira:

    - O nome da sua rede Wi-Fi (SSID).
    - A sua senha de rede Wi-Fi.
    - Os detalhes da ligação: introduza o **ID do dispositivo,** **o ID Scope**e **a Chave Primária SAS** que fez anteriormente.

    > [!NOTE]
    > Atualmente, o IoT DevKit só pode ligar-se a Wi-Fi de 2,4 GHz, 5 GHz não é suportado devido a restrições de hardware.

1. Escolha **o Dispositivo de Configuração,** o dispositivo DevKit reinicia e executa a aplicação:

    ![Reiniciar UI](media/howto-connect-devkit/reboot-ui.png)

    O ecrã DevKit apresenta uma confirmação de que a aplicação está em execução:

    ![DevKit correndo](media/howto-connect-devkit/devkit-running.png)

O DevKit regista primeiro um novo dispositivo na aplicação IoT Central e depois começa a enviar dados.

## <a name="view-the-telemetry"></a>Ver a telemetria

Neste passo, você vê a telemetria na sua aplicação Azure IoT Central.

Na sua aplicação IoT Central, selecione **o separador Dispositivos,** selecione o dispositivo adicionado. No **separador Visão** Geral, pode ver a telemetria a partir do dispositivo DevKit:

![Visão geral do dispositivo central IoT](media/howto-connect-devkit/mxchip-overview-page.png)

## <a name="review-the-code"></a>Rever o código

Para rever o código ou modificá-lo e compilá-lo, vá às [Amostras](https://docs.microsoft.com/samples/azure-samples/mxchip-iot-devkit-pnp/sample/)de Código .

## <a name="next-steps"></a>Próximos passos

Se você é um desenvolvedor de dispositivos, alguns passos sugeridos seguintes são:

- Leia sobre [a conectividade do dispositivo no Azure IoT Central](./concepts-get-connected.md)
- Saiba como monitorizar a [conectividade do dispositivo utilizando o Azure CLI](./howto-monitor-devices-azure-cli.md)

---
title: Conectar um dispositivo DevKit ao aplicativo de IoT Central do Azure | Microsoft Docs
description: Como desenvolvedor de dispositivos, aprenda a ligar um dispositivo MXChip IoT DevKit à sua aplicação Azure IoT Central utilizando ioT Plug e Play (pré-visualização).
author: liydu
ms.author: liydu
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: 929651264cc900e38ca24d4a2ea703a3c586aedd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024573"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Conectar um dispositivo MXChip IoT DevKit ao aplicativo IoT Central do Azure

Este artigo mostra como conectar um dispositivo DevKit (MXChip IoT DevKit) a um aplicativo de IoT Central do Azure. O dispositivo utiliza o modelo certificado IoT Plug and Play (pré-visualização) para o dispositivo DevKit para configurar a sua ligação à IoT Central.

Neste artigo de instruções, você:

- Obtenha os detalhes de conexão do seu aplicativo IoT Central.
- Prepare o dispositivo e conecte-o ao seu aplicativo IoT Central.
- Exiba a telemetria e as propriedades do dispositivo em IoT Central.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisa dos seguintes recursos:

- Um [dispositivo devkit](https://aka.ms/iot-devkit-purchase).
- Uma aplicação IoT Central. Pode seguir os passos na [Create a IoT Central application](./quick-deploy-iot-central.md).

## <a name="get-device-connection-details"></a>Obter detalhes de conexão do dispositivo

1. Na sua aplicação Azure IoT Central, selecione o separador **Modelos** de Dispositivo e selecione **+ Novo**. Na secção Utilize um modelo de **dispositivo pré-configurado,** selecione **MXChip IoT DevKit**.

    ![Modelo de dispositivo para MXChip IoT DevKit](media/howto-connect-devkit/device-template.png)

1. Selecione **A seguir: Personalizar** e **criar**.

1. Selecione o separador **Dispositivos.** Na lista de dispositivos, selecione **MXChip IoT DevKit** e selecione **+ Novo** para criar um novo dispositivo a partir do modelo.

    ![Novo dispositivo](media/howto-connect-devkit/new-device.png)

1. Na janela pop-up, insira a **ID do dispositivo** como `SampleDevKit` e o **nome do dispositivo** como `MXChip IoT DevKit - Sample`. Certifique-se de que a opção **Simulada** está desligada. Em seguida, selecione **Criar**.

    ![ID e nome do dispositivo](media/howto-connect-devkit/device-id-name.png)

1. Selecione o dispositivo que criou e, em seguida, selecione **Connect**. Tome nota do âmbito de **identificação,** **id do dispositivo**e da tecla **primária**. Precisa destes valores mais tarde neste artigo de como fazer.

    ![Informações de conexão do dispositivo](media/howto-connect-devkit/device-connection-info.png)

## <a name="prepare-the-device"></a>Preparar o dispositivo

1. Descarregue o mais recente [firmware de plug e play central azure IoT (pré-visualização)](https://github.com/Azure-Samples/mxchip-iot-devkit-pnp/raw/master/bin/iotc_devkit.bin) para o dispositivo DevKit a partir do GitHub.

1. Conecte o dispositivo DevKit à sua máquina de desenvolvimento usando um cabo USB. No Windows, uma janela Explorador de arquivos é aberta em uma unidade mapeada para o armazenamento no dispositivo DevKit. Por exemplo, a unidade pode ser chamada **AZ3166 (D:)** .

1. Arraste o ficheiro **iotc_devkit.bin** para a janela de acionamento. Quando a cópia for concluída, o dispositivo será reinicializado com o novo firmware.

    > [!NOTE]
    > Se você encontrar erros na tela, como **sem Wi-Fi**, isso ocorre porque o devkit ainda não foi conectado ao wifi.

1. No DevKit, mantenha o **botão b**, pressione e solte o botão **Redefinir** e, em seguida, solte o **botão b**. O dispositivo agora está no modo de ponto de acesso. Para confirmar, a tela exibe "IoT DevKit-AP" e o endereço IP do portal de configuração.

1. Em seu computador ou Tablet, conecte-se ao nome da rede WiFi mostrado na tela do dispositivo. A rede WiFi começa com **AZ-** seguido pelo endereço Mac. Quando você se conecta a essa rede, você não tem acesso à Internet. Esse estado é esperado e você só se conecta a essa rede por um curto período enquanto configura o dispositivo.

1. Abra o navegador da Web e navegue até [http://192.168.0.1/](http://192.168.0.1/). A página da Web a seguir exibe:

    ![Interface do usuário de configuração](media/howto-connect-devkit/config-ui.png)

    Na página da Web, digite:

    - O nome de sua rede WiFi (SSID).
    - Sua senha de rede WiFi.
    - Os detalhes da ligação: introduza o ID do **dispositivo,** **o ID Scope**e a Chave Primária **SAS** de que tomou uma nota anteriormente.

    > [!NOTE]
    > Atualmente, o IoT DevKit pode se conectar somente a Wi-Fi de 2,4 GHz, não há suporte para 5 GHz devido a restrições de hardware.

1. Escolha **Configurar dispositivo**, o dispositivo devkit reinicializa e executa o aplicativo:

    ![Reinicializar interface do usuário](media/howto-connect-devkit/reboot-ui.png)

    A tela DevKit exibe uma confirmação de que o aplicativo está em execução:

    ![DevKit em execução](media/howto-connect-devkit/devkit-running.png)

O DevKit primeiro registra um novo dispositivo no aplicativo IoT Central e, em seguida, inicia o envio de dados.

## <a name="view-the-telemetry"></a>Exibir a telemetria

Nesta etapa, você exibe a telemetria no aplicativo IoT Central do Azure.

No aplicativo IoT Central, selecione a guia **dispositivos** , selecione o dispositivo que você adicionou. Na guia **visão geral** , você pode ver a telemetria do dispositivo devkit:

![Visão geral do dispositivo IoT Central](media/howto-connect-devkit/mxchip-overview-page.png)

## <a name="review-the-code"></a>Rever o código

Para examinar o código ou modificá-lo e compilá-lo, vá para os [exemplos de código](https://docs.microsoft.com/samples/azure-samples/mxchip-iot-devkit-pnp/sample/).

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu como conectar um dispositivo DevKit ao aplicativo IoT Central do Azure, a próxima etapa sugerida é aprender a [configurar um modelo de dispositivo personalizado](./howto-set-up-template.md) para seu próprio dispositivo IOT.

---
title: Conectar um dispositivo DevKit ao aplicativo de IoT Central do Azure | Microsoft Docs
description: Como desenvolvedor de dispositivos, saiba como conectar um dispositivo MXChip IoT DevKit ao seu aplicativo de IoT Central do Azure usando o IoT Plug and Play.
author: liydu
ms.author: liydu
ms.date: 08/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: b7d2e1b08653cb8023ef6a5190ab53ecc3d568a6
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72297043"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Conectar um dispositivo MXChip IoT DevKit ao aplicativo IoT Central do Azure

Este artigo mostra como conectar um dispositivo DevKit (MXChip IoT DevKit) a um aplicativo de IoT Central do Azure. O dispositivo usa o modelo de Plug and Play de IoT certificado para o dispositivo DevKit para configurar sua conexão com IoT Central.

Neste artigo de instruções, você:

- Obtenha os detalhes de conexão do seu aplicativo IoT Central.
- Prepare o dispositivo e conecte-o ao seu aplicativo IoT Central.
- Exiba a telemetria e as propriedades do dispositivo em IoT Central.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisa dos seguintes recursos:

1. Um [dispositivo devkit](https://aka.ms/iot-devkit-purchase).
1. Um aplicativo IoT Central criado a partir do modelo de **aplicativo de visualização** . Você pode seguir as etapas em [criar um aplicativo de plug and Play de IOT](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="get-device-connection-details"></a>Obter detalhes de conexão do dispositivo

No aplicativo IoT Central do Azure, selecione a guia **Administração** e selecione **conexão do dispositivo**. Anote o **escopo da ID** e a **chave primária**.

![Detalhes de conexão do grupo de dispositivos](media/howto-connect-devkit-pnp/device-group-connection-details.png)

## <a name="prepare-the-device"></a>Preparar o dispositivo

1. Baixe o mais recente [firmware de plug and Play de IOT central predefinido do Azure](https://github.com/MXCHIP/IoTDevKit/raw/master/pnp/iotc_devkit/bin/iotc_devkit.bin) para o dispositivo devkit do github.

1. Conecte o dispositivo DevKit à sua máquina de desenvolvimento usando um cabo USB. No Windows, uma janela Explorador de arquivos é aberta em uma unidade mapeada para o armazenamento no dispositivo DevKit. Por exemplo, a unidade pode ser chamada **AZ3166 (D:)** .

1. Arraste o arquivo **iotc_devkit. bin** para a janela da unidade. Quando a cópia for concluída, o dispositivo será reinicializado com o novo firmware.

    > [!NOTE]
    > Se você encontrar erros na tela, como **sem Wi-Fi**, isso ocorre porque o devkit ainda não foi conectado ao wifi.

1. No DevKit, mantenha o **botão b**, pressione e solte o botão **Redefinir** e, em seguida, solte o **botão b**. O dispositivo agora está no modo de ponto de acesso. Para confirmar, a tela exibe "IoT DevKit-AP" e o endereço IP do portal de configuração.

1. Em seu computador ou Tablet, conecte-se ao nome da rede WiFi mostrado na tela do dispositivo. A rede WiFi começa com **AZ-** seguido pelo endereço Mac. Quando você se conecta a essa rede, você não tem acesso à Internet. Esse estado é esperado e você só se conecta a essa rede por um curto período enquanto configura o dispositivo.

1. Abra o navegador da Web e navegue até [http://192.168.0.1/](http://192.168.0.1/). A página da Web a seguir exibe:

    ![Interface do usuário de configuração](media/howto-connect-devkit-pnp/config-ui.png)

    Na página da Web, digite:

    - O nome de sua rede WiFi (SSID).
    - Sua senha de rede WiFi.
    - Os detalhes da conexão: a **ID do dispositivo** que você pode escolher por conta própria e o escopo da **ID** e a **chave primária de SAS do grupo** anotada anteriormente.

    > [!NOTE]
    > Atualmente, o IoT DevKit pode se conectar somente a Wi-Fi de 2,4 GHz, não há suporte para 5 GHz devido a restrições de hardware.

1. Escolha **Configurar dispositivo**, o dispositivo devkit reinicializa e executa o aplicativo:

    ![Reinicializar interface do usuário](media/howto-connect-devkit-pnp/reboot-ui.png)

    A tela DevKit exibe uma confirmação de que o aplicativo está em execução:

    ![DevKit em execução](media/howto-connect-devkit-pnp/devkit-running.png)

O DevKit primeiro registra um novo dispositivo no aplicativo IoT Central e, em seguida, inicia o envio de dados.

## <a name="view-the-telemetry"></a>Exibir a telemetria

Nesta etapa, você exibe a telemetria no aplicativo IoT Central do Azure.

No aplicativo IoT Central, selecione a guia **dispositivos** , selecione o dispositivo que você adicionou. Na guia **visão geral** , você pode ver a telemetria do dispositivo devkit:

   ![Visão geral do dispositivo IoT Central](media/howto-connect-devkit-pnp/mxchip-overview-page.png)

## <a name="review-the-code"></a>Rever o código

Para examinar o código ou modificá-lo e compilá-lo, vá para os [exemplos de código](https://docs.microsoft.com/samples/azure-samples/mxchip-iot-devkit-pnp/sample/).

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu como conectar um dispositivo DevKit ao aplicativo IoT Central do Azure, a próxima etapa sugerida é aprender a [configurar um modelo de dispositivo personalizado](./howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) para seu próprio dispositivo IOT.

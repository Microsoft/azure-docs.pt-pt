---
title: Conectar um dispositivo DevKit ao aplicativo de IoT Central do Azure | Microsoft Docs
description: Como desenvolvedor de dispositivos, saiba como conectar um dispositivo MXChip IoT DevKit ao seu aplicativo de IoT Central do Azure usando o IoT Plug and Play.
author: liydu
ms.author: liydu
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: 32dd3fa1fc137d786174e47d842f762c2a479d64
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849003"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application-preview-features"></a>Conectar um dispositivo MXChip IoT DevKit ao aplicativo IoT Central do Azure (recursos de visualização)

Este artigo mostra como conectar um dispositivo DevKit (MXChip IoT DevKit) a um aplicativo de IoT Central do Azure. O dispositivo usa o modelo de Plug and Play de IoT certificado para o dispositivo DevKit para configurar sua conexão com IoT Central.

Neste artigo de instruções, você:

- Obtenha os detalhes de conexão do seu aplicativo IoT Central.
- Prepare o dispositivo e conecte-o ao seu aplicativo IoT Central.
- Exiba a telemetria e as propriedades do dispositivo em IoT Central.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisa dos seguintes recursos:

- Um [dispositivo devkit](https://aka.ms/iot-devkit-purchase).
- Um aplicativo IoT Central criado a partir do modelo de **aplicativo de visualização** . Você pode seguir as etapas em [criar um aplicativo de plug and Play de IOT](./quick-deploy-iot-central.md).

## <a name="get-device-connection-details"></a>Obter detalhes de conexão do dispositivo

1. No aplicativo IoT Central do Azure, selecione a guia **modelos de dispositivo** e selecione **novo**. Na seção de **usar um modelo de dispositivo pré-configurado**, escolha **MXChip IOT devkit** na lista. E escolha **Avançar: Personalizar** e **criar**.

    ![Modelo de dispositivo para MXChip IoT DevKit](media/howto-connect-devkit/device-template.png)

1. Selecione a guia **dispositivos** , na lista dispositivos, selecione **MXChip IOT devkit** e selecione **novo** para criar um novo dispositivo do modelo de dispositivo.

    ![Novo dispositivo](media/howto-connect-devkit/new-device.png)

1. Na janela pop-up, insira a **ID do dispositivo** como `SampleDevKit` e o **nome do dispositivo** como `MXChip IoT DevKit - Sample`. Verifique se a opção **Simulated** está desativada. Em seguida, selecione **Criar**.

    ![ID e nome do dispositivo](media/howto-connect-devkit/device-id-name.png)

1. Clique no dispositivo que você acabou de criar e selecione **conectar**. Anote o **escopo da ID**, a **ID do dispositivo** e a **chave primária**.

    ![Informações de conexão do dispositivo](media/howto-connect-devkit/device-connection-info.png)

## <a name="prepare-the-device"></a>Preparar o dispositivo

1. Baixe o mais recente [firmware de plug and Play de IOT central predefinido do Azure](https://github.com/MXCHIP/IoTDevKit/raw/master/pnp/iotc_devkit/bin/iotc_devkit.bin) para o dispositivo devkit do github.

1. Conecte o dispositivo DevKit à sua máquina de desenvolvimento usando um cabo USB. No Windows, uma janela Explorador de arquivos é aberta em uma unidade mapeada para o armazenamento no dispositivo DevKit. Por exemplo, a unidade pode ser chamada **AZ3166 (D:)** .

1. Arraste o arquivo **iotc_devkit. bin** para a janela da unidade. Quando a cópia for concluída, o dispositivo será reinicializado com o novo firmware.

    > [!NOTE]
    > Se você encontrar erros na tela, como **sem Wi-Fi**, isso ocorre porque o devkit ainda não foi conectado ao wifi.

1. No DevKit, mantenha o **botão b**, pressione e solte o botão **Redefinir** e, em seguida, solte o **botão b**. O dispositivo agora está no modo de ponto de acesso. Para confirmar, a tela exibe "IoT DevKit-AP" e o endereço IP do portal de configuração.

1. Em seu computador ou Tablet, conecte-se ao nome da rede WiFi mostrado na tela do dispositivo. A rede WiFi começa com **AZ-** seguido pelo endereço Mac. Quando você se conecta a essa rede, você não tem acesso à Internet. Esse estado é esperado e você só se conecta a essa rede por um curto período enquanto configura o dispositivo.

1. Abra o navegador da Web e navegue até [http://192.168.0.1/](http://192.168.0.1/). A página da Web a seguir exibe:

    ![Interface do usuário de configuração](media/howto-connect-devkit/config-ui.png)

    Na página da Web, digite:

    - O nome de sua rede WiFi (SSID).
    - Sua senha de rede WiFi.
    - Os detalhes da conexão: Insira a **ID do dispositivo**, o escopo da **ID** e a **chave primária SAS** anotada anteriormente.

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

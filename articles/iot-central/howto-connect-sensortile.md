---
title: Conectar um dispositivo SensorTile. Box ao seu aplicativo de IoT Central do Azure | Microsoft Docs
description: Como desenvolvedor de dispositivos, saiba como conectar um dispositivo SensorTile. Box ao seu aplicativo de IoT Central do Azure.
author: sarahhubbard
ms.author: sahubbar
ms.date: 04/24/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: sandeep.pujar
ms.openlocfilehash: ce0c5abe6e89094623c07afa2d1c85903e0e7ee7
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877435"
---
# <a name="connect-sensortilebox-device-to-your-azure-iot-central-application"></a>Conectar o dispositivo SensorTile. Box ao seu aplicativo IoT Central do Azure

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Este artigo descreve como, como desenvolvedor de dispositivos, conectar um dispositivo SensorTile. Box ao seu aplicativo Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas neste artigo, você precisa dos seguintes recursos:

* Um dispositivo SensorTile. Box. Para obter mais informações, consulte [SensorTile. Box](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mems-motion-sensor-eval-boards/steval-mksbox1v1.html).
* O aplicativo de sensor ST BLE instalado em seu dispositivo Android, você pode [baixá-lo aqui](https://play.google.com/store/apps/details?id=com.st.bluems). Para obter mais informações, visite: [Sensor de ST BLE](https://www.st.com/stblesensor)
* Um aplicativo de IoT Central do Azure criado com base no modelo de aplicativo **DevKits** . Para obter mais informações, veja [criar um início rápido da aplicação](quick-deploy-iot-central.md).
* Adicione o modelo de dispositivo **SensorTile. Box** ao seu aplicativo IOT central visitando a página **modelos de dispositivo** , clicando em **+ novo**e selecionando o modelo **SensorTile. Box** .

### <a name="get-your-device-connection-details"></a>Obter detalhes de conexão do dispositivo

No aplicativo IoT Central do Azure, adicione um dispositivo real do modelo de dispositivo **SensorTile. Box** e anote os detalhes de conexão do dispositivo: **ID do escopo**, **ID do dispositivo**e **chave primária**:

1. Adicione um dispositivo de Device Explorer. Selecione **+ novo > real** para adicionar um dispositivo real.

    * Insira uma ID de **dispositivo**em minúsculas ou use a **ID de dispositivo**sugerida.
    * Insira um **nome de dispositivo**ou use o nome sugerido

    ![Adicionar Dispositivo](media/howto-connect-sensortile/real-device.png)

1. Para obter os detalhes de conexão do dispositivo, a **ID do escopo**, a **ID do dispositivo**e a **chave primária**, selecione **conectar** na página do dispositivo.

    ![Detalhes da ligação](media/howto-connect-sensortile/connect-device.png)

1. Anote os detalhes da conexão. Você estará temporariamente desconectado da Internet quando preparar o dispositivo DevKit na próxima etapa.

## <a name="set-up-the-sensortilebox-with-the-mobile-application"></a>Configurar o SensorTile. Box com o aplicativo móvel

Nesta seção, você aprenderá a enviar por push o firmware do aplicativo para o dispositivo. Em seguida, você deve enviar os dados do dispositivo para IoT Central por meio do aplicativo móvel ST BLE sensor usando conectividade BLE (Bluetooth de baixa energia).

1. Abra o aplicativo de sensor ST BLE e pressione o botão **criar um novo aplicativo** .

    ![Criar aplicação](media/howto-connect-sensortile/create-app.png)

1. Selecione o aplicativo **indicador** .
1. Pressione o botão carregar.

    ![Upload de indicador](media/howto-connect-sensortile/barometer-upload.png)

1. Pressione o botão reproduzir associado à sua SensorTile. Box.
1. Quando o processo for concluído, o SensorTile. Box transmitirá a temperatura, a pressão e a umidade sobre BLE.

## <a name="connect-the-sensortilebox-to-the-cloud"></a>Conectar o SensorTile. Box à nuvem

Nesta seção, você aprenderá a conectar o SensorTile. Box ao aplicativo móvel e a conectar o aplicativo móvel à nuvem.

1. Usando o menu à esquerda, selecione o botão **log de nuvem** .

    ![Log de nuvem](media/howto-connect-sensortile/cloud-logging.png)

1. Selecione **Azure IOT central** como o provedor de nuvem.
1. Insira a ID do dispositivo e a ID do escopo que foram indicadas anteriormente.

    ![Credenciais](media/howto-connect-sensortile/credentials.png)

1. Selecione o botão de opção **chave do aplicativo** .
1. Clique em **conectar** e selecione os dados de telemetria que você deseja carregar.
1. Após alguns segundos, os dados aparecerão no painel do aplicativo IoT Central.

## <a name="sensortilebox-device-template-details"></a>Detalhes do modelo de dispositivo SensorTile. Box

Um aplicativo criado a partir do modelo de dispositivo SensorTile. Box com as seguintes características:

### <a name="telemetry"></a>Telemetria

| Nome do campo     | Unidades  | Mínimo | Máximo | Casas decimais |
| -------------- | ------ | ------- | ------- | -------------- |
| humidade       | %      | 30       | 90     | 1              |
| Temp           | °     | 0     | 40     | 1              |
| pressure       | mbar    | 900     | 1100    | 2              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| acelerômetro | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | DPS   | -3276   | 3276    | 1              |
| gyroscopeY     | DPS   | -3276   | 3276    | 1              |
| gyroscopeZ     | DPS   | -3276   | 3276    | 1              |
| FFT_X     |    |    |     |               |
| FFT_Y     |    |    |     |               |
| FFT_Z     |    |    |     |               |

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu como conectar um SensorTile. Box ao seu aplicativo IoT Central do Azure, a próxima etapa sugerida é aprender [como configurar um modelo de dispositivo personalizado](howto-set-up-template.md) para seu próprio dispositivo IOT.

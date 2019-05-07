---
title: Ligar um dispositivo de SensorTile.box à sua aplicação do Azure IoT Central | Documentos da Microsoft
description: Como desenvolvedor de dispositivo, saiba como ligar um dispositivo de SensorTile.box à sua aplicação do Azure IoT Central.
author: sarahhubbard
ms.author: sahubbar
ms.date: 04/24/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: sandeep.pujar
ms.openlocfilehash: 580a8baa19e8ed4fc3f4449ead9d8aedbc4c039a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160909"
---
# <a name="connect-sensortilebox-device-to-your-azure-iot-central-application"></a>Ligar o dispositivo de SensorTile.box à sua aplicação do Azure IoT Central

Este artigo descreve como, como um desenvolvedor de dispositivo, para ligar um dispositivo de SensorTile.box a sua aplicação do Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Antes de começar

Para concluir os passos neste artigo, tem os seguintes recursos:

* Um dispositivo de SensorTile.box, consulte [SensorTile.box](https://www.st.com/content/st_com/en/products/evaluation-tools/SensorTile.box) para obter mais informações.
* A aplicação de ST var Sensor instalada no seu dispositivo Android, pode [transferi-lo aqui] (https://play.google.com/store/apps/details?id=com.st.bluems). Para obter mais informações, visite: [ST var Sensor] (http://www.st.com/stblesensor)
* Uma aplicação do Azure IoT Central, criada a partir da **DevKits** o modelo de aplicativo. Para obter mais informações, veja [criar um início rápido da aplicação](quick-deploy-iot-central.md).
* Adicionar a **SensorTile.box** modelo de dispositivo na sua aplicação do Centro de IoT, visitando a **modelos de dispositivos** página, clicando em **+ novo**e selecionando o **SensorTile** modelo.

### <a name="get-your-device-connection-details"></a>Obter o seu dispositivo detalhes de ligação

Na aplicação do Azure IoT Central, adicionar um dispositivo real a partir da **SensorTile.box** modelo do dispositivo e tome nota dos detalhes da ligação de dispositivo: **Definir âmbito de ID, a ID do dispositivo e a chave primária**:

1. Adicione um dispositivo a partir do Device Explorer. Selecione **+ novo > Real** para adicionar um dispositivo real.

    * Introduza uma minúsculas **ID do dispositivo**, ou utilize sugeridos **ID do dispositivo**.
    * Introduza um **nome do dispositivo**, ou utilize o nome sugerido

    ![Adicionar Dispositivo](media/howto-connect-sensortile/real-device.png)

1. Para obter detalhes de ligação, de dispositivo **ID de âmbito**, **ID do dispositivo**, e **chave primária**, selecione **Connect** na página do dispositivo.

    ![Detalhes da ligação](media/howto-connect-sensortile/connect-device.png)

1. Tome nota dos detalhes da ligação. Temporariamente estiver desligado da internet quando preparar o seu dispositivo DevKit no próximo passo.

## <a name="set-up-the-sensortilebox-with-the-mobile-application"></a>Configurar o SensorTile.box com a aplicação móvel

Nesta secção, irá aprender como enviar por push o firmware de aplicação no dispositivo e enviar os dados do dispositivo ao IoT Central através da aplicação móvel do Sensor de var ST por meio de conectividade de energia de baixa de Bluetooth (var).
1. Abra a aplicação de ST var Sensor e prima a **criar uma nova aplicação** botão.

    ![Criar aplicação](media/howto-connect-sensortile/create-app.png)

1. Selecione o **indicador** aplicação.
1. Prima o botão de carregamento.

    ![Carregamento de indicador](media/howto-connect-sensortile/barometer-upload.png)

1. Prima o botão play associado com seu SensorTile.box.
1. Quando o processo estiver concluído, o SensorTile.box transmitirá a temperatura, a pressão e a umidade por var.

## <a name="connect-the-sensortilebox-to-the-cloud"></a>Ligar o SensorTile.box para a cloud

Nesta secção, ficará a saber como ligar o SensorTile.box à aplicação móvel e ligar a aplicação móvel para a cloud.
1. Utilizando o menu à esquerda, selecione o **registo de Cloud** botão.

    ![Registo da cloud](media/howto-connect-sensortile/cloud-logging.png)

1. Selecione **do Azure IoT Central** como o fornecedor de cloud.
1. Insira o ID de dispositivo e o ID de âmbito que apontou anteriormente.

    ![Credenciais](media/howto-connect-sensortile/credentials.png)

1. Selecione o **chave da aplicação** botão de opção.
1. Clique em **Connect** e selecione os dados de telemetria que pretende carregar.
1. Após alguns segundos, os dados serão apresentados no dashboard de aplicações de IoT Central.

## <a name="sensortilebox-device-template-details"></a>Detalhes do modelo de dispositivo de SensorTile.box

Uma aplicação criada a partir do modelo de dispositivo SensorTile.box com as seguintes características:

### <a name="telemetry"></a>Telemetria

| Nome do campo     | Unidades  | Mínimo | Máximo | Casas decimais |
| -------------- | ------ | ------- | ------- | -------------- |
| humidade       | %      | 30       | 90     | 1              |
| temp           | °C     | 0     | 40     | 1              |
| pressure       | mbar    | 900     | 1100    | 2              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | Pontos de distribuição   | -3276   | 3276    | 1              |
| gyroscopeY     | Pontos de distribuição   | -3276   | 3276    | 1              |
| gyroscopeZ     | Pontos de distribuição   | -3276   | 3276    | 1              |
| FFT_X     |    |    |     |               |
| FFT_Y     |    |    |     |               |
| FFT_Z     |    |    |     |               |


## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como ligar um SensorTile.box à sua aplicação do Azure IoT Central, a próxima etapa sugerida é saber como [configurar um modelo de dispositivo personalizado](howto-set-up-template.md) para o seu próprio dispositivo IoT.

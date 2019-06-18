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
ms.openlocfilehash: 8c1b4a4ab834b2203a7e0b6e4e9e366c3fc38774
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65472201"
---
# <a name="connect-sensortilebox-device-to-your-azure-iot-central-application"></a>Ligar o dispositivo de SensorTile.box à sua aplicação do Azure IoT Central

Este artigo descreve como, como um desenvolvedor de dispositivo, para ligar um dispositivo de SensorTile.box a sua aplicação do Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Antes de começar

Para concluir os passos neste artigo, tem os seguintes recursos:

* Um dispositivo de SensorTile.box. Para obter mais informações, consulte [SensorTile.box](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mems-motion-sensor-eval-boards/steval-mksbox1v1.html).
* A aplicação de ST var Sensor instalada no seu dispositivo Android, pode [baixá-lo a partir daqui](https://play.google.com/store/apps/details?id=com.st.bluems). Para mais informações, visite: [Sensor de var ST](https://www.st.com/stblesensor)
* Uma aplicação do Azure IoT Central, criada a partir da **DevKits** o modelo de aplicativo. Para obter mais informações, veja [criar um início rápido da aplicação](quick-deploy-iot-central.md).
* Adicionar a **SensorTile.box** modelo de dispositivo na sua aplicação do Centro de IoT, visitando a **modelos de dispositivos** página, clicando em **+ novo**e selecionando o **SensorTile.box** modelo.

### <a name="get-your-device-connection-details"></a>Obter o seu dispositivo detalhes de ligação

Na aplicação do Azure IoT Central, adicionar um dispositivo real a partir da **SensorTile.box** modelo do dispositivo e tome nota dos detalhes da ligação de dispositivo: **Definir âmbito de ID**, **ID do dispositivo**, e **chave primária**:

1. Adicione um dispositivo a partir do Device Explorer. Selecione **+ novo > Real** para adicionar um dispositivo real.

    * Introduza uma minúsculas **ID do dispositivo**, ou utilize sugeridos **ID do dispositivo**.
    * Introduza um **nome do dispositivo**, ou utilize o nome sugerido

    ![Adicionar Dispositivo](media/howto-connect-sensortile/real-device.png)

1. Para obter detalhes de ligação, de dispositivo **ID de âmbito**, **ID do dispositivo**, e **chave primária**, selecione **Connect** na página do dispositivo.

    ![Detalhes da ligação](media/howto-connect-sensortile/connect-device.png)

1. Tome nota dos detalhes da ligação. Temporariamente estiver desligado da internet quando preparar o seu dispositivo DevKit no próximo passo.

## <a name="set-up-the-sensortilebox-with-the-mobile-application"></a>Configurar o SensorTile.box com a aplicação móvel

Nesta secção, saiba como enviar o firmware de aplicação no dispositivo. Então como enviar os dados do dispositivo ao IoT Central através da aplicação móvel de ST var Sensor com conectividade de energia de baixa de Bluetooth (var).

1. Abra a aplicação de ST var Sensor e prima a **criar uma nova aplicação** botão.

    ![Criar aplicação](media/howto-connect-sensortile/create-app.png)

1. Selecione o **indicador** aplicação.
1. Prima o botão de carregamento.

    ![Carregamento de indicador](media/howto-connect-sensortile/barometer-upload.png)

1. Prima o botão play associado com seu SensorTile.box.
1. Quando o processo estiver concluído, o SensorTile.box transmite a temperatura, pressão e humidade sobre var.

## <a name="connect-the-sensortilebox-to-the-cloud"></a>Ligar o SensorTile.box para a cloud

Nesta secção irá aprender a ligar o SensorTile.box à aplicação móvel e ligar a aplicação móvel para a cloud.

1. Utilizando o menu à esquerda, selecione o **registo de Cloud** botão.

    ![Registo da cloud](media/howto-connect-sensortile/cloud-logging.png)

1. Selecione **do Azure IoT Central** como o fornecedor de cloud.
1. Insira o ID de dispositivo e o ID de âmbito que apontou anteriormente.

    ![Credenciais](media/howto-connect-sensortile/credentials.png)

1. Selecione o **chave da aplicação** botão de opção.
1. Clique em **Connect** e selecione os dados de telemetria que pretende carregar.
1. Após alguns segundos, são apresentados os dados no dashboard de aplicações IoT Central.

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

Agora que aprendeu como ligar um SensorTile.box à sua aplicação do Azure IoT Central, a próxima etapa sugerida é saber [como configurar um modelo de dispositivo personalizado](howto-set-up-template.md) para o seu próprio dispositivo IoT.

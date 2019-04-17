---
title: Ligar um Raspberry Pi a sua aplicação do Azure IoT Central (Python) | Documentos da Microsoft
description: Como desenvolvedor de dispositivo, como ligar um Raspberry Pi a sua aplicação do Azure IoT Central com o Python.
author: dominicbetts
ms.author: dobett
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: eccc4100c89c971e264b9b915cd17b9f5ce4477b
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617451"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Ligar um Raspberry Pi a sua aplicação do Azure IoT Central (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

Este artigo descreve como, como um desenvolvedor de dispositivo, para ligar um Raspberry Pi a sua aplicação do Microsoft Azure IoT Central com o Python de linguagem de programação.

## <a name="before-you-begin"></a>Antes de começar

Para concluir os passos neste artigo, tem os seguintes componentes:

* Uma aplicação do Azure IoT Central, criada a partir da **Devkits exemplo** o modelo de aplicativo. Para obter mais informações, veja [criar um início rápido da aplicação](quick-deploy-iot-central.md).
* Um dispositivo de Raspberry Pi com o sistema de operativo Raspbian. O Raspberry Pi tem de ser capaz de ligar à internet. Para obter mais informações, consulte [configurar o seu Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

## <a name="sample-devkits-application"></a>**Exemplo Devkits** aplicação

Um aplicativo criado a partir da **Devkits de exemplo** inclui o modelo de aplicativo um **Raspberry Pi** modelo de dispositivo com as seguintes características:

- Telemetria, que inclui as seguintes medidas que recolherá o dispositivo:
  - Humidade
  - Temperatura
  - Pressão
  - Magnetômetro (X, Y, Z)
  - Acelerômetro (X, Y, Z)
  - Por giroscópio (X, Y, Z)
- Definições
  - Tensão
  - Atual
  - Ventoinha velocidade
  - Botão de alternar do Runtime de integração.
- Propriedades
  - Morrem número propriedade do dispositivo
  - Propriedade de cloud de localização

Para todos os detalhes da configuração do modelo de dispositivo, consulte a [detalhes do modelo de dispositivo do Raspberry Pi](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Adicionar um dispositivo real

Na aplicação do Azure IoT Central, adicionar um dispositivo real a partir da **Raspberry Pi** modelo do dispositivo. Tome nota do dispositivo detalhes de ligação (**ID de âmbito**, **ID do dispositivo**, e **chave primária**). Para obter mais informações, consulte [adicionar um dispositivo real à sua aplicação do Azure IoT Central](tutorial-add-device.md).

### <a name="configure-the-raspberry-pi"></a>Configurar o Raspberry Pi

Os passos seguintes descrevem como transferir e configurar a aplicação de Python de exemplo do GitHub. Este aplicativo de exemplo:

* Envia os valores de propriedade e telemetria para o Azure IoT Central.
* Responde a definir as alterações feitas no Azure IoT Central.

Para configurar o dispositivo [siga as instruções passo a passo no GitHub](https://github.com/Azure/iot-central-firmware/blob/master/RaspberryPi/README.md).

1. Quando o dispositivo está configurado, o seu dispositivo começa a enviar medições de telemetria para o Azure IoT Central.
1. Na aplicação do Azure IoT Central, pode ver como o código que executa o Raspberry Pi interage com o aplicativo:

    * Sobre o **medidas** página para o seu dispositivo real, pode ver a telemetria enviada o Raspberry PI.
    * Sobre o **definições** página, pode alterar as definições no Raspberry Pi, por exemplo, a velocidade de tensão e fan. Quando o Raspberry Pi reconhece a alteração, a definição mostra como **sincronizado**.

## <a name="raspberry-pi-device-template-details"></a>Detalhes do modelo do raspberry Pi dispositivo

Um aplicativo criado a partir da **Devkits de exemplo** inclui o modelo de aplicativo um **Raspberry Pi** modelo de dispositivo com as seguintes características:

### <a name="telemetry-measurements"></a>Medições de telemetria

| Nome do campo     | Unidades  | Mínimo | Máximo | Casas decimais |
| -------------- | ------ | ------- | ------- | -------------- |
| humidade       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Definições

Definições de numérico

| Nome a apresentar | Nome do campo | Unidades | Casas decimais | Mínimo | Máximo | Inicial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Tensão      | setVoltage | Volts | 0              | 0       | 240     | 0       |
| Atual      | setCurrent | AMPS  | 0              | 0       | 100     | 0       |
| Ventoinha velocidade    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

Ativar/desativar definições

| Nome a apresentar | Nome do campo | No texto | Desativar o texto | Inicial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | OFF      | Desativado     |

### <a name="properties"></a>Propriedades

| Type            | Nome a apresentar | Nome do campo | Tipo de dados |
| --------------- | ------------ | ---------- | --------- |
| Propriedade do dispositivo | Morrem número   | dieNumber  | número    |
| Texto            | Localização     | localização   | N/A       |

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como ligar um Raspberry Pi a sua aplicação do Azure IoT Central, a próxima etapa sugerida é saber como [configurar um modelo de dispositivo personalizado](howto-set-up-template.md) para o seu próprio dispositivo IoT.

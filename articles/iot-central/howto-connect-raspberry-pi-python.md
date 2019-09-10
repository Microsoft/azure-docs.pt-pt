---
title: Conectar um Raspberry Pi ao seu aplicativo do Azure IoT Central (Python) | Microsoft Docs
description: Como um desenvolvedor de dispositivos, como conectar um Raspberry Pi ao seu aplicativo IoT Central do Azure usando o Python.
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 5c6e99c62d199c36081113011845a3bf2607bcb3
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842551"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Conectar um Raspberry Pi ao seu aplicativo do Azure IoT Central (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Este artigo descreve como, como desenvolvedor de dispositivos, conectar um Raspberry Pi ao seu aplicativo Microsoft Azure IoT Central usando a linguagem de programação Python.

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas neste artigo, você precisa dos seguintes componentes:

* Um aplicativo IoT Central do Azure criado no modelo de aplicativo **devkits de exemplo** . Para obter mais informações, veja [criar um início rápido da aplicação](quick-deploy-iot-central.md).
* Um dispositivo Raspberry Pi que executa o sistema operacional Raspbian. O Raspberry Pi deve ser capaz de se conectar à Internet. Para obter mais informações, consulte [Configurando o Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

> [!TIP]
> Para saber mais sobre como configurar e conectar-se a um dispositivo Raspberry Pi, visite introdução [ao Raspberry Pi](https://projects.raspberrypi.org/en/pathways/getting-started-with-raspberry-pi)

## <a name="sample-devkits-application"></a>Aplicativo **devkits de exemplo**

Um aplicativo criado no modelo de aplicativo **devkits de exemplo** inclui um modelo de dispositivo **Raspberry Pi** com as seguintes características:

- Telemetria, que inclui as seguintes medidas que o dispositivo coletará:
  - Humidade
  - Temperatura
  - Pressão
  - Magnetômetro (X, Y, Z)
  - Acelerômetro (X, Y, Z)
  - Giroscópio (X, Y, Z)
- Definições
  - Volta
  - Atual
  - Velocidade do ventilador
  - Alternância de IR.
- properties
  - Propriedade de dispositivo de número de matriz
  - Propriedade de nuvem de local

Para obter os detalhes completos da configuração do modelo de dispositivo, consulte os [detalhes do modelo de dispositivo do Raspberry Pi](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Adicionar um dispositivo real

No aplicativo IoT Central do Azure, adicione um dispositivo real do modelo de dispositivo **Raspberry Pi** . Anote os detalhes de conexão do dispositivo (**ID do escopo**, **ID do dispositivo**e **chave primária**). Para obter mais informações, consulte [Adicionar um dispositivo real ao aplicativo IOT central do Azure](tutorial-add-device.md).

### <a name="configure-the-raspberry-pi"></a>Configurar o Raspberry Pi

As etapas a seguir descrevem como baixar e configurar o aplicativo Python de exemplo do GitHub. Este aplicativo de exemplo:

* Envia valores de telemetria e propriedade para IoT Central do Azure.
* Responde às alterações de configuração feitas no Azure IoT Central.

1. Conecte-se a um ambiente de Shell em seu Raspberry Pi, seja da área de trabalho do Raspberry Pi ou remotamente usando SSH.

1. Execute o seguinte comando para instalar o IoT Central cliente Python:

    ```bash
    pip install iotc
    ```

1. Baixe o código Python de exemplo:

    ```bash
    curl -O https://raw.githubusercontent.com/Azure/iot-central-firmware/master/RaspberryPi/app.py
    ```

1. Edite `app.py` o arquivo baixado e substitua os `DEVICE_ID`espaços `SCOPE_ID`reservados, `PRIMARY/SECONDARY device KEY` e pelos valores de conexão anotados anteriormente. Guarde as alterações.

    > [!TIP]
    > No Shell no Raspberry Pi, você pode usar os editores de texto **nano** ou **vi** .

1. Use o seguinte comando para executar o exemplo:

    ```bash
    python app.py
    ```

    O Raspberry Pi começa a enviar medidas de telemetria para o Azure IoT Central.

1. No aplicativo IoT Central do Azure, você pode ver como o código em execução no Raspberry Pi interage com o aplicativo:

    * Na página **medidas** do seu dispositivo real, você pode ver a telemetria enviada do Raspberry Pi.
    * Na página **Propriedades** , você pode ver a propriedade de dispositivo **número de matriz** .
    * Na página **configurações** , você pode alterar as configurações no Raspberry Pi, como tensão e velocidade do ventilador. Quando o Raspberry Pi reconhece a alteração, a configuração é mostrada como **sincronizada**.

## <a name="raspberry-pi-device-template-details"></a>Detalhes do modelo de dispositivo Raspberry Pi

Um aplicativo criado no modelo de aplicativo **devkits de exemplo** inclui um modelo de dispositivo **Raspberry Pi** com as seguintes características:

### <a name="telemetry-measurements"></a>Medições de telemetria

| Nome do campo     | Unidades  | Mínimo | Máximo | Casas decimais |
| -------------- | ------ | ------- | ------- | -------------- |
| humidade       | %      | 0       | 100     | 0              |
| Temp           | °     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| acelerômetro | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Definições

Configurações numéricas

| Display name | Nome do campo | Unidades | Casas decimais | Mínimo | Máximo | Inicial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Volta      | setVoltage | V | 0              | 0       | 240     | 0       |
| Atual      | SetCurrent | 2,0  | 0              | 0       | 100     | 0       |
| Velocidade do ventilador    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

Alternar configurações

| Display name | Nome do campo | Em texto | Fora do texto | Inicial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ATIVADO      | OFF      | Desativado     |

### <a name="properties"></a>properties

| Type            | Display name | Nome do campo | Tipo de dados |
| --------------- | ------------ | ---------- | --------- |
| Propriedade do dispositivo | Número do chip   | dieNumber  | number    |
| Text            | Location     | location   | N/A       |

## <a name="next-steps"></a>Passos Seguintes

Agora que você aprendeu como conectar um Raspberry Pi ao seu aplicativo IoT Central do Azure, a próxima etapa sugerida é aprender como [configurar um modelo de dispositivo personalizado](howto-set-up-template.md) para seu próprio dispositivo IOT.

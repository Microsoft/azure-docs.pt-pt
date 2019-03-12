---
title: Ligar um dispositivo de DevKit à sua aplicação do Azure IoT Central | Documentos da Microsoft
description: Como desenvolvedor de dispositivo, saiba como ligar um dispositivo de MXChip IoT DevKit a sua aplicação do Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 150f2b9155d5e920a7394e2fa55ce28701497868
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57763015"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Ligar um dispositivo de MXChip IoT DevKit a sua aplicação do Azure IoT Central

Este artigo descreve como, como um desenvolvedor de dispositivo, para ligar um dispositivo de MXChip IoT DevKit (DevKit) à sua aplicação do Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Antes de começar

Para executar os passos descritos neste artigo é necessário o seguinte:

1. Uma aplicação do Azure IoT Central, criada a partir da **Devkits exemplo** o modelo de aplicativo. Para obter mais informações, veja [criar um início rápido da aplicação](quick-deploy-iot-central.md).
1. Um dispositivo de DevKit. Para comprar um dispositivo de DevKit, visite [MXChip IoT DevKit](http://mxchip.com/az3166).

## <a name="sample-devkits-application"></a>Exemplo de aplicação Devkits

Um aplicativo criado a partir da **Devkits de exemplo** inclui o modelo de aplicativo um **MXChip** modelo de dispositivo com as seguintes características:

- Telemetria que contém as medidas para o dispositivo **humidade**, **temperatura**, **pressão**, **Magnetômetro** (medida ao longo de X, Y, eixo Z), **Acelerômetro** (medido ao longo de X, Y, eixo Z) e **Gyroscope** (medido ao longo de X, Y, eixo Z).
- Estado que contém uma medida de exemplo para **estado do dispositivo**.
- Medida de eventos com um **premido do botão B** eventos. 
- As definições que mostra **tensão**, **atual**, **ventoinha velocidade**e um **IR** Ativar/desativar.
- Propriedades que contém a propriedade do dispositivo **morrem número** e **localização do dispositivo** que é uma propriedade de localização, bem como num **fabricados no** propriedade da cloud. 

Para obter detalhes completos sobre a configuração, consulte [detalhes do modelo de dispositivo MXChip](#mxchip-device-template-details)


## <a name="add-a-real-device"></a>Adicionar um dispositivo real

Na aplicação do Azure IoT Central, adicionar um dispositivo real a partir do **MXChip** modelo de dispositivo e tome nota dos detalhes da ligação de dispositivo (**chave de ID de âmbito, o ID de dispositivo e o principal**).

1. Adicionar uma **dispositivo real** a partir do Device Explorer, selecione **+ novo > Real** para adicionar um dispositivo real.

    * Introduza o Id de dispositivo **<span style="color:Red">(deve ser minúscula)</span>** ou utilize o ID de dispositivo sugerido.
    * Introduza o nome do dispositivo ou utilize o nome sugerido

    ![Adicionar Dispositivo](media/howto-connect-devkit/add-device.png)

1. Obter os detalhes de ligação, tal como **chave de ID de âmbito, o ID de dispositivo e o primário** para o dispositivo foi adicionado ao selecionar **Connect** na página do dispositivo.

    ![Detalhes da ligação](media/howto-connect-devkit/device-connect.png)

1. Certifique-se guardar estes detalhes, conforme temporariamente irá obter desligado da internet para preparar o dispositivo DevKit.

### <a name="prepare-the-devkit-device"></a>Preparar o dispositivo DevKit

> [!NOTE]
> Se tiver utilizado anteriormente o dispositivo e tem Wi-Fi credenciais armazenadas e gostariam de reconfigurar o dispositivo utilize uma rede diferente de Wi-Fi, a cadeia de ligação ou a medição de telemetria, prima a **uma** e **B** botões no quadro em simultâneo. Se não funcionar, prima **repor** botão e tente novamente.

#### <a name="to-prepare-the-devkit-device"></a>Para preparar o dispositivo DevKit

1. Baixe o firmware do Azure IoT Central previamente criado mais recente para o MXChip do [versões](https://aka.ms/iotcentral-docs-MXChip-releases) página no GitHub.
1. Ligue o dispositivo de DevKit ao seu computador de desenvolvimento com um cabo USB. No Windows, uma janela do Explorador de ficheiros é aberto numa unidade mapeada para o armazenamento no dispositivo DevKit. Por exemplo, poderia ser chamada a unidade **AZ3166 (d)**.
1. Arrastar o **iotCentral.bin** ficheiro para a janela de unidade. Quando a cópia estiver concluída, o dispositivo é reiniciado com o novo firmware.

1. Quando o dispositivo DevKit é reiniciado, apresenta o ecrã seguinte:

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > Se a tela exibe qualquer outra coisa, reponha o dispositivo e prima a **uma** e **B** botões no dispositivo, ao mesmo tempo para reiniciar o dispositivo.

1. O dispositivo está agora no modo de ponto (AP) de acesso. Pode ligar a este ponto de acesso Wi-Fi do seu computador ou dispositivo móvel.

1. No seu computador, telemóvel ou tablet ligar para o nome da rede Wi-Fi mostrado no ecrã do dispositivo. Quando se liga a esta rede, não tem acesso à internet. Este estado é esperado e apenas está ligado a esta rede durante um curto período de tempo enquanto configura o dispositivo.

1. Abra o browser e navegue para [ http://192.168.0.1/start ](http://192.168.0.1/start). A seguinte página web apresenta:

    ![Página de configuração do dispositivo](media/howto-connect-devkit/configpage.png)

    Na página da web: 
    - Adicione o nome da sua rede Wi-Fi 
    - a palavra-passe de rede do Wi-Fi
    - CÓDIGO de PIN mostrado no dispositivo de LCD 
    - os detalhes da ligação **chave de Id de âmbito, o Id de dispositivo e o principal** do seu dispositivo (deve já guardar seguindo os passos)      
    - Selecione todas as medidas a telemetria disponível! 

1. Depois de escolher **configurar dispositivo**, vê esta página:

    ![Dispositivo configurado](media/howto-connect-devkit/deviceconfigured.png)

1. Prima a **repor** botão no seu dispositivo.


## <a name="view-the-telemetry"></a>Ver a telemetria

Quando o dispositivo DevKit é reiniciado, mostra o ecrã do dispositivo:

* O número de mensagens de telemetria enviada.
* O número de falhas.
* O número de propriedades pretendidas recebida e o número de propriedades comunicadas enviadas.

> [!NOTE]
> Se o dispositivo é apresentado para o loop durante a verificação do connect, se o dispositivo estiver *bloqueado* no Centro de IoT, e *desbloqueio* o dispositivo, de modo que consegue estabelecer ligação à aplicação.

Abane o incremento de dispositivo o número de propriedades comunicadas enviadas. O dispositivo envia um número aleatório como o **morrem número** propriedade do dispositivo.

Pode ver os valores de propriedades comunicadas e medidas de telemetria e configurar as definições no Azure IoT Central:

1. Uso **Device Explorer** para navegar para o **medidas** página para o dispositivo MXChip real que adicionou:

    ![Navegue para o dispositivo real](media/howto-connect-devkit/realdevicenew.png)

1. Sobre o **medidas** página, pode ver a telemetria provenientes do dispositivo MXChip:

    ![Ver telemetria do dispositivo real](media/howto-connect-devkit/devicetelemetrynew.png)

1. Sobre o **propriedades** página, pode ver o último número de die e a localização do dispositivo comunicado pelo dispositivo:

    ![Ver as propriedades do dispositivo](media/howto-connect-devkit/devicepropertynew.png)

1. Sobre o **definições** página, pode atualizar as definições no dispositivo MXChip:

    ![Ver definições de dispositivo](media/howto-connect-devkit/devicesettingsnew.png)

1. Sobre o **Dashboard** página, pode ver a localização do mapa

    ![Ver o dashboard do dispositivo](media/howto-connect-devkit/devicedashboardnew.png)


## <a name="download-the-source-code"></a>Baixe o código-fonte

Se quiser explorar e modificar o código de dispositivo, pode transferi-lo a partir do GitHub. Se pretender modificar o código, deve seguir estas instruções para [preparar o ambiente de desenvolvimento](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment) para o seu sistema de operativo ambiente de trabalho.

Para transferir o código-fonte, execute o seguinte comando no seu computador desktop:

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

O comando anterior transfere o código-fonte para uma pasta chamada `iot-central-firmware`. 

> [!NOTE]
> Se **git** não está instalado no seu ambiente de desenvolvimento, pode baixá-lo de [ https://git-scm.com/download ](https://git-scm.com/download).

## <a name="review-the-code"></a>Rever o código

Utilizar o Visual Studio Code, que foi instalado quando preparou a seu ambiente de desenvolvimento, para abrir o `AZ3166` pasta na `iot-central-firmware` pasta: 

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

Para ver como a telemetria é enviada para a aplicação Azure IoT Central, abra a **main_telemetry.cpp** ficheiro na pasta de origem.

A função `buildTelemetryPayload` cria o payload JSON de telemetria com dados de sensores no dispositivo.

A função `sendTelemetryPayload` chamadas `sendTelemetry` no **iotHubClient.cpp** para enviar o payload JSON para o IoT Hub utilizados pela aplicação Azure IoT Central.

Para ver a forma como os valores de propriedade são comunicados para a aplicação Azure IoT Central, abra a **main_telemetry.cpp** ficheiro na pasta de origem.

A função `telemetryLoop` envia os **doubleTap** comunicado propriedade quando o acelerômetro Deteta um duplo toque. Ele usa o `sendReportedProperty` funcionar a **iotHubClient.cpp** ficheiro de origem.

O código na **iotHubClient.cpp** ficheiro de origem utilizar as funções da [ Microsoft Azure IoT SDKs e bibliotecas para C](https://github.com/Azure/azure-iot-sdk-c) para interagir com o IoT Hub.

Para obter informações sobre como modificar, criar e carregar o código de exemplo para o seu dispositivo, consulte a **readme.md** de ficheiros a `AZ3166` pasta.

## <a name="mxchip-device-template-details"></a>Detalhes do modelo de dispositivo MXChip

Uma aplicação criada a partir do modelo de aplicativo de exemplo Devkits inclui um modelo de dispositivo MXChip com as seguintes características:

### <a name="measurements"></a>Medições

#### <a name="telemetry"></a>Telemetria 

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


#### <a name="states"></a>Estados 
| Name          | Nome a apresentar   | NORMAL | CUIDADO | PERIGO | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | Estado do dispositivo   | Verde  | Orange  | Vermelho    | 

#### <a name="events"></a>Eventos 
| Name             | Nome a apresentar      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | B de botão premido  | 

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
| Propriedade do dispositivo | Localização do dispositivo   | localização  | localização    |
| Texto            | Fabricados no     | manufacturedIn   | N/A       |



## <a name="next-steps"></a>Passos Seguintes

Agora que sabe como ligar um dispositivo de DevKit à sua aplicação do Azure IoT Central, aqui estão os passos sugeridos seguintes:

* [Preparar e ligar um Raspberry Pi](howto-connect-raspberry-pi-python.md)

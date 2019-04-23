---
title: Ligar um dispositivo de DevKit à sua aplicação do Azure IoT Central | Documentos da Microsoft
description: Como desenvolvedor de dispositivo, saiba como ligar um dispositivo de MXChip IoT DevKit a sua aplicação do Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 82222dd927f46761941a6a750d96222cc626e71b
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60006187"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Ligar um dispositivo de MXChip IoT DevKit a sua aplicação do Azure IoT Central

Este artigo descreve como, como um desenvolvedor de dispositivo, para ligar um dispositivo de MXChip IoT DevKit (DevKit) à sua aplicação do Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Antes de começar

Para concluir os passos neste artigo, tem os seguintes recursos:

1. Uma aplicação do Azure IoT Central, criada a partir da **Devkits exemplo** o modelo de aplicativo. Para obter mais informações, veja [criar um início rápido da aplicação](quick-deploy-iot-central.md).
1. Um dispositivo de DevKit. Para comprar um dispositivo de DevKit, visite [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/).

## <a name="sample-devkits-application"></a>Exemplo de aplicação Devkits

Um aplicativo criado a partir da **Devkits de exemplo** inclui o modelo de aplicativo um **MXChip** modelo de dispositivo que define as seguintes características de dispositivo:

- Medições de telemetria para **humidade**, **temperatura**, **pressão**, **Magnetômetro** (medido ao longo de X, Y, eixo Z), **Acelerômetro** (medido ao longo de X, Y, eixo Z), e **Gyroscope** (medido ao longo de X, Y, eixo Z).
- Estado de medição para **estado do dispositivo**.
- Medida de eventos para **premido do botão B**.
- Definições para **tensão**, **atual**, **ventoinha velocidade**e um **IR** Ativar/desativar.
- Propriedades do dispositivo **morrem número** e **localização do dispositivo**, que é uma propriedade de localização.
- Propriedade da cloud **fabricados no**.
- Comandos **eco** e **contagem regressiva**. Quando um dispositivo real recebe uma **eco** comando, mostra o valor enviado no ecrã do dispositivo. Quando um dispositivo real recebe uma **contagem regressiva** comando, os ciclos de LED através de um padrão, e o dispositivo envia os valores de contagem regressiva para o IoT Central.

Para obter detalhes completos sobre a configuração, consulte [detalhes do modelo de dispositivo MXChip](#mxchip-device-template-details)

## <a name="add-a-real-device"></a>Adicionar um dispositivo real

### <a name="get-your-device-connection-details"></a>Obter o seu dispositivo detalhes de ligação

Na aplicação do Azure IoT Central, adicionar um dispositivo real a partir da **MXChip** modelo do dispositivo e tome nota dos detalhes da ligação de dispositivo: **Definir âmbito de ID, a ID do dispositivo e a chave primária**:

1. Adicionar uma **dispositivo real** a partir do Device Explorer, selecione **+ novo > Real** para adicionar um dispositivo real.

    * Introduza uma minúsculas **ID do dispositivo**, ou utilize sugeridos **ID do dispositivo**.
    * Introduza um **nome do dispositivo**, ou utilize o nome sugerido

    ![Adicionar Dispositivo](media/howto-connect-devkit/add-device.png)

1. Para obter detalhes de ligação, de dispositivo **ID de âmbito**, **ID do dispositivo**, e **chave primária**, selecione **Connect** na página do dispositivo.

    ![Detalhes da ligação](media/howto-connect-devkit/device-connect.png)

1. Tome nota dos detalhes da ligação. Temporariamente estiver desligado da internet quando preparar o seu dispositivo DevKit no próximo passo.

### <a name="prepare-the-devkit-device"></a>Preparar o dispositivo DevKit

Se usou anteriormente o dispositivo e quiser reconfigurá-lo para utilizar uma rede diferente de Wi-Fi, a cadeia de ligação ou a medição de telemetria, prima a **uma** e **B** botões ao mesmo tempo. Se não funcionar, prima **repor** botão e tente novamente.

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

    Na página da web, introduza:
    - O nome da sua rede Wi-Fi
    - a palavra-passe de rede do Wi-Fi
    - O código PIN mostrado no ecrã do dispositivo
    - Os detalhes da ligação **ID de âmbito**, **ID do dispositivo**, e **chave primária** do seu dispositivo (deve já guardar seguindo os passos)
    - Selecione todas as medidas a telemetria disponível

1. Depois de escolher **configurar dispositivo**, vê esta página:

    ![Dispositivo configurado](media/howto-connect-devkit/deviceconfigured.png)

1. Prima a **repor** botão no seu dispositivo.

## <a name="view-the-telemetry"></a>Ver a telemetria

Quando o dispositivo DevKit é reiniciado, mostra o ecrã do dispositivo:

* O número de mensagens de telemetria enviada.
* O número de falhas.
* O número de propriedades pretendidas recebida e o número de propriedades comunicadas enviadas.

> [!NOTE]
> Se o dispositivo é exibido fazer o loop quando tentar ligar, verifique se o dispositivo está **bloqueado** no Centro de IoT, e **desbloqueio** o dispositivo, de modo que consegue estabelecer ligação à aplicação.

Abane o dispositivo para enviar uma propriedade comunicada. O dispositivo envia um número aleatório como o **morrem número** propriedade do dispositivo.

Pode ver os valores de propriedades comunicadas e medidas de telemetria e configurar as definições no Azure IoT Central:

1. Uso **Device Explorer** para navegar para o **medidas** página para o dispositivo MXChip real que adicionou:

    ![Navegue para o dispositivo real](media/howto-connect-devkit/realdevicenew.png)

1. Sobre o **medidas** página, pode ver a telemetria provenientes do dispositivo MXChip:

    ![Ver telemetria do dispositivo real](media/howto-connect-devkit/devicetelemetrynew.png)

1. Sobre o **propriedades** página, pode ver o último número de die e a localização do dispositivo comunicado pelo dispositivo:

    ![Ver as propriedades do dispositivo](media/howto-connect-devkit/devicepropertynew.png)

1. Sobre o **definições** página, pode atualizar as definições no dispositivo MXChip:

    ![Ver definições de dispositivo](media/howto-connect-devkit/devicesettingsnew.png)

1. Sobre o **comandos** página, pode chamar os **eco** e **contagem regressiva** comandos:

    ![Comandos de chamada](media/howto-connect-devkit/devicecommands.png)

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

Utilize o Visual Studio Code para abrir o `MXCHIP/mxchip_advanced` pasta na `iot-central-firmware` pasta:

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

Para ver como a telemetria é enviada para a aplicação Azure IoT Central, abra a **telemetry.cpp** de ficheiros a `src` pasta:

- A função `TelemetryController::buildTelemetryPayload` cria o payload JSON de telemetria com dados de sensores no dispositivo.

- A função `TelemetryController::sendTelemetryPayload` chamadas `sendTelemetry` no **AzureIOTClient.cpp** para enviar o payload JSON para o IoT Hub utilizados pela aplicação Azure IoT Central.

Para ver a forma como os valores de propriedade são comunicados para a aplicação Azure IoT Central, abra a **telemetry.cpp** de ficheiros a `src` pasta:

- A função `TelemetryController::loop` envia os **localização** comunicado propriedade aproximadamente a cada 30 segundos. Ele usa o `sendReportedProperty` funcionar a **AzureIOTClient.cpp** ficheiro de origem.

- A função `TelemetryController::loop` envia os **dieNumber** comunicado propriedade quando o acelerômetro dispositivo Deteta um duplo toque. Ele usa o `sendReportedProperty` funcionar a **AzureIOTClient.cpp** ficheiro de origem.

Para ver como o dispositivo responde a comandos chamados a partir da aplicação de IoT Central, abra a **registeredMethodHandlers.cpp** de ficheiros a `src` pasta:

- O **dmEcho** função é o manipulador para o **eco** comando. Mostra os **displayedValue** arquivado no payload no ecrã do dispositivo.

- O **dmCountdown** função é o manipulador para o **contagem regressiva** comando. Ele altera a cor de LED do dispositivo e utiliza uma propriedade comunicada para enviar o valor de contagem regressiva para a aplicação do Centro de IoT. A propriedade comunicada tem o mesmo nome que o comando. A função usa o `sendReportedProperty` funcionar a **AzureIOTClient.cpp** ficheiro de origem.

O código na **AzureIOTClient.cpp** ficheiro de origem utilizar as funções da [Microsoft Azure IoT SDKs e bibliotecas para C](https://github.com/Azure/azure-iot-sdk-c) para interagir com o IoT Hub.

Para obter informações sobre como modificar, criar e carregar o código de exemplo para o seu dispositivo, consulte a **readme.md** de ficheiros a `MXCHIP/mxchip_advanced` pasta.

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
| Text            | Fabricados no     | manufacturedIn   | N/A       |

### <a name="commands"></a>Comandos

| Nome a apresentar | Nome do campo | Tipo de retorno | Nome a apresentar do campo de entrada | Nome do campo de entrada | Tipo de campo de entrada |
| ------------ | ---------- | ----------- | ------------------------ | ---------------- | ---------------- |
| Echo         | echo       | texto        | valor, para apresentar         | displayedValue   | texto             |
| Contagem decrescente    | Contagem decrescente  | número      | Contar de               | countFrom        | número           |

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como ligar um Raspberry Pi a sua aplicação do Azure IoT Central, a próxima etapa sugerida é saber como [configurar um modelo de dispositivo personalizado](howto-set-up-template.md) para o seu próprio dispositivo IoT.

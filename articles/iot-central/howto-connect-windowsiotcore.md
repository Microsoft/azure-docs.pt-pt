---
title: Ligar um dispositivo de Windows IoT Core à sua aplicação do Azure IoT Central | Documentos da Microsoft
description: Como desenvolvedor de dispositivo, saiba como ligar um dispositivo de MXChip IoT DevKit a sua aplicação do Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 73a23ace23d2373e238c6887c4a41c6037d233de
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189009"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Ligar um dispositivo de Windows IoT Core à sua aplicação do Azure IoT Central

Este artigo descreve como, como um desenvolvedor de dispositivo, para ligar um dispositivo de Windows IoT Core à sua aplicação do Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Antes de começar

Para executar os passos descritos neste artigo é necessário o seguinte:

1. Uma aplicação do Azure IoT Central, criada a partir da **Devkits exemplo** o modelo de aplicativo. Para obter mais informações, consulte [criar seu aplicativo de Central de IoT do Azure](howto-create-application.md).
2. Um dispositivo com o sistema operativo do Windows 10 IoT Core. Nestas instruções, utilizamos um Raspberry Pi.


## <a name="sample-devkits-application"></a>**Exemplo Devkits** aplicação

Um aplicativo criado a partir da **Devkits de exemplo** inclui o modelo de aplicativo um **Windows IoT Core** modelo de dispositivo com as seguintes características: 

- Telemetria que contém as medidas para o dispositivo **humidade**, **temperatura** e **pressão**. 
- As definições que mostra **ventoinha velocidade**.
- Propriedades que contém a propriedade do dispositivo **morrem número** e **localização** propriedade da cloud.


Para obter detalhes completos sobre a configuração do modelo de dispositivo, consulte [detalhes do modelo de dispositivo do Windows IoT Core](howto-connect-windowsiotcore.md#windows-iot-core-device-template-details)

## <a name="add-a-real-device"></a>Adicionar um dispositivo real

Na aplicação do Azure IoT Central, adicionar um dispositivo real a partir da **Windows IoT Core** modelo do dispositivo e tome nota da cadeia de ligação do dispositivo. Para obter mais informações, consulte [adicionar um dispositivo real à sua aplicação do Azure IoT Central](tutorial-add-device.md).

### <a name="prepare-the-windows-iot-core-device"></a>Preparar o dispositivo de Windows IoT Core

Para configurar um dispositivo de Windows IoT Core, siga o guia passo a passo em [configurar um dispositivo de Windows IoT Core] (https://github.com/Azure/iot-central-firmware/tree/master/WindowsIoT#setup-a-physical-device).

### <a name="add-a-real-device"></a>Adicionar um dispositivo real

Na aplicação do Azure IoT Central, adicionar um dispositivo real a partir da **Windows IoT Core** modelo do dispositivo e tome nota da cadeia de ligação do dispositivo. Para obter mais informações, consulte [adicionar um dispositivo real à sua aplicação do Azure IoT Central](tutorial-add-device.md).

## <a name="prepare-the-windows-10-iot-core-device"></a>Preparar o dispositivo Windows 10 IoT Core

### <a name="what-youll-need"></a>O que necessita

Para configurar um dispositivo físico do Windows 10 IoT Core, precisará primeiro ter um dispositivo com Windows 10 IoT Core. Saiba como configurar um dispositivo Windows 10 IoT Core [aqui](https://developer.microsoft.com/en-us/windows/iot/getstarted/prototype/setupdevice).

Também terá uma aplicação de cliente que possa comunicar com o Azure IoT Central. Pode criar a sua aplicação personalizada com o SDK do Azure e implementá-la para o seu dispositivo com o Visual Studio ou pode transferir uma [exemplo precompilados](https://developer.microsoft.com/en-us/windows/iot/samples) e simplesmente implementar e executá-la no dispositivo. 

### <a name="deploying-the-sample-client-application"></a>Implementar a aplicação de cliente de exemplo

Para implementar a aplicação de cliente do passo anterior para o seu dispositivo Windows 10 IoT para prepará-lo:

**Certifique-se de que a cadeia de ligação é armazenada no dispositivo para a aplicação de cliente a utilizar**
* Na área de trabalho, guarde a cadeia de ligação num arquivo de texto chamado connection.string.iothub.
* Copie o ficheiro de texto para a pasta de documentos do dispositivo: `[device-IP-address]\C$\Data\Users\DefaultAccount\Documents\connection.string.iothub`

Depois de fazer isso, precisará abrir o [Windows Device Portal](https://docs.microsoft.com/en-us/windows/iot-core/manage-your-device/deviceportal) ao escrever na http://[device-IP-address]:8080 em qualquer navegador.

Desde há e, como apresentado na if abaixo, poderá ser útil para:
1. Expanda o nó de "Aplicações" no lado esquerdo.
2. Clique em "amostras de execução rápida".
3. Clique em "Cliente do Hub IoT do Azure".
4. Clique em "Implementar e executar".

![GIF do cliente do Hub IoT do Azure no Windows Device Portal](./media/howto-connect-windowsiotcore/iothubapp.gif)

Quando for bem-sucedido, o aplicativo iniciará no dispositivo e o seguinte aspeto:

![Captura de ecrã do Hub IoT do Azure a aplicação de cliente](./media/howto-connect-windowsiotcore/IoTHubForegroundClientScreenshot.png)

No Azure IoT Central, pode ver como o código que executa o Raspberry Pi interage com o aplicativo:

* Sobre o **medidas** página para o seu dispositivo real, pode ver a telemetria.
* Sobre o **propriedades** página, pode ver o valor da propriedade de número morrem comunicada.
* Sobre o **definições** página, pode alterar várias definições no Raspberry Pi, por exemplo, a velocidade de tensão e fan.

## <a name="download-the-source-code"></a>Baixe o código-fonte

Se quiser explorar e modificar o código-fonte para o aplicativo cliente, pode transferi-lo a partir do GitHub [aqui](https://github.com/Microsoft/Windows-iotcore-samples/tree/develop/Samples/Azure/IoTHubClients). Se pretender modificar o código, deve seguir estas instruções no ficheiro Leia-me [aqui](https://github.com/Microsoft/Windows-iotcore-samples) para o seu sistema de operativo ambiente de trabalho.

> [!NOTE]
> Se **git** não está instalado no seu ambiente de desenvolvimento, pode baixá-lo de [ https://git-scm.com/download ](https://git-scm.com/download).

## <a name="windows-iot-core-device-template-details"></a>Detalhes do modelo de dispositivo do Windows IoT Core

Um aplicativo criado a partir da **Devkits de exemplo** inclui o modelo de aplicativo um **Windows IoT Core** modelo de dispositivo com as seguintes características:

### <a name="telemetry-measurements"></a>Medições de telemetria

| Nome do campo     | Unidades  | Mínimo | Máximo | Casas decimais |
| -------------- | ------ | ------- | ------- | -------------- |
| humidade       | %      | 0       | 100     | 0              |
| Temp           | ° C     | -40,Year),temperature     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>Definições

Definições de numérico

| Nome a apresentar | Nome do campo | Unidades | Casas decimais | Mínimo | Máximo | Inicial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Ventoinha velocidade    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |


### <a name="properties"></a>Propriedades

| Tipo            | Nome a apresentar | Nome do campo | Tipo de dados |
| --------------- | ------------ | ---------- | --------- |
| Propriedade do dispositivo | Morrem número   | dieNumber  | número    |
| Texto            | Localização     | localização   | N/A       |

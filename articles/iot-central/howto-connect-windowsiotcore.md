---
title: Ligar um dispositivo de Windows IoT Core à sua aplicação do Azure IoT Central | Documentos da Microsoft
description: Como desenvolvedor de dispositivo, saiba como ligar um dispositivo de MXChip IoT DevKit a sua aplicação do Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: e8d4ab46c598580a3a87f4344202f2700926bf5c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65510329"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Ligar um dispositivo de Windows IoT Core à sua aplicação do Azure IoT Central

Este artigo descreve como, como um desenvolvedor de dispositivo, para ligar um dispositivo de Windows IoT Core à sua aplicação do Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Antes de começar

Para executar os passos descritos neste artigo é necessário o seguinte:

- Uma aplicação do Azure IoT Central, criada a partir da **Devkits exemplo** o modelo de aplicativo. Para obter mais informações, veja [criar um início rápido da aplicação](quick-deploy-iot-central.md).

- Um dispositivo com o sistema operativo do Windows 10 IoT Core. Para obter mais informações, consulte [configuração do dispositivo Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup).

- Uma máquina de desenvolvimento com [node. js](https://nodejs.org/) versão 8.0.0 ou posterior instalado. Pode executar `node --version` na linha de comandos para verificar a sua versão. O Node.js está disponível para uma grande variedade de sistemas operativos.

## <a name="the-sample-devkits-application"></a>O aplicativo de exemplo Devkits

Um aplicativo criado a partir da **Devkits de exemplo** inclui o modelo de aplicativo um **Windows IoT Core** modelo de dispositivo com as seguintes características:

- Medições de telemetria para o dispositivo: **Humidade**, **temperatura**, e **pressão**.
- Definição para controlar **ventoinha velocidade**.
- Uma propriedade do dispositivo **morrem número** e uma propriedade de cloud **localização**.

Para obter detalhes completos sobre a configuração do modelo de dispositivo, consulte [detalhes do modelo de dispositivo do Windows IoT Core](#device-template-details).

## <a name="add-a-real-device"></a>Adicionar um dispositivo real

Na aplicação do Azure IoT Central, utilize o **Device Explorer** página para adicionar um dispositivo real a partir do **Windows 10 IoT Core** modelo do dispositivo. Tome nota do dispositivo detalhes de ligação (**ID de âmbito**, **ID do dispositivo**, e **chave primária**). Para obter mais informações, consulte [obter informações da ligação](howto-generate-connection-string.md#get-connection-information).

## <a name="prepare-the-device"></a>Preparar o dispositivo

Para o dispositivo para estabelecer ligação ao IoT Central, ele precisa de uma cadeia de ligação.

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

O código de dispositivo aceder a cadeia de ligação, guarde-o num arquivo chamado **connection.string.iothub** na pasta `C:\Data\Users\DefaultAccount\Documents\` no seu dispositivo Windows 10 IoT Core.

Para copiar o **connection.string.iothub** ficheiro a partir do computador com o ambiente de trabalho para o `C:\Data\Users\DefaultAccount\Documents\` pasta no seu dispositivo, pode utilizar os [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. Utilize o browser para navegar para o de Windows Device Portal no seu dispositivo.
1. Para procurar os ficheiros no seu dispositivo, escolha **aplicações > Explorador de ficheiros**.
1. Navegue para **utilizador Folders\Documents**. Em seguida, carregue o **connection.string.iothub** ficheiro:

    ![Carregar a cadeia de ligação](media/howto-connect-windowsiotcore/device-portal.png)

## <a name="deploy-and-run"></a>Implementar e executar

Para implementar e executar o aplicativo de exemplo no seu dispositivo, pode utilizar o [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. Utilize o browser para navegar para o de Windows Device Portal no seu dispositivo.
1. Para implementar e executar o **cliente do Azure IoT Hub** aplicação, escolha **aplicações > amostras de execução rápida**. Em seguida, escolha **cliente do Azure IoT Hub**.
1. Em seguida, escolha **implementar e executar**.

    ![Implementar e executar](media/howto-connect-windowsiotcore/quick-run.png)

Após alguns minutos, pode ver a telemetria a partir do seu dispositivo na sua aplicação IoT Central.

O [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) inclui ferramentas que pode utilizar para resolver problemas relacionados com o seu dispositivo:

- O **Gestor de aplicações** página permite-lhe controlar as aplicações executadas no seu dispositivo.
- Se não tiver um monitor ligado ao seu dispositivo, pode utilizar o **definições do dispositivo** página para capturar as capturas de ecrã do seu dispositivo. Por exemplo:

    ![Captura de ecrã da aplicação](media/howto-connect-windowsiotcore/iot-hub-foreground-client.png)

## <a name="download-the-source-code"></a>Baixe o código-fonte

Se quiser explorar e modificar o código-fonte para o aplicativo cliente, poderá transferi-lo a partir da [repositório do GitHub do Windows-iotcore-samples](https://github.com/Microsoft/Windows-iotcore-samples/blob/master/Samples/Azure/IoTHubClients).

## <a name="device-template-details"></a>Detalhes do modelo de dispositivo

Um aplicativo criado a partir da **Devkits de exemplo** inclui o modelo de aplicativo um **Windows IoT Core** modelo de dispositivo com as seguintes características:

### <a name="telemetry-measurements"></a>Medições de telemetria

| Nome do campo     | Unidades  | Mínimo | Máximo | Casas decimais |
| -------------- | ------ | ------- | ------- | -------------- |
| humidade       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>Definições

Definições de numérico

| Display name | Nome do campo | Unidades | Casas decimais | Mínimo | Máximo | Inicial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Ventoinha velocidade    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

### <a name="properties"></a>Propriedades

| Tipo            | Display name | Nome do campo | Tipo de dados |
| --------------- | ------------ | ---------- | --------- |
| Propriedade do dispositivo | Morrem número   | dieNumber  | número    |
| Text            | Location     | localização   | N/A       |

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como ligar um dispositivo de Windows IoT Core à sua aplicação do Azure IoT Central, a próxima etapa sugerida é saber como [configurar um modelo de dispositivo personalizado](howto-set-up-template.md) para o seu próprio dispositivo IoT.

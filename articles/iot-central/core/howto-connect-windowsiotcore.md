---
title: Conectar o Windows IoT Core ao seu aplicativo de IoT Central do Azure | Microsoft Docs
description: Como desenvolvedor de dispositivos, saiba como conectar um dispositivo MXChip IoT DevKit ao aplicativo IoT Central do Azure.
author: miriambrus
ms.author: miriamb
ms.date: 08/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 43e99c54249738436f24369ed3525e78ff971a12
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930212"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Conectar um dispositivo Windows IoT Core ao seu aplicativo IoT Central do Azure

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Este artigo descreve como, como desenvolvedor de dispositivos, conectar um dispositivo Windows IoT Core ao seu aplicativo Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Antes de começar

Para executar os passos descritos neste artigo é necessário o seguinte:

- Um aplicativo IoT Central do Azure criado no modelo de aplicativo **devkits de exemplo** . Para obter mais informações, veja [criar um início rápido da aplicação](quick-deploy-iot-central.md).

- Um dispositivo que executa o sistema operacional Windows 10 IoT Core. Para obter mais informações, consulte [configurando seu dispositivo Windows 10 IOT Core](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup).

- Um computador de desenvolvimento com o [node. js](https://nodejs.org/) versão 8.0.0 ou posterior instalado. Você pode executar `node --version` na linha de comando para verificar sua versão. O Node.js está disponível para uma grande variedade de sistemas operativos.

## <a name="the-sample-devkits-application"></a>O aplicativo devkits de exemplo

Um aplicativo criado no modelo de aplicativo **devkits de exemplo** inclui um modelo de dispositivo **Windows IOT Core** com as seguintes características:

- Medições de telemetria para o dispositivo: **umidade**, **temperatura**e **pressão**.
- Configuração para controlar a **velocidade do ventilador**.
- Um número de **matriz** de propriedade de dispositivo e um **local**de propriedade de nuvem.

Para obter detalhes completos sobre a configuração do modelo de dispositivo, consulte [detalhes do modelo de dispositivo do Windows IOT Core](#device-template-details).

## <a name="add-a-real-device"></a>Adicionar um dispositivo real

No aplicativo IoT Central do Azure, use a página **Device Explorer** para adicionar um dispositivo real do modelo de dispositivo do **Windows 10 IOT Core** . Anote os detalhes de conexão do dispositivo (**ID do escopo**, **ID do dispositivo**e **chave primária**).

## <a name="prepare-the-device"></a>Preparar o dispositivo

Para que o dispositivo se conecte ao IoT Central, ele precisa de uma cadeia de conexão:

1. Use o utilitário de linha de comando `dps-keygen` para gerar uma cadeia de conexão:

    Para instalar o [utilitário gerador de chaves](https://github.com/Azure/dps-keygen), execute o seguinte comando:

    ```cmd/sh
    npm i -g dps-keygen
    ```

1. Para gerar uma cadeia de conexão, execute o seguinte comando usando os detalhes de conexão que você anotou anteriormente:

    ```cmd/sh
    dps-keygen -di:<Device ID> -dk:<Primary or Secondary Key> -si:<Scope ID>
    ```

1. Copie a cadeia de conexão da saída `dps-keygen` para usar no código do dispositivo.

Para que o código do dispositivo acesse a cadeia de conexão, salve-o em um arquivo chamado **Connection. String. iothub** na pasta `C:\Data\Users\DefaultAccount\Documents\` em seu dispositivo Windows 10 IOT Core.

Para copiar o arquivo **Connection. String. iothub** de seu computador desktop para a pasta `C:\Data\Users\DefaultAccount\Documents\` em seu dispositivo, você pode usar o [portal do dispositivo Windows](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. Use seu navegador da Web para navegar até o portal do dispositivo Windows em seu dispositivo.
1. Para procurar os arquivos em seu dispositivo, escolha **aplicativos > explorador de arquivos**.
1. Navegue para o **usuário Folders\Documents**. Em seguida, carregue o arquivo **Connection. String. iothub** :

    ![Carregar cadeia de conexão](media/howto-connect-windowsiotcore/device-portal.png)

## <a name="deploy-and-run"></a>Implantar e executar

Para implantar e executar o aplicativo de exemplo em seu dispositivo, você pode usar o [portal de dispositivo do Windows](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. Use seu navegador da Web para navegar até o portal do dispositivo Windows em seu dispositivo.
1. Para implantar e executar o aplicativo **cliente do Hub IOT do Azure** , escolha **aplicativos > amostras de execução rápida**. Em seguida, escolha **cliente do Hub IOT do Azure**.
1. Em seguida, escolha **implantar e executar**.

    ![Implantar e executar](media/howto-connect-windowsiotcore/quick-run.png)

Após alguns minutos, você pode exibir a telemetria do seu dispositivo no seu aplicativo IoT Central.

O [portal de dispositivos do Windows](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) inclui ferramentas que você pode usar para solucionar problemas de seu dispositivo:

- A página **Gerenciador de aplicativos** permite controlar os aplicativos em execução no seu dispositivo.
- Se você não tiver um monitor conectado ao seu dispositivo, poderá usar a página **configurações do dispositivo** para capturar capturas de tela do seu dispositivo. Por exemplo:

    ![Captura de tela do aplicativo](media/howto-connect-windowsiotcore/iot-hub-foreground-client.png)

## <a name="download-the-source-code"></a>Baixar o código-fonte

Se você quiser explorar e modificar o código-fonte do aplicativo cliente, poderá baixá-lo no [repositório GitHub Windows-iotcore-Samples](https://github.com/Microsoft/Windows-iotcore-samples/blob/master/Samples/Azure/IoTHubClients).

## <a name="device-template-details"></a>Detalhes do modelo de dispositivo

Um aplicativo criado no modelo de aplicativo **devkits de exemplo** inclui um modelo de dispositivo **Windows IOT Core** com as seguintes características:

### <a name="telemetry-measurements"></a>Medições de telemetria

| Nome do campo     | Unidades  | Mínimo | Máximo | Casas decimais |
| -------------- | ------ | ------- | ------- | -------------- |
| humidade       | %      | 0       | 100     | 0              |
| Temp           | °     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>Definições

Configurações numéricas

| Nome a apresentar | Nome do campo | Unidades | Casas decimais | Mínimo | Máximo | This |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Velocidade do ventilador    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

### <a name="properties"></a>Propriedades

| Tipo            | Nome a apresentar | Nome do campo | Data type |
| --------------- | ------------ | ---------- | --------- |
| Propriedade do dispositivo | Número do chip   | dieNumber  | número    |
| Texto            | Localização     | localização   | N/D       |

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu como conectar um dispositivo Windows IoT Core ao seu aplicativo IoT Central do Azure, a próxima etapa sugerida é aprender a [configurar um modelo de dispositivo personalizado](howto-set-up-template.md) para seu próprio dispositivo IOT.

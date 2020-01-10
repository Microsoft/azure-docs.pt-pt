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
ms.openlocfilehash: 452d18908406214bb7e1253363a42d8ba8287d96
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454009"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Conectar um dispositivo Windows IoT Core ao seu aplicativo IoT Central do Azure

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Este artigo descreve como, como desenvolvedor de dispositivos, conectar um dispositivo Windows IoT Core ao seu aplicativo Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Antes de começar

Para executar os passos descritos neste artigo é necessário o seguinte:

- Um aplicativo de IoT Central do Azure criado com base no modelo de aplicativo **herdado** . Para obter mais informações, veja [criar um início rápido da aplicação](quick-deploy-iot-central.md).

- Um dispositivo que executa o sistema operacional Windows 10 IoT Core. Para obter mais informações, consulte [configurando seu dispositivo Windows 10 IOT Core](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup).

- Um computador de desenvolvimento com o [node. js](https://nodejs.org/) versão 8.0.0 ou posterior instalado. Você pode executar `node --version` na linha de comando para verificar sua versão. O Node.js está disponível para uma grande variedade de sistemas operativos.

## <a name="add-a-device-template"></a>Adicionar um modelo de dispositivo

No aplicativo IoT Central do Azure, adicione um novo modelo de dispositivo **Windows IOT Core** com as seguintes características:

- Medições de telemetria para o dispositivo: **umidade**, **temperatura**e **pressão**.
- Configuração para controlar a **velocidade do ventilador**.
- Um número de **matriz** de propriedade de dispositivo e um **local**de propriedade de nuvem.

1. Selecione **+ novo** nos modelos de dispositivo ![modelo de dispositivo](media/howto-connect-windowsiotcore/adddevicetemplate.png)
   

2. Selecione **Windows IOT Core** e crie o modelo de dispositivo Windows iot Core ![adicionar modelo de dispositivo](media/howto-connect-windowsiotcore/newdevicetemplate.png)

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
| temp           | °     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>Definições

Configurações numéricas

| Nome a apresentar | Nome do campo | Unidades | Casas decimais | Mínimo | Máximo | Inicial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Velocidade do ventilador    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

### <a name="properties"></a>Propriedades

| Tipo            | Nome a apresentar | Nome do campo | Data type |
| --------------- | ------------ | ---------- | --------- |
| Propriedade do dispositivo | Número do chip   | dieNumber  | número    |
| Texto            | Localização     | localização   | N/A       |

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu como conectar um dispositivo Windows IoT Core ao seu aplicativo IoT Central do Azure, a próxima etapa sugerida é aprender a [configurar um modelo de dispositivo personalizado](howto-set-up-template.md) para seu próprio dispositivo IOT.

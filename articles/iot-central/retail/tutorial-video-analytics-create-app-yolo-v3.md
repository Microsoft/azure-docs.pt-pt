---
title: Tutorial - Criar uma aplicação de deteção de objetos e movimentos no Azure IoT Central (YOLO v3)
description: Este tutorial mostra como criar uma aplicação de análise de vídeo na IoT Central. Cria-se, personaliza-o e liga-se a outros serviços Azure. Este tutorial utiliza o sistema de deteção de objetos EM TEMPO REAL YOLO v3.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 10/06/2020
ms.openlocfilehash: 3994b05f613cbebcf6daa05cf8db3ef429b52407
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428067"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central-yolo-v3"></a>Tutorial: Criar uma aplicação de análise de vídeo - aplicação de deteção de objetos e movimentos no Azure IoT Central (YOLO v3)

Como construtor de soluções, aprenda a criar uma aplicação de análise de vídeo com o modelo de análise de vídeo IoT Central - modelo *de aplicação de deteção de objetos e movimentos,* dispositivos Azure IoT Edge, Azure Media Services e o sistema de deteção de objetos e movimentos em tempo real YOLO v3. A solução utiliza uma loja de retalho para mostrar como atender à necessidade de monitorizar as câmaras de segurança. A solução utiliza a deteção automática de objetos num feed de vídeo para identificar e localizar rapidamente eventos interessantes.

> [!TIP]
> Para utilizar o OpenVINO &trade; em vez de YOLO v3 para deteção de objetos, consulte [Tutorial: Criar uma aplicação de deteção de vídeo - objeto e deteção de movimentos em Azure IoT Central (OpenVINO) &trade; ](tutorial-video-analytics-create-app-openvino.md).

[!INCLUDE [iot-central-video-analytics-part1](../../../includes/iot-central-video-analytics-part1.md)]

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt)
- [deployment.amd64.js](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.amd64.json)
- [LvaEdgeGatewayDcm.js](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.js](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json)

> [!NOTE]
> O repositório GitHub também inclui o código fonte para os módulos **LvaEdgeGatewayModule** e **LvaYolov3** IoT Edge. Para obter mais informações sobre o trabalho com o código fonte, consulte os [Módulos de Gateway Build the LVA](tutorial-video-analytics-build-module.md).

[!INCLUDE [iot-central-video-analytics-part2](../../../includes/iot-central-video-analytics-part2.md)]

## <a name="edit-the-deployment-manifest"></a>Editar o manifesto de implantação

Você implanta um módulo IoT Edge usando um manifesto de implantação. No IoT Central pode importar o manifesto como modelo de dispositivo e, em seguida, deixar a IoT Central gerir a implementação do módulo.

Para preparar o manifesto de implantação:

1. Abra a *deployment.amd64.jsno* ficheiro, que guardou na pasta *de configuração de Iva,* utilizando um editor de texto.

1. Encontre as `LvaEdgeGatewayModule` definições e altere o nome da imagem como mostrado no seguinte corte:

    ```json
    "LvaEdgeGatewayModule": {
        "settings": {
            "image": "mcr.microsoft.com/lva-utilities/lva-edge-iotc-gateway:1.0-amd64",
    ```

1. Adicione o nome da sua conta de Media Services no `env` nó na `LvaEdgeGatewayModule` secção. Fez uma nota deste nome da conta no ficheiro *scratchpad.txt:*

    ```json
    "env": {
        "lvaEdgeModuleId": {
            "value": "lvaEdge"
        },
        "amsAccountName": {
            "value": "<YOUR_AZURE_MEDIA_ACCOUNT_NAME>"
        }
    }
    ```

1. O modelo não expõe estas propriedades no IoT Central, pelo que é necessário adicionar os valores de configuração dos Serviços de Mídia ao manifesto de implantação. Localize o `lvaEdge` módulo e substitua os espaços reservados pelos valores que fez no ficheiro *scratchpad.txt* quando criou a sua conta de Serviços de Comunicação Social.

    O `azureMediaServicesArmId` **ID de recursos** que fez uma nota no ficheiro *scratchpad.txt* quando criou a conta dos Serviços de Comunicação Social.

    Fez uma nota do `aadTenantId` ficheiro , e no ficheiro `aadServicePrincipalAppId`scratchpad.txtquando criou o principal serviço para a sua conta de Media `aadServicePrincipalSecret` Services: *scratchpad.txt*

    ```json
    {
        "lvaEdge":{
        "properties.desired": {
            "applicationDataDirectory": "/var/lib/azuremediaservices",
            "azureMediaServicesArmId": "[Resource ID from scratchpad]",
            "aadTenantId": "[AADTenantID from scratchpad]",
            "aadServicePrincipalAppId": "[AadClientId from scratchpad]",
            "aadServicePrincipalSecret": "[AadSecret from scratchpad]",
            "aadEndpoint": "https://login.microsoftonline.com",
            "aadResourceId": "https://management.core.windows.net/",
            "armEndpoint": "https://management.azure.com/",
            "diagnosticsEventsOutputName": "AmsDiagnostics",
            "operationalMetricsOutputName": "AmsOperational",
            "logCategories": "Application,Event",
            "AllowUnsecuredEndpoints": "true",
            "TelemetryOptOut": false
            }
        }
    }
    ```

1. Guarde as alterações.

Este tutorial configura a sua solução para utilizar o módulo YOLO v3 para deteção de objetos e movimentos. O seguinte corte mostra a configuração do módulo:

```json
"lvaYolov3": {
    "settings": {
        "image": "mcr.microsoft.com/lva-utilities/yolov3-onnx:1.0"
    },
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "version": "1.0"
}
```

[!INCLUDE [iot-central-video-analytics-part3](../../../includes/iot-central-video-analytics-part3.md)]

### <a name="replace-the-manifest"></a>Substitua o manifesto

Na página **LVA Edge Gateway v2,** selecione **+ Substitua o manifesto**.

:::image type="content" source="./media/tutorial-video-analytics-create-app-yolo-v3/replace-manifest.png" alt-text="Substituir Manifesto":::

Navegue na pasta *de configuração Lva* e selecione a *deployment.amd64.jsno* ficheiro manifesto que editou anteriormente. Selecione **Carregar**. Quando a validação estiver concluída, selecione **Substitua.**

[!INCLUDE [iot-central-video-analytics-part4](../../../includes/iot-central-video-analytics-part4.md)]

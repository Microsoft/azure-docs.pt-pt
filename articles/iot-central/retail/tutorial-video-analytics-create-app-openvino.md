---
title: Tutorial - Criar uma aplicação de análise de vídeo - aplicação de deteção de objetos e movimentos no Azure IoT Central (OpenVINO)
description: Este tutorial mostra como criar uma aplicação de análise de vídeo na IoT Central. Cria-se, personaliza-o e liga-se a outros serviços Azure. Este tutorial utiliza o conjunto de ferramentas Intel OpenVINO &trade; para deteção de objetos em tempo real.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 10/06/2020
ms.openlocfilehash: e16342b4b46a189a5a5c4c0554fb2d870c9cdcac
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91877203"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central-openvinotrade"></a>Tutorial: Criar uma aplicação de análise de vídeo - aplicação de deteção de objetos e movimentos no Azure IoT Central &trade; (OpenVINO)

Como construtor de soluções, aprenda a criar uma aplicação de análise de vídeo com a análise de vídeo IoT Central - modelo de aplicação *de deteção de objetos e movimentos,* dispositivos Azure IoT Edge, Azure Media Services e OpenVINO otimizado para deteção de &trade; objetos e movimentos da Intel. A solução utiliza uma loja de retalho para mostrar como atender à necessidade de monitorizar as câmaras de segurança. A solução utiliza a deteção automática de objetos num feed de vídeo para identificar e localizar rapidamente eventos interessantes.

> [!TIP]
> Para utilizar o YOLO v3 em vez de OpenVINO para deteção de &trade; objetos, consulte [Tutorial: Criar uma aplicação de deteção de vídeo - objeto e deteção de movimentos em Azure IoT Central (YOLO v3)](tutorial-video-analytics-create-app-yolo-v3.md).

[!INCLUDE [iot-central-video-analytics-part1](../../../includes/iot-central-video-analytics-part1.md)]

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt)
- [deployment.openvino.amd64.js](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.openvino.amd64.json)
- [LvaEdgeGatewayDcm.js](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.js](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json)

[!INCLUDE [iot-central-video-analytics-part2](../../../includes/iot-central-video-analytics-part2.md)]

## <a name="edit-the-deployment-manifest"></a>Editar o manifesto de implantação

Você implanta um módulo IoT Edge usando um manifesto de implantação. No IoT Central pode importar o manifesto como modelo de dispositivo e, em seguida, deixar a IoT Central gerir a implementação do módulo.

Para preparar o manifesto de implantação:

1. Abra a *deployment.openvino.amd64.jsno* ficheiro, que guardou na pasta *de configuração de Iva,* utilizando um editor de texto.

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

Este tutorial configura a sua solução para utilizar o módulo OpenVINO &trade; para deteção de objetos e movimentos. O seguinte corte mostra a configuração do módulo:

```json
"OpenVINOModelServerEdgeAIExtensionModule": {
    "settings": {
        "image": "marketplace.azurecr.io/intel_corporation/open_vino",
        "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"4000/tcp\":[{\"HostPort\":\"4000\"}]}},\"Cmd\":[\"/ams_wrapper/start_ams.py\",\"--ams_port=4000\",\"--ovms_port=9000\"]}"
    },
    "type": "docker",
    "version": "1.0",
    "status": "running",
    "restartPolicy": "always"
}
```

[!INCLUDE [iot-central-video-analytics-part3](../../../includes/iot-central-video-analytics-part3.md)]

### <a name="replace-the-manifest"></a>Substitua o manifesto

Na página **LVA Edge Gateway,** selecione **+ Substitua o manifesto**.

:::image type="content" source="./media/tutorial-video-analytics-create-app-openvino/replace-manifest.png" alt-text="Substituir Manifesto":::

Navegue na pasta *de configuração Lva* e selecione a *deployment.openvino.amd64.jsno* ficheiro manifesto que editou anteriormente. Selecione **Carregar**. Quando a validação estiver concluída, selecione **Substitua.**

[!INCLUDE [iot-central-video-analytics-part4](../../../includes/iot-central-video-analytics-part4.md)]

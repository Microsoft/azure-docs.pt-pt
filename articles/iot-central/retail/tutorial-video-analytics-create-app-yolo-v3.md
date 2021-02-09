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
ms.openlocfilehash: 70769ec97affcd95d07a1365d6357262072c86ec
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832088"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central-yolo-v3"></a>Tutorial: Criar uma aplicação de análise de vídeo - aplicação de deteção de objetos e movimentos no Azure IoT Central (YOLO v3)

Como construtor de soluções, aprenda a criar uma aplicação de análise de vídeo com o modelo de análise de vídeo IoT Central - modelo *de aplicação de deteção de objetos e movimentos,* dispositivos Azure IoT Edge, Azure Media Services e o sistema de deteção de objetos e movimentos em tempo real YOLO v3. A solução utiliza uma loja de retalho para mostrar como atender à necessidade de monitorizar as câmaras de segurança. A solução utiliza a deteção automática de objetos num feed de vídeo para identificar e localizar rapidamente eventos interessantes.

> [!TIP]
> Para utilizar o OpenVINO &trade; em vez de YOLO v3 para deteção de objetos, consulte [Tutorial: Criar uma aplicação de deteção de vídeo - objeto e deteção de movimentos em Azure IoT Central (OpenVINO) &trade; ](tutorial-video-analytics-create-app-openvino.md).

[!INCLUDE [iot-central-video-analytics-part1](../../../includes/iot-central-video-analytics-part1.md)]

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt) - este ficheiro ajuda-o a gravar as várias opções de configuração de que necessita enquanto trabalha através destes tutoriais.
- [deployment.amd64.js](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.amd64.json)
- [LvaEdgeGatewayDcm.js](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.jsligado](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json) - só precisa de descarregar este ficheiro se pretender utilizar o dispositivo Intel NUC no segundo tutorial.

> [!NOTE]
> O repositório GitHub também inclui o código fonte para os módulos **LvaEdgeGatewayModule** e **LvaYolov3** IoT Edge. Para obter mais informações sobre o trabalho com o código fonte, consulte os [Módulos de Gateway Build the LVA](tutorial-video-analytics-build-module.md).

[!INCLUDE [iot-central-video-analytics-part2](../../../includes/iot-central-video-analytics-part2.md)]

## <a name="edit-the-deployment-manifest"></a>Editar o manifesto de implantação

Você implanta um módulo IoT Edge usando um manifesto de implantação. No IoT Central pode importar o manifesto como modelo de dispositivo e, em seguida, deixar a IoT Central gerir a implementação do módulo.

Para preparar o manifesto de implantação:

1. Abra a *deployment.amd64.jsno* ficheiro, que guardou na pasta *de configuração de Iva,* utilizando um editor de texto.

1. Encontre as `LvaEdgeGatewayModule` definições e certifique-se de que o nome da imagem está como mostrado no seguinte corte:

    ```json
    "LvaEdgeGatewayModule": {
        "settings": {
            "image": "mcr.microsoft.com/lva-utilities/lva-edge-iotc-gateway:1.0-amd64",
    ```

1. Adicione o nome da sua conta de Media Services no `env` nó na `LvaEdgeGatewayModule` secção. Fez uma nota do nome da conta dos Serviços de Comunicação social no ficheiro *scratchpad.txt:*

    ```json
    "env": {
        "lvaEdgeModuleId": {
            "value": "lvaEdge"
        },
        "amsAccountName": {
            "value": "<YOUR_AZURE_MEDIA_SERVICES_ACCOUNT_NAME>"
        }
    }
    ```

1. O modelo não expõe estas propriedades no IoT Central, pelo que é necessário adicionar os valores de configuração dos Serviços de Mídia ao manifesto de implantação. Localize o `lvaEdge` módulo e substitua os espaços reservados pelos valores que fez no ficheiro *scratchpad.txt* quando criou a sua conta de Serviços de Comunicação Social.

    O `azureMediaServicesArmId` **ID de recursos** que fez uma nota no ficheiro *scratchpad.txt* quando criou a conta dos Serviços de Comunicação Social.

    A tabela a seguir mostra os valores da **API de Serviços de Ligação a Meios de Comunicação Social (JSON)** no ficheiro *scratchpad.txt* que deve utilizar no manifesto de implantação:

    | Manifesto de implementação       | Raspadinha  |
    | ------------------------- | ----------- |
    | aadTenantId               | AadTenantId |
    | aadServicePrincipalAppId  | AadClientId |
    | aadServicePrincipalSecret | AadSecret   |

    > [!CAUTION]
    > Utilize a tabela anterior para se certificar de que adiciona os valores corretos no manifesto de implantação, caso contrário o dispositivo não funcionará.

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

## <a name="clean-up-resources"></a>Limpar os recursos

Se terminou a aplicação, pode remover todos os recursos que criou da seguinte forma:

1. Na aplicação IoT Central, navegue para a **página de aplicação** da sua candidatura na secção **Administração.** Em seguida, selecione **Eliminar**.
1. No portal Azure, elimine o grupo de recursos **Lva-rg.**
1. Na sua máquina local, pare o recipiente **do espectador** estivador.

## <a name="next-steps"></a>Passos seguintes

Criou agora uma aplicação IoT Central utilizando o modelo de aplicação **de deteção de objetos e movimentos,** criou um modelo de dispositivo para o dispositivo gateway e adicionou um dispositivo de gateway à aplicação.

Se quiser experimentar a análise de vídeo - aplicação de deteção de objetos e movimentos utilizando módulos IoT Edge que executam um VM em nuvem com streams de vídeo simulados:

> [!div class="nextstepaction"]
> [Crie uma instância IoT Edge para análise de vídeo (Linux VM)](tutorial-video-analytics-iot-edge-vm.md)

Se quiser experimentar a análise de vídeo - aplicação de deteção de objetos e movimentos utilizando módulos IoT Edge que executam um dispositivo real com uma câmara **ONVIF** real:

> [!div class="nextstepaction"]
> [Crie uma instância IoT Edge para análise de vídeo (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md)
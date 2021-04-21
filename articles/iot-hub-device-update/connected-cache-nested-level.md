---
title: Microsoft Connected Cache dois nível aninhado Azure IoT Edge Gateway com | de procuração não autenticado de saída Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Microsoft Connected Cache dois nível aninhado Azure IoT Edge Gateway com tutorial de procuração não autenticado de saída
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 623ce808423f76ae1be079e0424fe3ddf27d1d58
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811891"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-two-level-nested-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Amostra do cenário de visualização da cache conectada da Microsoft: Dois níveis aninhado Azure IoT Edge Gateway com procuração não autenticada de saída

O diagrama abaixo descreve o cenário em que um gateway Azure IoT Edge tem acesso direto aos recursos da CDN e está a agir como o progenitor de outra porta de entrada Azure IoT Edge. O porta de entrada IoT Edge infantil está a agir como o progenitor de um dispositivo de folha Azure IoT, como um Raspberry Pi. Tanto a criança Azure IoT Edge como o dispositivo Azure IoT estão isolados na Internet. O exemplo abaixo demonstra a configuração para dois níveis de gateways Azure IoT Edge, mas não há limite para a profundidade dos anfitriões a montante que o Microsoft Connected Cache irá suportar. Não existe diferença no recipiente cache ligado à Microsoft para criar opções a partir dos exemplos anteriores.

Consulte a documentação Ligue os [dispositivos IoT Edge a jusante - Azure IoT Edge](../iot-edge/how-to-connect-downstream-iot-edge-device.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11) para obter mais detalhes sobre a configuração de implementações em camadas de gateways Azure IoT Edge. Além disso, note que ao implementar Azure IoT Edge, Microsoft Connected Cache e módulos personalizados, todos os módulos devem residir no mesmo registo de contentores.

>[!Note]
>Ao implementar O Azure IoT Edge, Microsoft Connected Cache e módulos personalizados, todos os módulos devem residir no mesmo registo de contentores.

  :::image type="content" source="media/connected-cache-overview/nested-level-proxy.png" alt-text="Cache conectado da Microsoft Aninhado" lightbox="media/connected-cache-overview/nested-level-proxy.png":::

## <a name="parent-gateway-configuration"></a>Configuração do gateway dos pais
1. Adicione o módulo cache ligado à sua implementação do dispositivo de gateway Azure IoT Edge no Azure IoT Hub (consulte [Suporte para Dispositivos Desligados](connected-cache-disconnected-device-update.md) para obter detalhes sobre como obter o módulo).
2. Adicione as variáveis ambientais para a implantação. Abaixo está um exemplo das variáveis ambientais.

    **Variáveis ambientais**

    | Name                          | Valor                                                                 |
    | ----------------------------- | ----------------------------------------------------------------------| 
    | CACHE_NODE_ID                 | Ver descrições [variáveis do ambiente](connected-cache-configure.md) |
    | CUSTOMER_ID                   | Ver descrições [variáveis do ambiente](connected-cache-configure.md) |
    | CUSTOMER_KEY                  | Ver descrições [variáveis do ambiente](connected-cache-configure.md) |
    | STORAGE_1_SIZE_GB             | 10                                                                    |
    | CACHEABLE_CUSTOM_1_HOST       | Packagerepo.com:80                                                    |
    | CACHEABLE_CUSTOM_1_CANONICAL  | Packagerepo.com                                                       |
    | IS_SUMMARY_ACCESS_UNRESTRICTED| true                                                                  |

3. Adicione o recipiente criar opções para a implantação. Não há diferença no que se passa no contentor MCC, a partir do exemplo anterior. Abaixo está um exemplo do recipiente criar opções.

### <a name="container-create-options"></a>Opções de criação de contentores

```json
{
    "HostConfig": {
        "Binds": [
            "/MicrosoftConnectedCache1/:/nginx/cache1/"
        ],
        "PortBindings": {
            "8081/tcp": [
                {
                    "HostPort": "80"
                }
            ],
            "5000/tcp": [
                {
                    "HostPort": "5100"
                }
            ]
        }
    }
}
```

## <a name="child-gateway-configuration"></a>Configuração do gateway infantil

>[!Note]
>Se tiver recipientes replicados utilizados na sua configuração no seu próprio registo privado, terá de haver uma modificação nas definições config.toml e nas definições de tempo de funcionamento na implementação do módulo. Para obter mais informações, consulte os [dispositivos IoT Edge a jusante - Azure IoT Edge](../iot-edge/how-to-connect-downstream-iot-edge-device.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11#deploy-modules-to-lower-layer-devices) para obter mais detalhes.


1. Modificar o caminho de imagem para o agente Edge, como demonstrado no exemplo abaixo:

    ```markdown
    [agent]
    name = "edgeAgent"
    type = "docker"
    env = {}
    [agent.config]
    image = "<parent_device_fqdn_or_ip>:8000/iotedge/azureiotedge-agent:1.2.0-rc2"
    auth = {}
    ```
2. Modificar as definições de tempo de execução do edge hub e do agente edge na implementação Azure IoT Edge, como demonstrado neste exemplo:
    
    * Sob o Edge Hub, no campo de imagem, insira ```$upstream:8000/iotedge/azureiotedge-hub:1.2.0-rc2```
    * Sob o Agente Edge, no campo de imagem, insira ```$upstream:8000/iotedge/azureiotedge-agent:1.2.0-rc2```

3. Adicione o módulo cache ligado à sua implementação do dispositivo de gateway Azure IoT Edge no Azure IoT Hub.

   * Escolha um nome para o seu módulo: ```ConnectedCache```
   * Modificar o URI de imagem: ```$upstream:8000/mcc/linux/iot/mcc-ubuntu-iot-amd64:latest```

4. Adicione o mesmo conjunto de variáveis ambientais e o recipiente crie opções usadas na colocação dos pais.
>[!Note]
>O CACHE_NODE_ID deve ser único.  Os valores CUSTOMER_ID e CUSTOMER_KEY serão idênticos aos dos pais. (ver [Configurar cache conectado da Microsoft](connected-cache-configure.md)

Para uma validação do funcionamento correto da Cache Microsoft, execute o seguinte comando no terminal do dispositivo IoT Edge que hospeda o módulo ou qualquer dispositivo na rede. \<Azure IoT Edge Gateway IP\>Substitua-o pelo endereço IP ou nome de anfitrião do seu gateway IoT Edge. (ver detalhes variáveis ambientais para informações sobre a visibilidade deste relatório).

```bash
    wget http://<CHILD Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```
---
title: Microsoft Connected Cache dois nível aninhado Azure IoT Edge Gateway com | de procuração não autenticado de saída Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Microsoft Connected Cache dois nível aninhado Azure IoT Edge Gateway com tutorial de procuração não autenticado de saída
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 0128d0de4f078b62bc9571c8758d80cb26585354
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102615385"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-two-level-nested-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Amostra do cenário de visualização da cache conectada da Microsoft: Dois níveis aninhado Azure IoT Edge Gateway com procuração não autenticada de saída

Dado o diagrama abaixo, neste cenário existe um gateway Azure IoT Edge e um dispositivo Azure IoT Edge a jusante, um gateway Azure IoT Edge baseado em outro gateway Azure IoT Edge e um servidor proxy no IT DMZ. Abaixo está um exemplo das variáveis ambientais da Cache conectadas da Microsoft que seriam definidas no portal Azure UX para ambos os módulos MCC implantados nos gateways Azure IoT Edge. O exemplo mostrado demonstra a configuração para dois níveis de gateways Azure IoT Edge, mas não há limite para a profundidade dos anfitriões a montante que o Microsoft Connected Cache irá suportar. Não há diferença no contentor MCC criar opções a partir dos exemplos anteriores.

Consulte a documentação Ligue os [dispositivos IoT Edge a jusante - Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-connect-downstream-iot-edge-device?view=iotedge-2020-11&tabs=azure-portal&preserve-view=true) para obter mais detalhes sobre a configuração de implementações em camadas de gateways Azure IoT Edge. Além disso, note que ao implementar Azure IoT Edge, Microsoft Connected Cache e módulos personalizados, todos os módulos devem residir no mesmo registo de contentores.

O diagrama abaixo descreve o cenário em que um gateway Azure IoT Edge como acesso direto aos recursos da CDN está a agir como o progenitor de outra porta de entrada Azure IoT Edge que está a agir como o progenitor de um dispositivo de folha Azure IoT, como um Raspberry Pi. Apenas o pai do gateway Azure IoT Edge tem conectividade de internet com os recursos da CDN e tanto o dispositivo Azure IoT Edge como o dispositivo Azure IoT estão isolados na Internet. 

  :::image type="content" source="media/connected-cache-overview/nested-level-proxy.png" alt-text="Cache conectado da Microsoft Aninhado" lightbox="media/connected-cache-overview/nested-level-proxy.png":::

## <a name="parent-gateway-configuration"></a>Configuração do gateway dos pais

1. Adicione o módulo cache ligado à sua implementação do dispositivo de gateway Azure IoT Edge no Azure IoT Hub.
2. Adicione as variáveis ambientais para a implantação. Abaixo está um exemplo das variáveis ambientais.

    **Variáveis ambientais**

    | Name                 | Valor                                       |
    | ----------------------------- | --------------------------------------------| 
    | CACHE_NODE_ID                 | Consulte a descrição variável do ambiente acima. |
    | CUSTOMER_ID                   | Consulte a descrição variável do ambiente acima. |
    | CUSTOMER_KEY                  | Consulte a descrição variável do ambiente acima. |
    | STORAGE_ *N* _SIZE_GB           | N = 5                                       |
    | CACHEABLE_CUSTOM_1_HOST       | Packagerepo.com:80                          |
    | CACHEABLE_CUSTOM_1_CANONICAL  | Packagerepo.com                             |
    | IS_SUMMARY_ACCESS_UNRESTRICTED| true                                        |
    | UPSTREAM_PROXY                | Proxy servidor IP ou FQDN                     |

3. Adicione o recipiente criar opções para a implantação. Não há diferença no que se passa no contentor MCC, a partir do exemplo anterior. Abaixo está um exemplo do recipiente criar opções.

### <a name="container-create-options"></a>Opções de criação de contentores

```markdown
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
```

## <a name="child-gateway-configuration"></a>Configuração do gateway infantil

>[!Note]
>Se tiver recipientes replicados utilizados na sua configuração no seu próprio registo privado, terá de haver uma modificação nas definições config.toml e nas definições de tempo de funcionamento na implementação do módulo. Para mais informações, consulte [tutorial - Crie uma hierarquia de dispositivos IoT Edge - Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-nested-iot-edge?view=iotedge-2020-11&tabs=azure-portal&preserve-view=true#deploy-modules-to-the-lower-layer-device) para mais detalhes.

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

4. Adicione as mesmas variáveis ambientais e o recipiente crie opções usadas na colocação dos pais.

Para uma validação do funcionamento correto da Cache Microsoft, execute o seguinte comando no terminal do dispositivo IoT Edge que hospeda o módulo ou qualquer dispositivo na rede.

```bash
    wget "http://<CHILD Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```
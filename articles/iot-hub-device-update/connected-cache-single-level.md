---
title: Amostras de cenário de visualização da Cache conectada da Microsoft | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Microsoft Connected Cache preview scenario de implementação de amostras tutoriais
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: c116bbf5ea9f5fc6e58962e02c93c630fc747d9e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811729"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-samples"></a>Amostras de cenário de visualização da Cache conectadas com o Microsoft Connected Cache

## <a name="single-level-azure-iot-edge-gateway-no-proxy"></a>Gateway de borda Azure IoT de nível único sem procuração

O diagrama abaixo descreve o cenário em que um gateway Azure IoT Edge que tem acesso direto aos recursos da CDN e existe um dispositivo de folha Azure IoT, como um PI de framboesa que é um dispositivo infantil isolado na Internet do gateway Azure IoT Edge. 

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Atualização de dispositivo desligado da cache conectada da Microsoft" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Adicione o módulo cache ligado à sua implementação do dispositivo de gateway Azure IoT Edge no Azure IoT Hub (consulte [Suporte para Dispositivos Desligados](connected-cache-disconnected-device-update.md) para obter detalhes sobre como obter o módulo).
2. Adicione as variáveis ambientais para a implantação. Abaixo está um exemplo das variáveis ambientais.

    **Variáveis ambientais**
    
    | Name                          | Valor                                                                 |
    | ----------------------------- | ----------------------------------------------------------------------| 
    | CACHE_NODE_ID                 | Ver descrições [variáveis do ambiente](connected-cache-configure.md) |
    | CUSTOMER_ID                   | Ver descrições [variáveis do ambiente](connected-cache-configure.md) |
    | CUSTOMER_KEY                  | Ver descrições [variáveis do ambiente](connected-cache-configure.md) |
    | STORAGE_1_SIZE_GB             | 10                                                                    |

3. Adicione o recipiente criar opções para a implantação. Abaixo está um exemplo do recipiente criar opções.

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

Para uma validação do funcionamento correto da Cache Microsoft, execute o seguinte comando no terminal do dispositivo IoT Edge que hospeda o módulo ou qualquer dispositivo na rede. \<Azure IoT Edge Gateway IP\>Substitua-o pelo endereço IP ou nome de anfitrião do seu gateway IoT Edge. (ver detalhes variáveis ambientais para informações sobre a visibilidade deste relatório).

```bash
    wget http://<IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```

## <a name="single-level-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Gateway de borda Azure IoT de nível único com procuração não autenticada de saída

Neste cenário existe um Azure IoT Edge Gateway que tem acesso aos recursos da CDN através de um representante não autenticado de saída. O Microsoft Connected Cache está a ser configurado para cache de conteúdo de um repositório personalizado e o relatório de resumo foi tornado visível para qualquer pessoa da rede. Abaixo está um exemplo das variáveis ambientais mcC que seriam definidas.

  :::image type="content" source="media/connected-cache-overview/single-level-proxy.png" alt-text="Microsoft Connected Cache Single Level Proxy" lightbox="media/connected-cache-overview/single-level-proxy.png":::

1. Adicione o módulo cache ligado à sua implementação do dispositivo de gateway Azure IoT Edge no Azure IoT Hub.
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
    | UPSTREAM_PROXY                | O seu servidor proxy IP ou FQDN                                          |

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

Para uma validação do funcionamento correto da Cache Microsoft, execute o seguinte comando no terminal do dispositivo Azure IoT Edge que hospeda o módulo ou qualquer dispositivo na rede. \<Azure IoT Edge Gateway IP\>Substitua-o pelo endereço IP ou nome de anfitrião do seu gateway IoT Edge. (ver detalhes variáveis ambientais para informações sobre a visibilidade deste relatório).

```bash
    wget http://<Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com 
```

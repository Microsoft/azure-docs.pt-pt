---
title: Amostras de cenário de visualização da Cache conectada da Microsoft | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Microsoft Connected Cache preview scenario de implementação de amostras tutoriais
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: ae07926d7d8c768170e945e916367bee41999571
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101665030"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-samples"></a>Amostras de cenário de visualização da Cache conectadas com o Microsoft Connected Cache

## <a name="single-level-azure-iot-edge-gateway-no-proxy"></a>Gateway de borda Azure IoT de nível único sem procuração

O diagrama abaixo descreve o cenário em que um gateway Azure IoT Edge que tem acesso direto aos recursos da CDN e existe um dispositivo de folha Azure IoT, como um PI de framboesa que é um dispositivo infantil isolado na Internet do gateway Azure IoT Edge. 

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Atualização de dispositivo desligado da cache conectada da Microsoft" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Adicione o módulo cache ligado à sua implementação do dispositivo de gateway Azure IoT Edge no Azure IoT Hub (consulte `MCC concepts` para obter detalhes sobre como obter o módulo).
2. Adicione as variáveis ambientais para a implantação. Abaixo está um exemplo das variáveis ambientais.

    **Variáveis ambientais**
    
    | Name                 | Valor                                       |
    | ----------------------------- | --------------------------------------------| 
    | CACHE_NODE_ID                 | Consulte a descrição variável do ambiente acima. |
    | CUSTOMER_ID                   | Consulte a descrição variável do ambiente acima. |
    | CUSTOMER_KEY                  | Consulte a descrição variável do ambiente acima. |
    | STORAGE_ *N* _SIZE_GB           | N = 5                                       |

3. Adicione o recipiente criar opções para a implantação. Abaixo está um exemplo do recipiente criar opções.

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

Para uma validação do funcionamento correto da Cache Microsoft, execute o seguinte comando no terminal do dispositivo IoT Edge que hospeda o módulo ou qualquer dispositivo na rede.

```bash
    wget "http://<IOT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```

## <a name="single-level-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Gateway de borda Azure IoT de nível único com procuração não autenticada de saída

Neste cenário existe um Azure IoT Edge Gateway que tem acesso aos recursos da CDN através de um representante não autenticado de saída. O Microsoft Connected Cache está a ser configurado para cache de conteúdo de um repositório personalizado e o relatório de resumo foi tornado visível para qualquer pessoa da rede. Abaixo está um exemplo das variáveis ambientais mcC que seriam definidas.

  :::image type="content" source="media/connected-cache-overview/single-level-proxy.png" alt-text="Microsoft Connected Cache Single Level Proxy" lightbox="media/connected-cache-overview/single-level-proxy.png":::

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
```

Para uma validação do funcionamento correto da Cache Microsoft, execute o seguinte comando no terminal do dispositivo Azure IoT Edge que hospeda o módulo ou qualquer dispositivo na rede.

```bash
    wget "http://<Azure IOT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```

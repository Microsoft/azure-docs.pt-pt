---
title: Suporte de WebSocket no Gateway de aplicação do Azure | Documentos da Microsoft
description: Esta página fornece uma descrição geral do suporte de WebSocket de Gateway de aplicação.
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: 8968dac1-e9bc-4fa1-8415-96decacab83f
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: amsriva
ms.openlocfilehash: cc6e2480ea117a288ae94c9cd66be6a354d8230f
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52993331"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Descrição geral do suporte de WebSocket no Gateway de aplicação

Gateway de aplicação fornece suporte nativo para WebSocket em todos os tamanhos de gateway. Não existe qualquer definição configurável pelo utilizador para ativar ou desativar seletivamente o suporte de WebSocket. 

Protocolo WebSocket padronizado nas [RFC6455](https://tools.ietf.org/html/rfc6455) permite uma comunicação duplex completa entre um servidor e um cliente através de uma ligação de TCP de longa execução. Esta funcionalidade permite uma comunicação mais interativa entre o servidor web e o cliente, que pode ser bidirecional sem a necessidade de consulta como necessário em implementações baseadas em HTTP. WebSocket tem baixa sobrecarga ao contrário de HTTP e pode reutilizar a mesma ligação de TCP para várias solicitações/respostas, resultando numa utilização mais eficiente dos recursos. Protocolos de WebSocket foram concebidos para funcionar nas portas de HTTP tradicionais de 80 e 443.

Pode continuar a utilizar um serviço de escuta HTTP padrão na porta 80 ou 443 para receber o tráfego do WebSocket. Tráfego de WebSocket, em seguida, é direcionado para o servidor de back-end ativado WebSocket usando o pool de back-end adequado conforme especificado nas regras de gateway de aplicação. O servidor de back-end deve responder para as sondas de gateway de aplicação, que são descritas no [descrição geral de sonda de estado de funcionamento](application-gateway-probe-overview.md) secção. Sondas de estado de funcionamento do gateway de aplicação são apenas HTTP/HTTPS. Cada servidor de back-end tem de responder às sondas HTTP para o gateway de aplicação para encaminhar o tráfego de WebSocket para o servidor.

## <a name="listener-configuration-element"></a>Elemento de configuração do serviço de escuta

Um serviço de escuta HTTP pode ser utilizado para suportar o tráfego do WebSocket. Segue-se um fragmento de um elemento httpListeners de um ficheiro de modelo de exemplo. Precisaria de serviços de escuta HTTP e HTTPS para suportar o WebSocket e proteger o tráfego do WebSocket. Da mesma forma, pode utilizar o [portal](application-gateway-create-gateway-portal.md) ou [PowerShell](application-gateway-create-gateway-arm.md) para criar um gateway de aplicação com serviços de escuta na porta 80/443 para suportar o tráfego do WebSocket.

```json
"httpListeners": [
        {
            "name": "appGatewayHttpsListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/DefaultFrontendPublicIP"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort443'"
                },
                "Protocol": "Https",
                "SslCertificate": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/sslCertificates/appGatewaySslCert1'"
                },
            }
        },
        {
            "name": "appGatewayHttpListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/appGatewayFrontendIP'"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort80'"
                },
                "Protocol": "Http",
            }
        }
    ],
```

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>Configuração de regra BackendAddressPool, BackendHttpSetting e roteamento

Um BackendAddressPool é usado para definir um conjunto de back-end com servidores WebSocket ativada. O backendHttpSetting é definida com uma porta de back-end 80 e 443. As propriedades de afinidade baseada em cookies e requestTimeouts não são relevantes para o tráfego do WebSocket. Não há qualquer alteração obrigatória na regra de encaminhamento, "Basic" é usada para ligar o serviço de escuta apropriado para o conjunto de endereços de back-end correspondente. 

```json
"requestRoutingRules": [{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpsListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}, {
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }

    }
}]
```

## <a name="websocket-enabled-backend"></a>Back-end do WebSocket ativada

O back-end tem de ter um servidor de web HTTP/HTTPS com configurada na porta (normalmente 80/443) para WebSocket funcione. Este requisito é porque o protocolo WebSocket exige o handshake inicial seja HTTP com a atualização para o protocolo WebSocket como um campo de cabeçalho. Segue-se um exemplo de um cabeçalho:

```
    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: https://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13
```

Outro motivo para isso é que sonda de estado de funcionamento de back-end de gateway de aplicação suporta protocolos HTTP e HTTPS. Se o servidor de back-end não responder para HTTP ou HTTPS sondas, é colocado para fora de conjunto de back-end.

## <a name="next-steps"></a>Passos Seguintes

Após saber mais sobre o suporte de WebSocket, aceda a [criar um gateway de aplicação](application-gateway-create-gateway.md) começar com um WebSocket aplicação web com suporte.


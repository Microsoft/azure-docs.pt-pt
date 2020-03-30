---
title: Suporte WebSocket no Gateway de Aplicação Azure
description: O Gateway de Aplicação fornece suporte nativo para o WebSocket em todos os tamanhos de gateway. Não existem definições configuráveis pelo utilizador.
author: vhorne
ms.author: amsriva
ms.service: application-gateway
services: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.openlocfilehash: baa02c4d946a121f26f421af99835ae2bea18847
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74130341"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Visão geral do suporte webSocket no Gateway da aplicação

O Gateway de Aplicação fornece suporte nativo para o WebSocket em todos os tamanhos de gateway. Não existe qualquer definição configurável pelo utilizador para ativar ou desativar seletivamente o suporte de WebSocket. 

O protocolo WebSocket normalizado no [RFC6455](https://tools.ietf.org/html/rfc6455) permite uma comunicação duplex completa entre um servidor e um cliente sobre uma ligação TCP de longa duração. Esta funcionalidade permite uma comunicação mais interativa entre o servidor web e o cliente, que pode ser bidirecional sem a necessidade de sondagens, conforme exigido nas implementações baseadas em HTTP. WebSocket tem sobrecargas baixas ao contrário de HTTP e pode reutilizar a mesma ligação TCP para múltiplos pedidos/respostas, resultando numa utilização mais eficiente dos recursos. Os protocolos WebSocket são projetados para trabalhar sobre as portas tradicionais HTTP de 80 e 443.

Pode continuar a utilizar um ouvinte HTTP padrão na porta 80 ou 443 para receber o tráfego WebSocket. O tráfego webSocket é então direcionado para o servidor de backend ativado pela WebSocket utilizando o pool de backend apropriado, conforme especificado nas regras de gateway da aplicação. O servidor backend deve responder às sondas de gateway da aplicação, que são descritas na secção de visão geral da sonda de [saúde.](application-gateway-probe-overview.md) As sondas de saúde gateway de aplicação são apenas HTTP/HTTPS. Cada servidor backend deve responder às sondas HTTP para o gateway da aplicação para direcionar o tráfego WebSocket para o servidor.

É usado em apps que beneficiam de comunicação rápida e em tempo real, como chat, dashboard e aplicações de jogos.

## <a name="how-does-websocket-work"></a>Como funciona o WebSocket

Para estabelecer uma ligação WebSocket, um aperto de mão específico baseado em HTTP é trocado entre o cliente e o servidor. Se for bem sucedido, o protocolo de camada de aplicação é "atualizado" de HTTP para WebSockets, utilizando a ligação TCP previamente estabelecida. Uma vez que isto ocorra, http está completamente fora de cena; os dados podem ser enviados ou recebidos utilizando o protocolo WebSocket por ambos os pontos finais, até que a ligação WebSocket esteja fechada. 

![websocket](./media/application-gateway-websocket/websocket.png)

### <a name="listener-configuration-element"></a>Elemento de configuração do serviço de escuta

Um ouvinte HTTP existente pode ser usado para suportar o tráfego WebSocket. Segue-se um fragmento de um elemento httpListeners de um ficheiro de modelo de amostra. Precisaria tanto de ouvintes HTTP como HTTPS para suportar o WebSocket e garantir o tráfego WebSocket. Da mesma forma, pode utilizar o portal ou o Azure PowerShell para criar um portal de aplicação com os ouvintes na porta 80/443 para suportar o tráfego WebSocket.

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

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>BackendAddressPool, BackendHttpSetting e configuração da regra de encaminhamento

Um BackendAddressPool é usado para definir uma piscina de backend com servidores ativados pela WebSocket. O backendHttpSetting é definido com uma porta de backend 80 e 443. O valor de tempo de pedido nas definições http também se aplica à sessão WebSocket. Não é necessária qualquer alteração na regra de encaminhamento, que é utilizada para ligar o ouvinte adequado ao conjunto de endereços correspondentes. 

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

## <a name="websocket-enabled-backend"></a>WebSocket habilitado backend

O seu backend deve ter um servidor web HTTP/HTTPS a funcionar na porta configurada (normalmente 80/443) para que o WebSocket funcione. Este requisito é porque o protocolo WebSocket requer que o aperto de mão inicial seja HTTP com atualização para o protocolo WebSocket como um campo de cabeçalho. Segue-se um exemplo de um cabeçalho:

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

Outra razão para isso é que a aplicação gateway backend health probe suporta apenas protocolos HTTP e HTTPS. Se o servidor de backend não responder às sondas HTTP ou HTTPS, este é retirado da piscina de backend.

## <a name="next-steps"></a>Passos seguintes

Depois de aprender sobre o suporte webSocket, vá criar [um portal de aplicação](quick-create-powershell.md) para começar com uma aplicação web ativada pela WebSocket.
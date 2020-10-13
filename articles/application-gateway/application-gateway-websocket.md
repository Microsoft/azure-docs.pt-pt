---
title: Suporte WebSocket em Gateway de aplicação Azure
description: O Gateway de Aplicação fornece suporte nativo para o WebSocket em todos os tamanhos de gateway. Não existem configurações configuráveis pelo utilizador.
author: vhorne
ms.author: amsriva
ms.service: application-gateway
services: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.openlocfilehash: 59ca7708b24d2e75381290b80adeb671e2b49822
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362700"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Visão geral do suporte do WebSocket no Gateway de aplicações

O Gateway de Aplicação fornece suporte nativo para o WebSocket em todos os tamanhos de gateway. Não existe qualquer definição configurável pelo utilizador para ativar ou desativar seletivamente o suporte de WebSocket. 

O protocolo WebSocket normalizado em [RFC6455](https://tools.ietf.org/html/rfc6455) permite uma comunicação duplex completa entre um servidor e um cliente durante uma longa ligação TCP. Esta funcionalidade permite uma comunicação mais interativa entre o servidor web e o cliente, o que pode ser bidirecional sem a necessidade de sondagens, conforme exigido nas implementações baseadas em HTTP. A WebSocket tem uma sobrecarga baixa ao contrário de HTTP e pode reutilizar a mesma ligação TCP para múltiplos pedidos/respostas, resultando numa utilização mais eficiente dos recursos. Os protocolos WebSocket são projetados para trabalhar sobre as portas HTTP tradicionais de 80 e 443.

Pode continuar a utilizar um ouvinte HTTP padrão na porta 80 ou 443 para receber o tráfego WebSocket. O tráfego WebSocket é então direcionado para o servidor de backend ativado webSocket utilizando o pool de backend apropriado, conforme especificado nas regras do gateway de aplicações. O servidor backend deve responder às sondas de gateway de aplicação, que são descritas na secção de visão geral da sonda de [saúde.](application-gateway-probe-overview.md) As sondas de saúde do gateway de aplicação são apenas HTTP/HTTPS. Cada servidor backend deve responder às sondas HTTP para o gateway de aplicações para encaminhar o tráfego WebSocket para o servidor.

É usado em apps que beneficiam de comunicações rápidas e em tempo real, como chat, dashboard e aplicações de jogos.

## <a name="how-does-websocket-work"></a>Como funciona o WebSocket

Para estabelecer uma ligação WebSocket, é trocado um aperto de mão específico baseado em HTTP entre o cliente e o servidor. Se for bem sucedido, o protocolo de camada de aplicação é "atualizado" de HTTP para WebSockets, utilizando a ligação TCP previamente estabelecida. Uma vez que isto ocorre, HTTP está completamente fora de imagem; os dados podem ser enviados ou recebidos utilizando o protocolo WebSocket por ambos os pontos finais, até que a ligação WebSocket esteja fechada. 

![O diagrama compara um cliente a interagir com um servidor web, conectando-se duas vezes para obter duas respostas, com uma interação WebSocket, onde um cliente se conecta a um servidor uma vez para obter várias respostas.](./media/application-gateway-websocket/websocket.png)

### <a name="listener-configuration-element"></a>Elemento de configuração do serviço de escuta

Um ouvinte HTTP existente pode ser usado para suportar o tráfego WebSocket. O seguinte é um corte de um elemento httpListeners de um ficheiro de modelo de amostra. Precisaria de ouvintes HTTP e HTTPS para apoiar o WebSocket e garantir o tráfego webSocket. Da mesma forma, pode utilizar o portal ou o Azure PowerShell para criar um gateway de aplicações com os ouvintes na porta 80/443 para suportar o tráfego webSocket.

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

Um BackendAddressPool é usado para definir um pool de backend com servidores ativados webSocket. O backendHttpSetting é definido com uma porta de backend 80 e 443. O valor de tempo limite de pedido em DEFINIções HTTP também se aplica à sessão WebSocket. Não é necessária qualquer alteração na regra de encaminhamento, que é utilizada para ligar o ouvinte apropriado ao pool de endereços de backend correspondente. 

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

## <a name="websocket-enabled-backend"></a>Backend ativado webSocket

O seu backend deve ter um servidor web HTTP/HTTPS a funcionar na porta configurada (normalmente 80/443) para que o WebSocket funcione. Este requisito é porque o protocolo WebSocket requer que o aperto de mão inicial seja HTTP com atualização para o protocolo WebSocket como um campo de cabeçalho. Segue-se um exemplo de cabeçalho:

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

Outra razão para isso é que a sonda de saúde backend gateway de aplicação suporta apenas protocolos HTTP e HTTPS. Se o servidor backend não responder às sondas HTTP ou HTTPS, é retirado do pool backend.

## <a name="next-steps"></a>Passos seguintes

Depois de aprender sobre o suporte webSocket, vá [criar uma porta de aplicação](quick-create-powershell.md) para começar com uma aplicação web ativada web ativada pela Web.
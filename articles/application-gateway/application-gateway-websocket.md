---
title: Suporte a WebSocket no gateway Aplicativo Azure
description: O Gateway de Aplicação fornece suporte nativo para o WebSocket em todos os tamanhos de gateway. Não há configurações definidas pelo usuário.
author: vhorne
ms.author: amsriva
ms.service: application-gateway
services: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.openlocfilehash: baa02c4d946a121f26f421af99835ae2bea18847
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74130341"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Visão geral do suporte ao WebSocket no gateway de aplicativo

O Gateway de Aplicação fornece suporte nativo para o WebSocket em todos os tamanhos de gateway. Não existe qualquer definição configurável pelo utilizador para ativar ou desativar seletivamente o suporte de WebSocket. 

O protocolo WebSocket padronizado no [RFC6455](https://tools.ietf.org/html/rfc6455) permite uma comunicação duplex completa entre um servidor e um cliente em uma conexão TCP de longa execução. Esse recurso permite uma comunicação mais interativa entre o servidor Web e o cliente, que pode ser bidirecional sem a necessidade de sondagem conforme necessário em implementações baseadas em HTTP. WebSocket tem baixa sobrecarga ao contrário de HTTP e pode reutilizar a mesma conexão TCP para várias solicitações/respostas, resultando em uma utilização mais eficiente dos recursos. Os protocolos WebSocket são projetados para funcionar em portas HTTP tradicionais de 80 e 443.

Você pode continuar usando um ouvinte HTTP padrão na porta 80 ou 443 para receber o tráfego WebSocket. O tráfego WebSocket é então direcionado para o servidor back-end habilitado para WebSocket usando o pool de back-end apropriado conforme especificado nas regras do gateway de aplicativo. O servidor back-end deve responder às investigações do gateway de aplicativo, que são descritas na seção [visão geral da investigação de integridade](application-gateway-probe-overview.md) . As investigações de integridade do gateway de aplicativo são somente HTTP/HTTPS. Cada servidor de back-end deve responder a investigações HTTP do gateway de aplicativo para rotear o tráfego do WebSocket para o servidor.

Ele é usado em aplicativos que se beneficiam da comunicação rápida e em tempo real, como o chat, o painel e os aplicativos de jogos.

## <a name="how-does-websocket-work"></a>Como funciona o WebSocket

Para estabelecer uma conexão WebSocket, um handshake baseado em HTTP específico é trocado entre o cliente e o servidor. Se for bem-sucedido, o protocolo de camada de aplicativo será "atualizado" de HTTP para WebSockets, usando a conexão TCP estabelecida anteriormente. Assim que isso ocorrer, o HTTP estará completamente fora da imagem; os dados podem ser enviados ou recebidos usando o protocolo WebSocket por ambos os pontos de extremidade, até que a conexão WebSocket seja fechada. 

![WebSocket](./media/application-gateway-websocket/websocket.png)

### <a name="listener-configuration-element"></a>Elemento de configuração do serviço de escuta

Um ouvinte HTTP existente pode ser usado para dar suporte ao tráfego do WebSocket. Veja a seguir um trecho de um elemento httpListeners de um arquivo de modelo de exemplo. Você precisaria de ouvintes HTTP e HTTPS para dar suporte ao WebSocket e ao tráfego de WebSocket seguro. Da mesma forma, você pode usar o portal ou Azure PowerShell para criar um gateway de aplicativo com ouvintes na porta 80/443 para dar suporte ao tráfego de WebSocket.

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

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>BackendAddressPool, BackendHttpSetting e configuração de regra de roteamento

Um BackendAddressPool é usado para definir um pool de back-end com servidores habilitados para WebSocket. O backendHttpSetting é definido com uma porta de back-end 80 e 443. O valor de tempo limite da solicitação em configurações HTTP também se aplica à sessão WebSocket. Não há nenhuma alteração necessária na regra de roteamento, que é usada para vincular o ouvinte apropriado ao pool de endereços de back-end correspondente. 

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

## <a name="websocket-enabled-backend"></a>Back-end habilitado para WebSocket

O back-end deve ter um servidor Web HTTP/HTTPS em execução na porta configurada (geralmente 80/443) para que o WebSocket funcione. Esse requisito é porque o protocolo WebSocket exige que o handshake inicial seja HTTP com a atualização para o protocolo WebSocket como um campo de cabeçalho. Veja a seguir um exemplo de um cabeçalho:

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

Outro motivo para isso é que a investigação de integridade de back-end do gateway de aplicativo dá suporte apenas a protocolos HTTP e HTTPS. Se o servidor back-end não responder a investigações HTTP ou HTTPS, ele será retirado do pool de back-end.

## <a name="next-steps"></a>Passos seguintes

Depois de aprender sobre o suporte ao WebSocket, vá para [criar um gateway de aplicativo](quick-create-powershell.md) para começar com um aplicativo Web habilitado para WebSocket.
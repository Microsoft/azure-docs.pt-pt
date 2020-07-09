---
title: Hospedar vários sites em Azure Application Gateway
description: Este artigo fornece uma visão geral do suporte multi-site do Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 03/11/2020
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: 4d945a255dacd35c61c3c80574b7d46b56de4aab
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80257415"
---
# <a name="application-gateway-multiple-site-hosting"></a>Alojamento de vários sites do Gateway de Aplicação

O alojamento de vários sites permite-lhe configurar mais do que uma aplicação web na mesma porta de entrada de uma aplicação. Esta funcionalidade permite-lhe configurar uma topologia mais eficiente para as suas implementações, adicionando até 100 websites a um gateway de aplicações. Cada site pode ser direcionado para o seu próprio agrupamento de back-end. No exemplo seguinte, o gateway de aplicações serve tráfego para `contoso.com` e a partir de `fabrikam.com` duas piscinas de servidor back-end chamadas ContosoServerPool e FabrikamServerPool.

![imageURLroute](./media/multiple-site-overview/multisite.png)

> [!IMPORTANT]
> As regras são processadas na ordem em que estão listadas no portal para o V1 SKU. Para o V2 SKU, os jogos exatos têm maior precedência. Antes de configurar um serviço de escuta básico, recomenda-se vivamente que configure serviços de escuta de múltiplos sites.  Desta forma, assegura-se que o tráfego é encaminhado para o back-end certo. Se for apresentado primeiro um serviço de escuta básico e este corresponde a um pedido de entrada, o pedido é processado por esse serviço de escuta.

Os pedidos de `http://contoso.com` são encaminhados para ContosoServerPool e os pedidos de `http://fabrikam.com` são encaminhados para FabrikamServerPool.

Da mesma forma, pode hospedar vários subdomínios do mesmo domínio principal na mesma implementação do gateway de aplicação. Por exemplo, pode `http://blog.contoso.com` hospedar-se e `http://app.contoso.com` numa única implementação de gateway de aplicação.

## <a name="host-headers-and-server-name-indication-sni"></a>Cabeçalhos de anfitrião e Indicação do Nome de Servidor (SNI)

Existem três mecanismos comuns que permitem alojar vários sites na mesma infraestrutura.

1. Aloje várias aplicações Web, cada uma num endereço IP exclusivo.
2. Utilize o nome do anfitrião para alojar várias aplicações Web no mesmo endereço IP.
3. Utilize portas diferentes para alojar várias aplicações Web no mesmo endereço IP.

Atualmente, o Application Gateway suporta um único endereço IP público onde ouve o tráfego. Assim, várias aplicações, cada uma com o seu próprio endereço IP não é atualmente suportada. 

O Application Gateway suporta múltiplas aplicações cada uma a ouvir em diferentes portas, mas este cenário requer que as aplicações aceitem o tráfego em portas não standard. Esta não é frequentemente uma configuração que você deseja.

O Gateway de Aplicação conta com os cabeçalhos de anfitrião HTTP 1.1 para alojar mais do que um site no mesmo endereço IP público e porta. Os sites alojados no gateway de aplicações também podem suportar a descarga de TLS com a extensão TLS de Indicação de Nome do Servidor (SNI). Neste cenário, o browser cliente e o web farm de back-end têm de suportar HTTP/1.1 e a extensão TLS conforme definido em RFC 6066.

## <a name="listener-configuration-element"></a>Elemento de configuração do serviço de escuta

Os elementos de configuração httpListener existentes são melhorados para suportar elementos de indicação do nome do anfitrião e do servidor. É usado pela Application Gateway para encaminhar o tráfego para a piscina de backend apropriada. 

O exemplo de código a seguir é o corte de um elemento httpListeners de um ficheiro de modelo:

```json
"httpListeners": [
    {
        "name": "appGatewayHttpsListener1",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/DefaultFrontendPublicIP"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort443'"
            },
            "Protocol": "Https",
            "SslCertificate": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/sslCertificates/appGatewaySslCert1'"
            },
            "HostName": "contoso.com",
            "RequireServerNameIndication": "true"
        }
    },
    {
        "name": "appGatewayHttpListener2",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
            },
            "Protocol": "Http",
            "HostName": "fabrikam.com",
            "RequireServerNameIndication": "false"
        }
    }
],
```

Pode visitar o [modelo do Resource Manager através do alojamento de vários sites](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting) para obter uma implementação baseada num modelo ponto a ponto.

## <a name="routing-rule"></a>Regra de encaminhamento

Não é necessária nenhuma alteração na regra do encaminhamento. Deve continuar a escolher a regra de encaminhamento “Básica” para associar o serviço de escuta de sites ao conjunto de endereços de back-end correspondente.

```json
"requestRoutingRules": [
{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener1')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

},
{
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener2')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/FabrikamServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}
]
```

## <a name="next-steps"></a>Passos seguintes

Depois de saber mais sobre o alojamento de vários sites, veja o artigo [Criar um gateway de aplicação através do alojamento de vários sites](tutorial-multiple-sites-powershell.md) para criar um gateway de aplicação com capacidade para suportar mais do que uma aplicação Web.


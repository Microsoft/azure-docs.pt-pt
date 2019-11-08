---
title: Usar endereço IP privado para roteamento interno para um ponto de extremidade de entrada
description: Este artigo fornece informações sobre como usar IPs privados para roteamento interno e, assim, expor o ponto de extremidade de entrada dentro de um cluster para o restante da VNet.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 570f28ce559ff1c1180ffaacb781b9120b1890a2
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795495"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>Usar o IP privado para roteamento interno para um ponto de extremidade de entrada 

Esse recurso permite expor o ponto de extremidade de entrada dentro do `Virtual Network` usando um IP privado.

## <a name="pre-requisites"></a>Pré-requisitos  
Gateway de aplicativo com uma [configuração de IP privada](https://docs.microsoft.com/azure/application-gateway/configure-application-gateway-with-private-frontend-ip)

Há duas maneiras de configurar o controlador para usar o IP privado para entrada,

## <a name="assign-to-a-particular-ingress"></a>Atribuir a uma entrada específica
Para expor uma entrada específica sobre IP privado, use a anotação [`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip) na entrada.

### <a name="usage"></a>Utilização
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

Para gateways de aplicativo sem um IP privado, o insere anotado com `appgw.ingress.kubernetes.io/use-private-ip: "true"` será ignorado. Isso será indicado no evento de entrada e no log Pod AGIC.

* Erro conforme indicado no evento de entrada

    ```bash
    Events:
    Type     Reason       Age               From                                                                     Message
    ----     ------       ----              ----                                                                     -------
    Warning  NoPrivateIP  2m (x17 over 2m)  azure/application-gateway, prod-ingress-azure-5c9b6fcd4-bctcb  Ingress default/hello-world-ingress requires Application Gateway 
    applicationgateway3026 has a private IP address
    ```

* Erro conforme indicado nos logs do AGIC

    ```bash
    E0730 18:57:37.914749       1 prune.go:65] Ingress default/hello-world-ingress requires Application Gateway applicationgateway3026 has a private IP address
    ```


## <a name="assign-globally"></a>Atribuir globalmente
No caso, o requisito é restringir a exposição de todos os insere sobre o IP privado, usar `appgw.usePrivateIP: true` na configuração `helm`.

### <a name="usage"></a>Utilização
```yaml
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>
    usePrivateIP: true
```

Isso fará com que o controlador de entrada filtre as configurações de endereço IP para um IP privado ao configurar os ouvintes de front-end no gateway de aplicativo.
AGIC entrarão em pane e falharão se `usePrivateIP: true` e nenhum IP privado for atribuído.

> [!NOTE]
> O SKU do gateway de aplicativo v2 requer um IP público. Se você precisar que o gateway de aplicativo seja privado, anexe um [`Network Security Group`](https://docs.microsoft.com/azure/virtual-network/security-overview) à sub-rede do gateway de aplicativo para restringir o tráfego.

---
title: Endereços IP do serviço de gerenciamento de API do Azure | Microsoft Docs
description: Saiba como recuperar os endereços IP de um serviço de gerenciamento de API do Azure e quando eles são alterados.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 64bd71d89446a19d2afe56a32b0c7124e897cb48
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072419"
---
# <a name="ip-addresses-of-azure-api-management"></a>Endereços IP do gerenciamento de API do Azure

Neste artigo, descreveremos como recuperar os endereços IP do serviço de gerenciamento de API do Azure. Os endereços IP podem ser públicos ou privados se o serviço estiver em uma rede virtual.

Você pode usar endereços IP para criar regras de firewall, filtrar o tráfego de entrada para os serviços de back-end ou restringir o tráfego de saída.

## <a name="ip-addresses-of-api-management-service"></a>Endereços IP do serviço de gerenciamento de API

Se o serviço de gerenciamento de API for um serviço de camada desenvolvedor, básico, Standard ou Premium, você poderá recuperar os endereços IP do painel Visão geral do recurso no portal do Azure.

![Endereço IP de gerenciamento de API](media/api-management-howto-ip-addresses/public-ip.png)

Você também pode obtê-los programaticamente com a seguinte chamada à API:

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

Os endereços IP públicos serão parte da resposta:

```json
{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.77.143.53"
    ],
    ...
  }
  ...
}
```

Em implantações de [várias regiões](api-management-howto-deploy-multi-region.md), cada implantação regional tem um endereço IP público.

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>Endereços IP do serviço de gerenciamento de API na VNET

Se o serviço de gerenciamento de API estiver dentro de uma rede virtual, ele terá dois tipos de endereços IP – público e privado.

Os endereços IP públicos são usados para comunicação interna na `3443` porta-para gerenciar a configuração (por exemplo, por meio de Azure Resource Manager). Além disso, quando uma solicitação é enviada do gerenciamento de API para um back-end voltado para o público (voltado para a Internet), um endereço IP público ficará visível como a origem da solicitação.

Endereços VIP (IP virtual privado) são usados para se conectar de dentro da rede a pontos de extremidade de gerenciamento de API-gateways, portal do desenvolvedor e plano de gerenciamento para acesso direto à API. Você pode usá-los para configurar os registros DNS na rede.

Você verá endereços de ambos os tipos na portal do Azure e na resposta da chamada à API:

![Gerenciamento de API no endereço IP da VNET](media/api-management-howto-ip-addresses/vnet-ip.png)


```json
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>

{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.85.20.170"
    ],
    "privateIPAddresses": [
      "192.168.1.5"
    ],
    ...
  },
  ...
}
```

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>Endereços IP do serviço de gerenciamento de API de camada de consumo

Se o serviço de gerenciamento de API for um serviço de camada de consumo, ele não terá um endereço IP dedicado. O serviço de camada de consumo é executado em uma infraestrutura compartilhada e sem um endereço IP determinístico. 

Para fins de restrição de tráfego, você pode usar o intervalo de endereços IP de data centers do Azure. Consulte [o artigo de Azure Functions documentação](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses) para obter etapas precisas.

## <a name="changes-to-the-ip-addresses"></a>Alterações nos endereços IP

Nas camadas Developer, Basic, Standard e Premium do gerenciamento de API, os endereços IP públicos (VIP) são estáticos durante o tempo de vida de um serviço, com as seguintes exceções:

* O serviço é excluído e, em seguida, recriado.
* A assinatura do serviço é [suspensa](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) ou [avisada](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) (por exemplo, para não pagamento) e, em seguida, restabelecida.
* A rede virtual do Azure é adicionada ou removida do serviço.

Em implantações de [várias regiões](api-management-howto-deploy-multi-region.md), o endereço IP regional é alterado se uma região está vazia e, em seguida, restabelecida.

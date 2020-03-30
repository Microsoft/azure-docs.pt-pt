---
title: Endereços IP do serviço de Gestão Da API Azure [ Microsoft Docs
description: Saiba como recuperar os endereços IP de um serviço de Gestão API Azure e quando estes mudam.
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
ms.openlocfilehash: 45501fee9ae6ff47643a1ed197a07c4ba598e981
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047734"
---
# <a name="ip-addresses-of-azure-api-management"></a>Endereços IP da Azure API Management

Neste artigo descrevemos como recuperar os endereços IP do serviço de Gestão API Azure. Os endereços IP podem ser públicos ou privados se o serviço estiver numa rede virtual.

Pode utilizar endereços IP para criar regras de firewall, filtrar o tráfego de entrada para os serviços de backend ou restringir o tráfego de saída.

## <a name="ip-addresses-of-api-management-service"></a>Endereços IP do serviço de Gestão API

Todas as instâncias de serviço de Gestão API em 'Developer, Basic, Standard ou Premium', têm endereços IP públicos, que são exclusivos apenas dessa instância de serviço (não são partilhados com outros recursos). 

Pode recuperar os endereços IP do painel de instrumentos do seu recurso no portal Azure.

![Endereço IP de gestão da API](media/api-management-howto-ip-addresses/public-ip.png)

Também pode revê-los programáticamente com a seguinte chamada da API:

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

Os endereços IP públicos farão parte da resposta:

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

Em [destacamentos multi-regionais,](api-management-howto-deploy-multi-region.md)cada destacamento regional tem um endereço IP público.

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>Endereços IP do serviço de Gestão API em VNet

Se o seu serviço de Gestão API estiver dentro de uma rede virtual, terá dois tipos de endereços IP - públicos e privados.

Os endereços IP públicos são `3443` utilizados para comunicação interna no porto - para gerir a configuração (por exemplo, através do Gestor de Recursos Azure). Na configuração externa da VNet, também são utilizados para o tráfego aPI em tempo de execução. Quando um pedido é enviado da API Management para um backend virado para o público (virado para a Internet), um endereço IP público será visível como a origem do pedido.

Os endereços IP virtuais privados (VIP), disponíveis **apenas** no [modo VNet interno,](api-management-using-with-internal-vnet.md)são utilizados para ligar a partir da rede aos pontos finais da API Management - gateways, portal de desenvolvimento e o plano de gestão para acesso direto à API. Pode usá-los para a instalação de registos DNS dentro da rede.

Você verá endereços de ambos os tipos no portal Azure e na resposta da chamada da API:

![Gestão API no endereço IP VNet](media/api-management-howto-ip-addresses/vnet-ip.png)


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

A API Management utiliza um endereço IP público para ligações fora do VNet e um endereço IP privado para ligações dentro do VNet.

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>Endereços IP do serviço de gestão de API de nível de consumo

Se o seu serviço de Gestão API for um serviço de nível de consumo, não tem um endereço IP dedicado. O serviço de nível de consumo funciona numa infraestrutura partilhada e sem endereço IP determinístico. 

Para efeitos de restrição de tráfego, pode utilizar a gama de endereços IP dos centros de dados Azure. Consulte o artigo de documentação de [Funções Azure](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses) para passos precisos.

## <a name="changes-to-the-ip-addresses"></a>Alterações nos endereços IP

Nos níveis de Desenvolvimento, Básico, Standard e Premium da Gestão aPI, os endereços IP públicos (VIP) são estáticos para o tempo de vida de um serviço, com as seguintes exceções:

* O serviço é apagado e, em seguida, recriado.
* A subscrição do serviço é [suspensa](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) ou [avisada](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) (por exemplo, para não pagamento) e depois reintegrada.
* A Rede Virtual Azure é adicionada ou removida do serviço.
* O serviço de gestão API é alternado entre o modo de implantação de VNet externo e interno.

Em [destacamentos multi-regionais,](api-management-howto-deploy-multi-region.md)o endereço IP regional muda se uma região for desocupada e depois reintegrada.

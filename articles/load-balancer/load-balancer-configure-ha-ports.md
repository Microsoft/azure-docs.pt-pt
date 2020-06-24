---
title: Configure portas de alta disponibilidade para equilibrador de carga Azure
titleSuffix: Azure Load Balancer
description: Saiba como utilizar portas de alta disponibilidade para equilibrar o tráfego interno em todas as portas
services: load-balancer
documentationcenter: na
author: rdhillon
manager: narayan
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: allensu
ms.openlocfilehash: a71a01629f334534d8eb26847a8f3400efbbeafe
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/16/2020
ms.locfileid: "84807828"
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Configure portas de alta disponibilidade para um equilibrador de carga interno

Este artigo fornece um exemplo de implantação de portas de alta disponibilidade num equilibrador de carga interno. Para obter mais informações sobre configurações específicas dos aparelhos virtuais de rede (NVAs), consulte os sites do fornecedor correspondentes.

>[!NOTE]
>O Balanceador de Carga do Azure suporta dois tipos diferentes: Básico e Standard. Este artigo discute o Balancer de Carga Padrão. Para obter mais informações sobre o Balanceador de Carga Básico, consulte [a visão geral do balanceador de carga](load-balancer-overview.md).

A ilustração mostra a seguinte configuração do exemplo de implantação descrito neste artigo:

- Os NVAs são implantados no pool back-end de um equilibrador de carga interno por trás da configuração de Portas de Alta Disponibilidade. 
- A rota definida pelo utilizador (UDR) aplicava-se nas rotas da sub-rede DMZ, todo o tráfego para as NVAs, fazendo o próximo salto como o ip virtual do balançador de carga interno. 
- O equilibrador de carga interno distribui o tráfego para um dos NVAs ativos de acordo com o algoritmo do balançador de carga.
- A NVA processa o tráfego e encaminha-o para o destino original na sub-rede back-end.
- O caminho de retorno pode seguir o mesmo caminho se um UDR correspondente estiver configurado na sub-rede traseira. 

![Implantação de exemplo de portas de alta disponibilidade](./media/load-balancer-configure-ha-ports/haports.png)

## <a name="configure-high-availability-ports"></a>Configure portas de alta disponibilidade

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para configurar portas de alta disponibilidade, crie um equilibrador de carga interno com os NVAs na piscina traseira. Crie uma configuração correspondente da sonda de saúde do balanceador de carga para detetar a saúde NVA e a regra do balançador de carga com portas de alta disponibilidade. A configuração geral relacionada com o balançador de carga é coberta no [Get start](load-balancer-get-started-ilb-arm-portal.md). Este artigo destaca a configuração das portas de alta disponibilidade.

A configuração envolve essencialmente a definição da porta frontal e o valor da porta traseira para **0**. Desa estação o valor do protocolo para **Todos**. Este artigo descreve como configurar portas de alta disponibilidade utilizando o portal Azure, PowerShell e Azure CLI.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Configure uma regra de balançamento de carga de portas de alta disponibilidade com o portal Azure

Para configurar portas de alta disponibilidade utilizando o portal Azure, selecione a caixa de verificação **das portas HA.** Quando selecionada, a configuração da porta e do protocolo relacionada é automaticamente povoada. 

![Configuração de portas de alta disponibilidade através do portal Azure](./media/load-balancer-configure-ha-ports/haports-portal.png)

### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>Configure uma regra de equilíbrio de carga de portas de alta disponibilidade através do modelo de Gestor de Recursos

Pode configurar portas de alta disponibilidade utilizando a versão API 2017-08-01 para o Microsoft.Network/loadBalancers no recurso Balancer de Carga. O seguinte snippet JSON ilustra as alterações na configuração do balançador de carga para portas de alta disponibilidade através da API REST:

```json
    {
        "apiVersion": "2017-08-01",
        "type": "Microsoft.Network/loadBalancers",
        ...
        "sku":
        {
            "name": "Standard"
        },
        ...
        "properties": {
            "frontendIpConfigurations": [...],
            "backendAddressPools": [...],
            "probes": [...],
            "loadBalancingRules": [
             {
                "properties": {
                    ...
                    "protocol": "All",
                    "frontendPort": 0,
                    "backendPort": 0
                }
             }
            ],
       ...
       }
    }
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>Configure uma regra de balançador de carga de portas de alta disponibilidade com PowerShell

Utilize o seguinte comando para criar a regra do balançador de carga das portas de alta disponibilidade enquanto cria o equilibrador de carga interno com PowerShell:

```powershell
lbrule = New-AzLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli"></a>Configure uma regra de balançador de carga de portas de alta disponibilidade com Azure CLI

No passo 4 de Criar um conjunto interno de balançador de [carga,](load-balancer-get-started-ilb-arm-cli.md)utilize o seguinte comando para criar a regra do balançador de carga de portas de alta disponibilidade:

```azurecli
az network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [portos de alta disponibilidade.](load-balancer-ha-ports-overview.md)
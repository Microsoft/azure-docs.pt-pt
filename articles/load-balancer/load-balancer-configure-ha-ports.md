---
title: Configure portas de alta disponibilidade para balanceor de carga Azure
titleSuffix: Azure Load Balancer
description: Saiba como utilizar portas de alta disponibilidade para equilibrar tráfego interno em todos os portos
services: load-balancer
documentationcenter: na
author: rdhillon
manager: narayan
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: allensu
ms.openlocfilehash: c6b8ecb443408f23ae604bd9c8139cb0a2afcd12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477787"
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Configure portas de alta disponibilidade para um equilibrador de carga interna

Este artigo fornece um exemplo de implantação de Portas de Alta Disponibilidade num equilibrador de carga interna. Para obter mais informações sobre configurações específicas para rede de aparelhos virtuais (NVAs), consulte os sites correspondentes do fornecedor.

>[!NOTE]
>O Balanceador de Carga do Azure suporta dois tipos diferentes: Básico e Standard. Este artigo discute o Standard Load Balancer. Para obter mais informações sobre o Equilíbrio de Carga Básica, consulte a [visão geral do Balancer de Carga](load-balancer-overview.md).

A ilustração mostra a seguinte configuração do exemplo de implantação descrito neste artigo:

- Os NVAs são implantados na piscina traseira de um equilibrador de carga interna por trás da configuração das Portas de Alta Disponibilidade. 
- A rota definida pelo utilizador (UDR) aplicada na sub-rede DMZ faz todo o tráfego para os NVAs, fazendo o próximo salto como o IP virtual do equilíbrio de carga interna. 
- O equilibrador de carga interno distribui o tráfego para um dos NVAs ativos de acordo com o algoritmo de equilíbrio de carga.
- A NVA processa o tráfego e encaminha-o para o destino original na sub-rede de back-end.
- O caminho de retorno pode tomar o mesmo caminho se um UDR correspondente estiver configurado na sub-rede de back-end. 

![Implantação de exemplo de portas de alta disponibilidade](./media/load-balancer-configure-ha-ports/haports.png)

## <a name="configure-high-availability-ports"></a>Configure portas de alta disponibilidade

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para configurar portas de alta disponibilidade, configure um equilibrador de carga interno com os NVAs na piscina traseira. Configurar uma configuração correspondente da sonda de saúde do equilibrante para detetar a saúde nva e a regra do equilíbrio de carga com portas de alta disponibilidade. A configuração geral relacionada com o equilíbrio de carga está coberta em [Get started](load-balancer-get-started-ilb-arm-portal.md). Este artigo destaca a configuração das Portas de Alta Disponibilidade.

A configuração envolve essencialmente a definição da porta frontal e do valor da porta traseira para **0**. Detete o valor do protocolo para **Todos**. Este artigo descreve como configurar portas de alta disponibilidade utilizando o portal Azure, PowerShell e Azure CLI.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Configure uma regra de balancedor de carga de portas de alta disponibilidade com o portal Azure

Para configurar portas de alta disponibilidade utilizando o portal Azure, selecione a caixa de verificação ha **ports.** Quando selecionado, a configuração da porta e do protocolo relacionadas é automaticamente povoada. 

![Configuração de Portas de Alta Disponibilidade através do portal Azure](./media/load-balancer-configure-ha-ports/haports-portal.png)

### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>Configure uma regra de equilíbrio de carga das portas de alta disponibilidade através do modelo de Gestor de Recursos

Pode configurar Portas de Alta Disponibilidade utilizando a versão API 2017-08-01 para microsoft.Network/loadBalancers no recurso Balancer de Carga. O seguinte snippet JSON ilustra as alterações na configuração do equilíbrio de carga para portas de alta disponibilidade através da API REST:

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

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>Configure uma regra de balancer de carga de portas de alta disponibilidade com powerShell

Utilize o seguinte comando para criar a regra do equilibrador de carga das Portas de Alta Disponibilidade enquanto cria o equilibrador de carga interna com a PowerShell:

```powershell
lbrule = New-AzLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli"></a>Configure uma regra de equilíbrio de carga de portas de alta disponibilidade com o Azure CLI

No passo 4 de Criar um conjunto interno de [equilíbrio de carga,](load-balancer-get-started-ilb-arm-cli.md)utilize o seguinte comando para criar a regra do equilibrador de carga das Portas de Alta Disponibilidade:

```azurecli
az network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [portas de alta disponibilidade.](load-balancer-ha-ports-overview.md)
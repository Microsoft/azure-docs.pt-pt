---
title: Configurar portas elevada disponibilidade para o Balanceador de carga do Azure
titlesuffix: Azure Load Balancer
description: Saiba como utilizar as portas de elevada disponibilidade para o tráfego interno em todas as portas de balanceamento de carga
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
ms.author: kumud
ms.openlocfilehash: ec43b79109181457f8ef8e214e296969db5dcb26
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56593408"
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Configurar portas elevada disponibilidade para um balanceador de carga interno

Este artigo fornece um exemplo de implementação de elevada disponibilidade portas no balanceador de carga interno. Para obter mais informações sobre configurações específicas de aplicações virtuais (NVAs) de rede, consulte os sites correspondentes do fornecedor.

>[!NOTE]
>O Balanceador de carga do Azure suporta dois tipos diferentes: Basic e Standard. Este artigo aborda o Balanceador de carga Standard. Para obter mais informações sobre o Balanceador de carga básico, consulte [descrição geral do Balanceador de carga](load-balancer-overview.md).

A ilustração mostra o exemplo de implementação descrito neste artigo, a seguinte configuração:

- As NVAs são implementadas no conjunto de back-end de Balanceador de carga interno subjacente à configuração de portas de elevada disponibilidade. 
- A rota definida pelo utilizador (UDR) aplicado as rotas de sub-rede de rede de Perímetro todo o tráfego para as NVAs, tornando o salto seguinte como o internos IP virtual do Balanceador de carga. 
- O Balanceador de carga interno distribui o tráfego para uma das NVAs Active Directory, de acordo com o algoritmo do Balanceador de carga.
- A NVA processa o tráfego e a encaminha para o destino original na sub-rede de back-end.
- O caminho de retorno pode seguir a mesma rota, se um UDR correspondente está configurado na sub-rede de back-end. 

![Exemplo de implementação de portas de elevada disponibilidade](./media/load-balancer-configure-ha-ports/haports.png)

## <a name="configure-high-availability-ports"></a>Configurar portas elevada disponibilidade

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para configurar portas elevada disponibilidade, configure um balanceador de carga interno com as NVAs no conjunto de back-end. Configure uma configuração de pesquisa do Estado de funcionamento de Balanceador de carga correspondente para detetar o estado de funcionamento da NVA e a regra de Balanceador de carga com portas de elevada disponibilidade. A configuração de relacionadas com o Balanceador de carga geral é descrita em [começar](load-balancer-get-started-ilb-arm-portal.md). Este artigo destaca a configuração de portas de elevada disponibilidade.

A configuração envolve, essencialmente, a porta de front-end e o valor de porta de back-end para a definição **0**. Defina o valor de protocolo para **todos os**. Este artigo descreve como configurar portas de elevada disponibilidade com o portal do Azure, PowerShell e CLI do Azure.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Configurar uma regra de Balanceador de carga de portas de elevada disponibilidade com o portal do Azure

Para configurar portas de elevada disponibilidade com o portal do Azure, selecione o **portas HA** caixa de verificação. Quando selecionada, a configuração de porta e protocolo relacionada é preenchida automaticamente. 

![Configuração de portas de elevada disponibilidade através do portal do Azure](./media/load-balancer-configure-ha-ports/haports-portal.png)

### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>Configurar uma regra de balanceamento de carga para portas de alta disponibilidade por meio do modelo do Resource Manager

Pode configurar portas de elevada disponibilidade, utilizando a versão de API de 2017-08-01 para Network/loadbalancers no recurso do Balanceador de carga. O fragmento JSON seguinte ilustra as alterações na configuração de Balanceador de carga para alta disponibilidade portas através da API REST:

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

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>Configurar uma regra de Balanceador de carga de portas de elevada disponibilidade com o PowerShell

Utilize o seguinte comando para criar a regra de Balanceador de carga de alta disponibilidade portas enquanto cria o Balanceador de carga interno com o PowerShell:

```powershell
lbrule = New-AzLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli"></a>Configurar uma regra de Balanceador de carga de portas de elevada disponibilidade com a CLI do Azure

No passo 4 da [criar um conjunto de balanceadores de carga interno](load-balancer-get-started-ilb-arm-cli.md), utilize o seguinte comando para criar a regra de Balanceador de carga de alta disponibilidade portas:

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [alta disponibilidade portas](load-balancer-ha-ports-overview.md).
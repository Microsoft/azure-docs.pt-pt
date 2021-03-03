---
title: Configurar o modo de distribuição do balanceador de carga Azure
titleSuffix: Azure Load Balancer
description: Neste artigo, começa a configurar o modo de distribuição do Azure Load Balancer para suportar a afinidade IP de origem.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2021
ms.author: allensu
ms.openlocfilehash: 2d8d5d84d32cdb8cc813d033f3f3fbb453b538fe
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101739921"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Configure o modo de distribuição para o Balançador de Carga Azure

O Azure Load Balancer suporta dois modos de distribuição para distribuir tráfego nas suas aplicações:

* Baseado em haxixe
* Afinidade do IP de origem

Neste artigo, aprende-se a configurar o modo de distribuição para o seu Balançador de Carga Azure.


## <a name="configure-distribution-mode"></a>Modo de distribuição de configuração

---

# <a name="azure-portal"></a>[**Portal Azure**](#tab/azure-portal)

Pode alterar a configuração do modo de distribuição modificando a regra de equilíbrio de carga no portal.

1. Inscreva-se no portal Azure e localize o grupo de recursos que contém o equilibrador de carga que pretende alterar clicando em **Grupos de Recursos**.
2. No ecrã geral do balançador de carga, selecione **regras de equilíbrio de carga** em **Definições**.
3. No ecrã das regras de equilíbrio de carga, selecione a regra de equilíbrio de carga que deseja alterar o modo de distribuição.
4. De acordo com a regra, o modo de distribuição é alterado alterando a caixa de queda de persistência da **Sessão.** 

Estão disponíveis as seguintes opções: 

* **Nenhum (baseado em haxixe)** - Especifica que os pedidos sucessivos do mesmo cliente podem ser tratados por qualquer máquina virtual.
* **Computador do Cliente (fonte IP affinity two-tuple)** - Especifica que os pedidos sucessivos do mesmo endereço IP do cliente serão tratados pela mesma máquina virtual.
* **Computador e protocolo do Cliente (fonte ip affinity three-tuple)** - Especifica que os pedidos sucessivos a partir do mesmo endereço IP do cliente e combinação de protocolo serão tratados pela mesma máquina virtual.

5. Escolha o modo de distribuição e, em seguida, **selecione Guardar**.

:::image type="content" source="./media/load-balancer-distribution-mode/session-persistence.png" alt-text="Alterar a persistência da sessão na regra do balançador de carga." border="true":::


# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utilize o PowerShell para alterar as definições de distribuição do balanceador de carga numa regra de equilíbrio de carga existente. O seguinte comando atualiza o modo de distribuição: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLoadBalancer -ResourceGroupName MyResourceGroupLB
$lb.LoadBalancingRules[0].LoadDistribution = 'default'
Set-AzLoadBalancer -LoadBalancer $lb
```

Desagrafe o valor do `LoadDistribution` elemento para o tipo de equilíbrio de carga necessário. 

* Especifique **o SourceIP** para o equilíbrio de carga de dois tuple (IP de origem e destino IP). 

* Especifique **o SourceIPProtocol** para o equilíbrio de carga de três tuple (IP de origem, destino IP e tipo de protocolo). 

* Especifique **o padrão** para o comportamento padrão do equilíbrio de carga de cinco tuples.

# <a name="cli"></a>[**CLI**](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

Utilize o CLI azul para alterar as definições de distribuição do balanceador de carga numa regra de equilíbrio de carga existente.  O seguinte comando atualiza o modo de distribuição:

```azurecli-interactive
az network lb rule update \
    --lb-name myLoadBalancer \
    --load-distribution Default \
    --name myHTTPRule \
    --resource-group myResourceGroupLB 
```
Desagrafe o valor do `--load-distribution` tipo de equilíbrio de carga necessário.

* Especifique **o SourceIP** para o equilíbrio de carga de dois tuple (IP de origem e destino IP). 

* Especifique **o SourceIPProtocol** para o equilíbrio de carga de três tuple (IP de origem, destino IP e tipo de protocolo). 

* Especifique **o padrão** para o comportamento padrão do equilíbrio de carga de cinco tuples.

Para obter mais informações sobre o comando utilizado neste artigo, consulte [a atualização da regra lb da rede Az](/cli/azure/network/lb/rule#az_network_lb_rule_update)

---

## <a name="next-steps"></a>Passos seguintes

* [Descrição Geral do Balanceador de Carga do Azure (Azure Load Balancer overview)](load-balancer-overview.md)
* [Começar com a configuração de um equilibrador de carga virado para a Internet](quickstart-load-balancer-standard-public-powershell.md)
* [Configurar definições de tempo limite TCP inativo para o balanceador de carga](load-balancer-tcp-idle-timeout.md)
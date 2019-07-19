---
title: Criar um Load Balancer com front-end zonal-CLI do Azure
titlesuffix: Azure Load Balancer
description: Saiba como criar um Standard Load Balancer com front-end zonal usando CLI do Azure
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: allensu
ms.openlocfilehash: 663567f6e3b078c1cb2afc60c3aaa9fcfb7af4dd
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275248"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-cli"></a>Criar um Standard Load Balancer com front-end zonal usando CLI do Azure

Este artigo percorre a criação de um [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) público com um front-end zonal. Ter um front-end zonal significa que qualquer fluxo de entrada ou saída é servido por uma única zona em uma região. Você pode criar um balanceador de carga com um front-end zonal usando um endereço IP público padrão de zona em sua configuração de front-end. Para compreender o funcionam das zonas de disponibilidade com o Balanceador de carga Standard, veja [zonas de disponibilidade e o Balanceador de carga Standard](load-balancer-standard-availability-zones.md). 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, certifique-se de que instalou a versão mais recente [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e a sessão iniciada numa conta do Azure com [início de sessão az](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest).

> [!NOTE]
> Suporte para zonas de disponibilidade está disponível para os recursos do Azure selecionados e regiões e famílias de tamanhos VM. Para obter mais informações sobre como começar a utilizar e quais recursos do Azure, regiões e famílias de tamanhos VM pode experimentar com as zonas de disponibilidade, consulte [descrição geral das zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Para obter suporte, pode contactar-nos no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [abrir um pedido de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 


## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o seguinte comando:

```azurecli-interactive
az group create --name myResourceGroupZLB --location westeurope
```

## <a name="create-a-public-standard-ip-address"></a>Criar um endereço IP público Standard

Crie um endereço IP público padrão de zona usando o seguinte comando:

```azurecli-interactive
az network public-ip create --resource-group myResourceGroupZLB --name myPublicIPZonal --sku Standard --zone 1
```

## <a name="create-a-load-balancer"></a>Criar um balanceador de carga

Crie um Standard Load Balancer público com o IP público padrão que você criou na etapa anterior usando o seguinte comando:

```azurecli-interactive
az network lb create --resource-group myResourceGroupZLB --name myLoadBalancer --public-ip-address myPublicIPZonal --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>Criar uma sonda LB na porta 80

Crie uma sonda de estado de funcionamento do Balanceador de carga com o seguinte comando:

```azurecli-interactive
az network lb probe create --resource-group myResourceGroupZLB --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>Criar uma regra LB para a porta 80

Crie uma regra de Balanceador de carga com o seguinte comando:

```azurecli-interactive
az network lb rule create --resource-group myResourceGroupZLB --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEnd \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [zonas de disponibilidade e o Balanceador de carga Standard](load-balancer-standard-availability-zones.md).




---
title: Criar um balanceador de carga com o front-end zonal - CLI do Azure
titlesuffix: Azure Load Balancer
description: Saiba como criar um balanceador de carga Standard com o front-end zonal com a CLI do Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: kumud
ms.openlocfilehash: 3b89c11c11276781ec63367247601fccfd2fa858
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66122210"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-cli"></a>Criar um balanceador de carga Standard com o front-end zonal com a CLI do Azure

Este artigo mostra-se através da criação de um público [Balanceador de carga Standard](https://aka.ms/azureloadbalancerstandard) com um front-end zona. Ter um meio de front-end zonal qualquer fluxo de entrada ou saído é atendido por uma única zona de uma região. Pode criar um balanceador de carga com um front-end zonal utilizando um endereço IP público Standard zonal na respetiva configuração de front-end. Para compreender o funcionam das zonas de disponibilidade com o Balanceador de carga Standard, veja [zonas de disponibilidade e o Balanceador de carga Standard](load-balancer-standard-availability-zones.md). 

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

Crie um endereço IP público Standard zonal com o seguinte comando:

```azurecli-interactive
az network public-ip create --resource-group myResourceGroupZLB --name myPublicIPZonal --sku Standard --zone 1
```

## <a name="create-a-load-balancer"></a>Criar um balanceador de carga

Crie um balanceador de carga Standard público com o IP público Standard que criou no passo anterior com o seguinte comando:

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




---
title: Configurar a balança virtual da máquina definida com um Equilibrador de Carga Azure existente - Azure CLI
description: Aprenda a configurar uma balança de máquina virtual definida com um Equilibrador de Carga Azure existente utilizando o Azure CLI.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: a60a6889217ce6ca8dccd5ebf5ee74b8f67a7757
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94518214"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>Configure uma balança de máquina virtual definida com um Equilibrador de Carga Azure existente usando o Azure CLI

Neste artigo, você vai aprender a configurar uma balança de máquina virtual definida com um Azure Load Balancer existente.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos 

- Você precisa de um balanceador de carga padrão existente na subscrição onde o conjunto de escala de máquina virtual será implantado.

- Precisa de uma Rede Virtual Azure para o conjunto de escala de máquina virtual.
 
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Este artigo requer a versão 2.0.28 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Implementar um conjunto de escala de máquina virtual com o equilibrador de carga existente

Substitua os valores nos parênteses pelos nomes dos recursos na sua configuração.

```azurecli-interactive
az vmss create \
    --resource-group <resource-group> \
    --name <vmss-name>\
    --image <your-image> \
    --admin-username <admin-username> \
    --generate-ssh-keys  \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --lb <load-balancer-name> \
    --backend-pool-name <backend-pool-name>
```

O exemplo abaixo implementa uma escala de máquina virtual definida com:

- Conjunto de escala de máquina virtual nomeado **myVMSS**
- Azure Load Balancer nomeado **myLoadBalancer**
- Piscina de backend de balançador de carga chamada **myBackendPool**
- Rede Virtual Azure chamada **myVnet**
- Sub-rede chamada **mySubnet**
- Grupo de recursos nomeado **myResourceGroup**
- Imagem do Servidor Ubuntu para o conjunto de escala de máquina virtual

```azurecli-interactive
az vmss create \
    --resource-group myResourceGroup \
    --name myVMSS \
    --image Canonical:UbuntuServer:18.04-LTS:latest \
    --admin-username adminuser \
    --generate-ssh-keys \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name myVnet\
    --subnet mySubnet \
    --lb myLoadBalancer \
    --backend-pool-name myBackendPool
```
> [!NOTE]
> Após a criação do conjunto de escala, a porta de backend não pode ser modificada para uma regra de equilíbrio de carga utilizada por uma sonda sanitária do equilibrador de carga. Para alterar a porta, pode remover a sonda de saúde atualizando o conjunto de escala de máquina virtual Azure, atualizar a porta e, em seguida, configurar novamente a sonda de saúde.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, implementou uma balança de máquina virtual definida com um Balançador de Carga Azure existente.  Para saber mais sobre conjuntos de balanças de máquinas virtuais e balanceador de carga, consulte:

- [What is Azure Load Balancer?](load-balancer-overview.md) (O que é o Balanceador de Carga do Azure?)
- [O que são os conjuntos de dimensionamento de máquinas virtuais?](../virtual-machine-scale-sets/overview.md)
                                
---
title: Configure conjunto de escala de máquina virtual com um equilíbrio de carga Azure existente - Azure CLI
description: Aprenda a configurar um conjunto de escala de máquina virtual com um Equilíbrio de Carga Azure existente.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: a7f44a21dd404c556d6f3d8444fa70583cd71c57
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80349742"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>Configure um conjunto de escala de máquina virtual com um equilíbrio de carga Azure existente utilizando o ClI Azure

Neste artigo, você aprenderá a configurar um conjunto de escala de máquina virtual com um Equilíbrio de Carga Azure existente. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure.
- Um equilibrador de carga padrão existente na subscrição onde será implantado o conjunto de escala de máquina virtual.
- Uma Rede Virtual Azure para o conjunto de escala de máquina virtual.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se optar por utilizar o CLI localmente, este artigo requer que tenha uma versão da versão 2.0.28 do Azure CLI ou posteriormente instalada. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, consulte [Instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure-cli"></a>Iniciar sessão na CLI do Azure

Assine em Azure.

```azurecli-interactive
az login
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Implementar um conjunto de escala de máquina virtual com o equilibrador de carga existente

Substitua os valores em parênteses pelos nomes dos recursos na sua configuração.

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

O exemplo abaixo implementa um conjunto de escala de máquina virtual com:

- Conjunto de escala de máquina virtual chamado **myVMSS**
- Balancer de carga Azure nomeado **myLoadBalancer**
- Piscina de backend de balancer de carga chamada **myBackendPool**
- Rede Virtual Azure nomeada **myVnet**
- Subnet nomeado **mySubnet**
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
> Após a criação do conjunto de calcário, a porta de backend não pode ser modificada para uma regra de equilíbrio de carga utilizada por uma sonda de saúde do equilibrador de carga. Para alterar a porta, pode remover a sonda de saúde atualizando o conjunto de escala de máquina virtual Azure, atualizar a porta e, em seguida, configurar novamente a sonda de saúde.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, implementou um conjunto de escala de máquina virtual com um Equilíbrio de Carga Azure existente.  Para saber mais sobre conjuntos de escala de máquinas virtuais e equilibrador de carga, consulte:

- [O que é o Balanceador de Carga do Azure?](load-balancer-overview.md)
- [O que são os conjuntos de dimensionamento de máquinas virtuais?](../virtual-machine-scale-sets/overview.md)
                                
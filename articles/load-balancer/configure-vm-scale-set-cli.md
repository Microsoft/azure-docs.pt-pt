---
title: Configurar a balança virtual da máquina definida com um Equilibrador de Carga Azure existente - Azure CLI
description: Aprenda a configurar uma balança de máquina virtual definida com um Equilibrador de Carga Azure existente utilizando o Azure CLI.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: 990380d553cc12d1a87b2e1c7ca9b09864801294
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91334001"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>Configure uma balança de máquina virtual definida com um Equilibrador de Carga Azure existente usando o Azure CLI

Neste artigo, você vai aprender a configurar uma balança de máquina virtual definida com um Azure Load Balancer existente. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure.
- Um balanceador de carga padrão existente na subscrição onde será implantado o conjunto de escala de máquina virtual.
- Uma Rede Virtual Azure para o conjunto de escala de máquina virtual.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se optar por utilizar o CLI localmente, este artigo requer que tenha uma versão da versão Azure CLI 2.0.28 ou posteriormente instalada. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, consulte [Instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure-cli"></a>Iniciar sessão na CLI do Azure

Inscreva-se no Azure.

```azurecli-interactive
az login
```

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

- [O que é o Balanceador de Carga do Azure?](load-balancer-overview.md)
- [O que são os conjuntos de dimensionamento de máquinas virtuais?](../virtual-machine-scale-sets/overview.md)
                                
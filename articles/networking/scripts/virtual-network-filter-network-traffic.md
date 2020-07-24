---
title: Exemplo de script da CLI do Azure - Filtrar o tráfego de rede de VM | Microsoft Docs
description: Exemplo de script da CLI do Azure - Filtrar o tráfego de rede de VM de entrada e saída.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud
ms.openlocfilehash: fb62def47e46e6f72a2f04efaefaa9d36ef9e72c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080906"
---
# <a name="use-an-azure-cli-script-to-filter-inbound-and-outbound-vm-network-traffic"></a>Use um script Azure CLI para filtrar o tráfego de rede VM de entrada e saída

Este script de exemplo cria uma rede virtual com as sub-redes de front-end e back-end. O tráfego de rede de entrada na sub-rede frontal é limitado a HTTP, HTTPS e SSH, enquanto não é permitido o tráfego de saída para a Internet a partir da sub-rede back-end. Depois de executar o script, terá uma máquina virtual com dois NICs. Cada NIC está ligado a outra sub-rede.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/filter-network-traffic/filter-network-traffic.sh  "Filter VM network traffic")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma rede virtual e grupos de segurança de rede. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az network vnet create](/cli/azure/network/vnet) | Cria uma rede e sub-rede virtual de front-end do Azure. |
| [az network subnet create](/cli/azure/network/vnet/subnet) | Cria uma sub-rede de back-end. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet) | Associa os NSGs a sub-redes. |
| [az network public-ip create](/cli/azure/network/public-ip) | Cria um endereço IP público para aceder ao VM a partir da Internet. |
| [az network nic create](/cli/azure/network/nic) | Cria interfaces de rede virtual e anexa-as a sub-redes de front-end e back-end da rede virtual. |
| [az network nsg create](/cli/azure/network/nsg) | Cria grupos de segurança (NSG) que estão associados às sub-redes de front-end e back-end. |
| [az network nsg rule create](/cli/azure/network/nsg/rule) |Cria regras do NSG que permitem ou bloquear portas específicas para sub-redes específicas. |
| [az vm create](/cli/azure/vm) | Cria máquinas virtuais e anexa um NIC para cada VM. Este comando também especifica a imagem da máquina virtual a utilizar e as credenciais administrativas. |
| [az group delete](/cli/azure/group) | Elimina um grupo de recursos e todos os recursos contidos no mesmo. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Amostras adicionais de script CLI em rede podem ser encontradas na documentação da [visão geral da rede Azure](../cli-samples.md)

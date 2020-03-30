---
title: Amostra de script Azure CLI - Peer duas redes virtuais / Microsoft Docs
description: Amostra de script Azure CLI - Peer duas redes virtuais
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud
ms.openlocfilehash: 0e5aaf07b49546aca9f44dcb9a43a79127c82341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890248"
---
# <a name="peer-two-virtual-networks"></a>Peering de rede de duas redes virtuais

Este script cria e conecta duas redes virtuais na mesma região através da rede Azure. Depois de executar o script, irá criar um peering entre duas redes virtuais.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.sh "Peer two networks")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma máquina virtual e todos os recursos relacionados. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | Cria uma rede e sub-rede virtual do Azure. |
| [az network vnet peering create](https://docs.microsoft.com/cli/azure/network/vnet/peering) | Cria um peering entre duas redes virtuais.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Encontrará exemplos adicionais de scripts de CLI de redes na [Documentação de Descrição Geral de Redes do Azure](../cli-samples.md).

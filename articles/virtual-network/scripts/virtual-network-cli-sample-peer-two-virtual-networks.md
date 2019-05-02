---
title: Exemplo de script da CLI do Azure - Peering de redes virtuais | Microsoft Docs
description: Exemplo de script da CLI do Azure - Peering de redes virtuais.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: kumud
ms.openlocfilehash: 82f4f06cd4e615df38f2251be9aef9e8578b9df4
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64726794"
---
# <a name="peer-two-virtual-networks-script-sample"></a>Exemplo de script de peering de duas redes virtuais

Este exemplo de script cria e liga duas redes virtuais na mesma região através da rede do Azure. Depois de executar o script, tem um peering entre duas redes virtuais.

Pode executar o script a partir do [Azure Cloud Shell](https://shell.azure.com/bash) ou a partir de uma instalação local da CLI do Azure. Se utilizar a CLI localmente, este script requer que esteja a executar a versão 2.0.28 ou posterior. Para localizar a versão instalada, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver a executar localmente a CLI, também terá de executar o `az login` para criar uma ligação com o Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.sh "Peer two networks")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados:

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma máquina virtual e todos os recursos relacionados. Cada comando na tabela seguinte liga à documentação específica do comando:

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az network vnet create](/cli/azure/network/vnet) | Cria uma rede e sub-rede virtual do Azure. |
| [az network vnet peering create](/cli/azure/network/vnet/peering) | Cria um peering entre duas redes virtuais.  |
| [az group delete](/cli/azure/vm/extension) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Pode ver exemplos adicionais de scripts da CLI de rede virtual em [Exemplos da CLI de rede virtual](../cli-samples.md).

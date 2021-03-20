---
title: Amostras Azure CLI - Permitir a autoescala baseada no hospedeiro
description: Este script cria um conjunto de dimensionamento de máquinas virtuais com o Ubuntu e utiliza as métricas baseadas no anfitrião para dimensionar automaticamente à medida que a carga de CPU muda.
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.devlang: azurecli
ms.topic: sample
ms.date: 03/27/2018
ms.author: jushiman
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 8e8118a32da296d090ba22122b5188f941419e94
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87502142"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli"></a>Dimensionar automaticamente um conjunto de dimensionamento de máquinas virtuais com a CLI do Azure
Este script cria um conjunto de dimensionamento de máquinas virtuais com o Ubuntu e utiliza as métricas baseadas no anfitrião para dimensionar automaticamente à medida que a carga de CPU muda.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/auto-scale-host-metrics/auto-scale-host-metrics.sh "Automatically scale a virtual machine scale set")]

## <a name="clean-up-deployment"></a>Limpar a implementação
Execute o seguinte comando para remover o grupo de recursos, o conjunto de dimensionamento e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação do script
Este script utiliza os seguintes comandos para criar um grupo de recursos, um conjunto de dimensionamento de máquinas virtuais e todos os recursos relacionados. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/ad/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az vmss create](/cli/azure/vmss) | Cria o conjunto de dimensionamento de máquinas virtuais e liga-o à rede virtual, à sub-rede e ao grupo de segurança de rede. É também criado um balanceador de carga para distribuir o tráfego para instâncias de VM individuais. Este comando também especifica a imagem da VM a ser utilizada e as credenciais administrativas.  |
| [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings) | Cria e aplica regras de dimensionamento automático a um conjunto de dimensionamento de máquinas virtuais. |
| [az group delete](/cli/azure/ad/group) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure/overview).

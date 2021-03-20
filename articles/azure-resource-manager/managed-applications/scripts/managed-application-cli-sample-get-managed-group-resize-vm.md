---
title: Obtenha grupo de recursos geridos & redimensionar VMs - Azure CLI
description: Fornece o script da amostra do Azure CLI que obtém um grupo de recursos geridos numa Aplicação Gerida Azure. O guião redimensiona os VMs.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.custom: devx-track-azurecli
ms.openlocfilehash: 711b516d1ba1154e574b0d8bbd8d86a02d7df018
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87497822"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-azure-cli"></a>Obter recursos num grupo de recursos gerido e redimensionar VMs com a CLI do Azure

Este script obtém os recursos a partir de um grupo de recursos gerido e redimensiona as VMs nesse grupo de recursos.


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../../cli_scripts/managed-applications/get-application/get-application.sh "Get application")]


## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para implementar a aplicação gerida. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az managedapp list](/cli/azure/managedapp#az-managedapp-list) | Lista as aplicações geridas. Forneça os valores de consulta para focar os resultados. |
| [az resource list](/cli/azure/resource#az-resource-list) | Lista os recursos. Forneça um grupo de recursos e os valores de consulta para focar o resultado. |
| [az vm resize](/cli/azure/vm#az-vm-resize) | Atualize um tamanho de máquina virtual. |


## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução às aplicações geridas, veja [Descrição geral da Aplicação Gerida do Azure](../overview.md).
* Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

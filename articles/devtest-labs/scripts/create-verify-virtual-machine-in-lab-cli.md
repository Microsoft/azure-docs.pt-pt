---
title: Azure CLI - Criar e verificar uma máquina virtual em um laboratório
description: Este script da CLI do Azure cria uma máquina virtual num laboratório e verifica se está disponível.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: c7625f62d7897d61903f864b216ccf9aa13648ea
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198426"
---
# <a name="use-azure-cli-to-create-and-verify-availability-of-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Utilizar a CLI do Azure para criar e verificar a disponibilidade de uma máquina virtual num laboratório do Azure DevTest Labs

Este script da CLI do Azure cria uma máquina virtual (VM) num laboratório. A VM criada com base numa imagem do marketplace com autenticação ssh. Em seguida, o script verifica se a VM está disponível para utilização. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/create-verify-virtual-machine-in-lab/create-verify-virtual-machine-in-lab.sh "Create and verify availability of a VM")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos:

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az laboratório vm criar](/cli/azure/lab/vm#az-lab-vm-create) | Cria uma máquina virtual (VM) num laboratório. |
| [az lab vm show](/cli/azure/lab/vm#az-lab-vm-show) | Apresenta o estado da VM num laboratório. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Para obter mais exemplos de scripts da CLI do Azure Lab Services, veja [Exemplos da CLI do Azure Lab Services](../samples-cli.md).

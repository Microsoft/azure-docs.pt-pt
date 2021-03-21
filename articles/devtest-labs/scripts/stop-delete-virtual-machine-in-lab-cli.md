---
title: Azure CLI - Pare e elimine uma máquina virtual em laboratório
description: Este artigo fornece um script Azure CLI que para e elimina uma máquina virtual num laboratório em Azure DevTest Labs.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 3f3802837685281339f0ca355c677e1a0ceac067
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198217"
---
# <a name="use-azure-cli-to-stop-and-delete-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Utilizar a CLI do Azure para parar e eliminar uma máquina virtual num laboratório no Azure DevTest Labs

Este script da CLI do Azure para e elimina uma máquina virtual (VM) num laboratório. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/stop-delete-virtual-machine-in-lab/stop-delete-virtual-machine-in-lab.sh "Stop and delete a VM in a lab")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos:

| Comando | Notas |
|---|---|
| [az lab vm stop](/cli/azure/lab/vm#az-lab-vm-stop) | Para uma máquina virtual (VM) num laboratório. Esta operação pode demorar algum tempo. |
| [az lab vm delete](/cli/azure/lab/vm#az-lab-vm-delete) | Elimina uma máquina virtual (VM) num laboratório. Esta operação pode demorar algum tempo. |


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Para obter mais exemplos de scripts da CLI do Azure Lab Services, veja [Exemplos da CLI do Azure Lab Services](../samples-cli.md).

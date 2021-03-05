---
title: Exemplo de Script da CLI do Azure – Iniciar uma máquina virtual num laboratório | Microsoft Docs
description: Este script da CLI do Azure inicia uma máquina virtual num laboratório no Azure DevTest Labs.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 8a3308a4e13b82cd90e00b6c25edadf4cc8aa4ee
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198171"
---
# <a name="use-azure-cli-to-start-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Utilizar a CLI do Azure para iniciar uma máquina virtual num laboratório no Azure DevTest Labs

Este script da CLI do Azure inicia uma máquina virtual (VM) num laboratório. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/start-connect-virtual-machine-in-lab/start-connect-virtual-machine-in-lab.sh "Start a VM")]


## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos:

| Comando | Notas |
|---|---|
| [az laboratório vm início](/cli/azure/lab/vm#az-lab-vm-start) | Inicia uma máquina virtual (VM) num laboratório. Esta operação pode demorar algum tempo. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Para obter mais exemplos de scripts da CLI do Azure Lab Services, veja [Exemplos da CLI do Azure Lab Services](../samples-cli.md).

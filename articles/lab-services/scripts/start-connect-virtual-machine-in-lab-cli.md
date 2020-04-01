---
title: Exemplo de Script da CLI do Azure – Iniciar uma máquina virtual num laboratório | Microsoft Docs
description: Este script da CLI do Azure inicia uma máquina virtual num laboratório no Azure DevTest Labs.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2018
ms.author: spelluru
ms.custom: mvc
ms.openlocfilehash: 2159e594f0cc6a43905f3562c0ad6f3e3c9984fe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "61084473"
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
| [az laboratório vm começar](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-start) | Inicia uma máquina virtual (VM) num laboratório. Esta operação pode demorar algum tempo. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Para obter mais exemplos de scripts da CLI do Azure Lab Services, veja [Exemplos da CLI do Azure Lab Services](../samples-cli.md).

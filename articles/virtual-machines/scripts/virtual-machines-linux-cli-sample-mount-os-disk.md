---
title: Exemplo do Script da CLI do Azure – Montar o disco do sistema operativo
description: Exemplo do Script da CLI do Azure – Montar o disco do sistema operativo
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 0e5cabdf9c69ead9a2d7f497aceec183cf0be900
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86509669"
---
# <a name="troubleshoot-a-vms-operating-system-disk"></a>Resolver problemas de um disco do sistema operativo de VMs

Este script monta o disco do sistema operativo de uma máquina virtual com falhas ou problemática como um disco de dados numa segunda máquina virtual. Isto pode ser útil quando estiver a resolver problemas do disco ou a recuperar dados.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/mount-os-disk/mount-os-disk.sh "Quick Create VM")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma máquina virtual e todos os recursos relacionados. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az vm show](/cli/azure/vm) | Devolve uma lista de máquinas virtuais. Neste caso, a opção de consulta é utilizada para devolver o disco do sistema operativo da máquina virtual. Este valor é adicionado ao "uri" de um nome de variável. |
| [az vm delete](/cli/azure/vm) | Elimina uma máquina virtual. |
| [az vm create](/cli/azure/vm) | Cria uma máquina virtual.  |
| [az vm disk attach](/cli/azure/vm/disk) | Anexa um disco a uma máquina virtual. |
| [az vm list-ip-addresses](/cli/azure/vm) | Devolve os endereços IP de uma máquina virtual. |

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI da máquina virtual adicionais na [Documentação da VM Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

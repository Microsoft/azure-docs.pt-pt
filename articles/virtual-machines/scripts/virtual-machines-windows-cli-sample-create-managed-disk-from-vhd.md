---
title: Disco gerido a partir de ficheiro VHD na mesma conta (Windows) - Amostra CLI
description: Exemplo do Script da CLI do Azure – Criar um disco gerido a partir de um ficheiro VHD numa conta de armazenamento na mesma subscrição
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 34f05318840bcb4356de0aab8dd12f62ac79d345
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87069247"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli-windows"></a>Criar um disco gerido a partir de um ficheiro VHD numa conta de armazenamento na mesma subscrição com O CLI (Windows)

Este script cria um disco gerido a partir de um ficheiro VHD numa conta de armazenamento na mesma subscrição. Utilize este script para importar um VHD especializado (não generalizado/processado por sysprep) para o disco do SO gerido para criar uma máquina virtual. Em alternativa, utilize-o para importar um VHD de dados para o disco de dados gerido.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-data-disks-from-vhd/create-managed-data-disks-from-vhd.sh "Create managed disk from VHD")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um disco gerido a partir de um VHD. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az disk create](/cli/azure/disk) | Cria um disco gerido com um URI de um VHD numa conta de armazenamento na mesma subscrição |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Máquinas virtuais adicionais e discos geridos Amostras de script CLI podem ser encontradas na [documentação VM do Azure Windows](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

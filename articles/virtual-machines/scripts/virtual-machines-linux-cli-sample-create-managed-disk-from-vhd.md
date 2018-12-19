---
title: Exemplo do Script da CLI do Azure – Criar um disco gerido a partir de um ficheiro VHD numa conta de armazenamento na mesma subscrição | Microsoft Docs
description: Exemplo do Script da CLI do Azure – Criar um disco gerido a partir de um ficheiro VHD numa conta de armazenamento na mesma subscrição
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 93a4a47b539184910f01fe9261562ffd8816ad45
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2018
ms.locfileid: "53581237"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli"></a>Criar um disco gerido a partir de um ficheiro VHD numa conta de armazenamento na mesma subscrição com a CLI

Este script cria um disco gerido a partir de um ficheiro VHD numa conta de armazenamento na mesma subscrição. Utilize este script para importar um VHD especializado (não generalizado/processado por sysprep) para o disco do SO gerido para criar uma máquina virtual. Em alternativa, utilize-o para importar um VHD de dados para o disco de dados gerido. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-data-disks-from-vhd/create-managed-data-disks-from-vhd.sh "Create managed disk from VHD")]


## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um disco gerido a partir de um VHD. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az disk create](https://docs.microsoft.com/cli/azure/disk#az_disk_create) | Cria um disco gerido com um URI de um VHD numa conta de armazenamento na mesma subscrição |

## <a name="next-steps"></a>Passos Seguintes

[Criar uma máquina virtual ao anexar um disco gerido como disco do SO](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Pode ver exemplos do script da CLI da máquina virtual e discos geridos adicionais na [Documentação da VM Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

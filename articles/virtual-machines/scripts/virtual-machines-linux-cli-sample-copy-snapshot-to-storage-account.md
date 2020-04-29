---
title: Copie um instantâneo para uma conta de armazenamento em outra região - amostra CLI
description: Amostra de script Azure CLI - Exportação/Cópia snapshot como VHD para uma conta de armazenamento na mesma ou região diferente.
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc,seodec18
ms.openlocfilehash: 42a516f3c52d7548d08f72687e9f233620a39287
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81458625"
---
# <a name="exportcopy-a-snapshot-to-a-storage-account-in-different-region-with-cli"></a>Exportar/Copiar um instantâneo para uma conta de armazenamento em diferentes regiões com CLI

Este script exporta um instantâneo gerido para uma conta de armazenamento numa região diferente. Gera primeiro o URI de SAS do instantâneo e, em seguida, utiliza-o para o copiar para uma conta de armazenamento numa região diferente. Utilize este script para manter a cópia de segurança dos discos geridos numa região diferente para recuperação após desastre. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshots-to-storage-account/copy-snapshots-to-storage-account.sh "Copy snapshot")]


## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para gerar o URI de SAS para um instantâneo gerido e copia o instantâneo para uma conta de armazenamento com o URI de SAS. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az snapshot grant-access](https://docs.microsoft.com/cli/azure/snapshot) | Gera o SAS só de leitura utilizado para copiar o ficheiro VHD subjacente para uma conta de armazenamento ou transferi-lo para o local  |
| [az storage blob copy start](https://docs.microsoft.com/cli/azure/storage/blob/copy) | Copia um blob de forma assíncrona de uma conta de armazenamento para outra |

## <a name="next-steps"></a>Passos seguintes

[Criar um disco gerido a partir de um VHD](virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

[Criar uma máquina virtual a partir de um disco gerido](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Pode ver exemplos do script da CLI da máquina virtual e discos geridos adicionais na [Documentação da VM Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

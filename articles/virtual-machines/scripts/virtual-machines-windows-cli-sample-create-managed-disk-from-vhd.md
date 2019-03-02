---
title: Exemplo do Script da CLI do Azure – Criar um disco gerido a partir de um ficheiro VHD numa conta de armazenamento na mesma subscrição | Microsoft Docs
description: Exemplo do Script da CLI do Azure – Criar um disco gerido a partir de um ficheiro VHD numa conta de armazenamento na mesma subscrição
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
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
ms.openlocfilehash: 3f68c4ccbcaad682303c9d67c95f8bf8a2d3041b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57248771"
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
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | Cria um disco gerido com um URI de um VHD numa conta de armazenamento na mesma subscrição |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Máquina virtual adicionais e exemplos de script da CLI de discos geridos podem ser encontrados no [documentação de VM do Windows Azure](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

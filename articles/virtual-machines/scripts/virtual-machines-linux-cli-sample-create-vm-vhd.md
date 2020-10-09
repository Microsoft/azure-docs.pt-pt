---
title: Amostra de script Azure CLI - Criar um VM com um VHD
description: Exemplo do Script da CLI do Azure – Criar uma VM com um disco rígido virtual.
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
ms.date: 03/09/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 6f5e30b0d6a072f9a40ae57ebe325461cdfe5bb1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87479637"
---
# <a name="create-a-vm-with-a-virtual-hard-disk"></a>Criar uma VM com um disco rígido virtual

Este exemplo cria uma máquina virtual com um VHD.
Cria um grupo de recursos, uma conta de armazenamento e um contentor. Em seguida, cria uma VM ao carregar o VHD para o contentor.
Substitui a chave pública ssh pela sua chave pública para que tenha acesso à VM.

Precisará de um VHD de arranque. O script procura `~/sample.vhd`.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-vhd/create-vm-vhd.sh "Create VM using a VHD")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli-interactive 
az group delete -n az-cli-vhd
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma máquina virtual, um conjunto de disponibilidade, um balanceador de carga e todos os recursos relacionados. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account list](/cli/azure/storage/account) | Lista as contas de armazenamento |
| [az storage account check-name](/cli/azure/storage/account) | Verifica se um nome de conta de armazenamento é válido e ainda não existe |
| [az storage account keys list](/cli/azure/storage/account/keys) | Lista as chaves para as contas de armazenamento |
| [az storage blob exists](/cli/azure/storage/blob) | Verifica se o blob existe |
| [Criação de contentores de armazenamento az](/cli/azure/storage/container) | Cria um contentor na conta de armazenamento. |
| [Carregamento de blobs de armazenamento az](/cli/azure/storage/blob) | Cria um blob no contentor ao carregar o VHD. |
| [az vm list](/cli/azure/vm) | Utilizado com `--query` e verifica se o nome da VM está em utilização. | 
| [az vm create](/cli/azure/vm/availability-set) | Cria as máquinas virtuais. |
| [az vm list-ip-addresses](/cli/azure/vm#az-vm-list-ip-addresses) | Obtém o endereço IP da VM que foi criada. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI da máquina virtual adicionais na [Documentação da VM Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

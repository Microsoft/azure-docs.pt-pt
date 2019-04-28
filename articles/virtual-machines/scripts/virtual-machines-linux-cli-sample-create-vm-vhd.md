---
title: Exemplo do Script da CLI do Azure – Criar uma VM com um VHD | Microsoft Docs
description: Exemplo do Script da CLI do Azure – Criar uma VM com um disco rígido virtual.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/09/2017
ms.author: allclark
ms.custom: mvc
ms.openlocfilehash: 331bf57c415922a6686ba733b5fbcee24699a152
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61127775"
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
| [az group create](https://docs.microsoft.com/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account list](https://docs.microsoft.com/cli/azure/storage/account) | Lista as contas de armazenamento |
| [az storage account check-name](https://docs.microsoft.com/cli/azure/storage/account) | Verifica se um nome de conta de armazenamento é válido e ainda não existe |
| [az storage account keys list](https://docs.microsoft.com/cli/azure/storage/account/keys) | Lista as chaves para as contas de armazenamento |
| [az storage blob exists](https://docs.microsoft.com/cli/azure/storage/blob) | Verifica se o blob existe |
| [az storage container create](https://docs.microsoft.com/cli/azure/storage/container) | Cria um contentor na conta de armazenamento. |
| [az storage blob upload](https://docs.microsoft.com/cli/azure/storage/blob) | Cria um blob no contentor ao carregar o VHD. |
| [az vm list](https://docs.microsoft.com/cli/azure/vm) | Utilizado com `--query` e verifica se o nome da VM está em utilização. | 
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set) | Cria as máquinas virtuais. |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm#az-vm-list-ip-addresses) | Obtém o endereço IP da VM que foi criada. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Pode ver exemplos do script da CLI da máquina virtual adicionais na [Documentação da VM Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

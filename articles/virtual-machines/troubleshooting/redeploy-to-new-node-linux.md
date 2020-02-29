---
title: Reimplantar máquinas virtuais Linux em Azure Microsoft Docs
description: Como reimplantar máquinas virtuais Linux em Azure para mitigar problemas de ligação SSH.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: genlin
manager: dcscontentpm
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 2c4152ec5e67761ce9be9a81bfbf39e1591787da
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77913519"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Recolocar a máquina virtual Linux para o novo nó Azure
Se enfrentar dificuldades em resolver problemas de resolução de SSH ou acesso a aplicação a uma máquina virtual Linux (VM) em Azure, a reimplantação do VM pode ajudar. Quando reimplanta um VM, move o VM para um novo nó dentro da infraestrutura Azure e, em seguida, alimenta-o de volta. Todas as suas opções de configuração e recursos associados são retidas. Este artigo mostra-lhe como reimplantar um VM utilizando o Azure CLI ou o portal Azure.

> [!NOTE]
> Depois de reimplantar um VM, o disco temporário perde-se e os endereços IP dinâmicos associados à interface de rede virtual são atualizados. 


## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure
Instale o mais recente [Azure CLI](/cli/azure/install-az-cli2) e faça login na sua conta Azure utilizando [login az](/cli/azure/reference-index).

Reutilizar o seu VM com [reimplantação az vm](/cli/azure/vm). O exemplo seguinte reimplanta o VM denominado *myVM* no grupo de recursos chamado *myResourceGroup*:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-classic-cli"></a>Use o CLI clássico azure

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


Instale o [mais recente CLI clássico do Azure](../../cli-install-nodejs.md) e faça login na sua conta Azure. Certifique-se de que está no modo Gestor de Recursos (`azure config mode arm`).

O exemplo seguinte reimplanta o VM denominado *myVM* no grupo de recursos chamado *myResourceGroup*:

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Passos seguintes
Se estiver a ter problemas de ligação ao seu VM, pode encontrar ajuda específica na resolução de problemas de [ligações SSH](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou em passos detalhados de resolução de [problemas do SSH](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Se não conseguir aceder a uma aplicação em execução no seu VM, também pode ler problemas de resolução de problemas de [aplicação.](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



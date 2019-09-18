---
title: Reimplantar Máquinas Virtuais do Linux no Azure | Microsoft Docs
description: Como reimplantar máquinas virtuais do Linux no Azure para mitigar problemas de conexão SSH.
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
ms.openlocfilehash: d8096a14bf2abc0b06b7ab7c3d340a313b1cd24c
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057290"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Reimplantar a máquina virtual Linux no novo nó do Azure
Se você enfrentar dificuldades para solucionar problemas de SSH ou de acesso de aplicativo para uma VM (máquina virtual) do Linux no Azure, a reimplantação da VM poderá ajudar. Quando você reimplanta uma VM, ela move a VM para um novo nó dentro da infraestrutura do Azure e, em seguida, a liga novamente. Todas as opções de configuração e os recursos associados são mantidos. Este artigo mostra como reimplantar uma VM usando CLI do Azure ou o portal do Azure.

> [!NOTE]
> Depois de reimplantar uma VM, o disco temporário é perdido e os endereços IP dinâmicos associados à interface de rede virtual são atualizados. 


## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure
Instale o [CLI do Azure](/cli/azure/install-az-cli2) mais recente e faça logon em sua conta do Azure usando [AZ login](/cli/azure/reference-index).

Reimplante sua VM com [AZ VM Redeploy](/cli/azure/vm). O exemplo a seguir reimplanta a VM chamada *myVM* no grupo de recursos chamado *MyResource*Group:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-classic-cli"></a>Usar a CLI clássica do Azure
Instale a [CLI clássica do Azure mais recente](../../cli-install-nodejs.md) e faça logon em sua conta do Azure. Verifique se você está no modo do Resource Manager (`azure config mode arm`).

O exemplo a seguir reimplanta a VM chamada *myVM* no grupo de recursos chamado *MyResource*Group:

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Passos Seguintes
Se você estiver tendo problemas para se conectar à sua VM, poderá encontrar ajuda específica sobre [como solucionar problemas de conexões SSH](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [etapas detalhadas de solução de problemas de SSH](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Se você não puder acessar um aplicativo em execução em sua VM, você também poderá ler [problemas de solução](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)de problemas de aplicativo.



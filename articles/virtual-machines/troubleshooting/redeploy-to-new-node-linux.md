---
title: Voltar a implementar máquinas virtuais do Linux no Azure | Documentos da Microsoft
description: Como voltar a implementar máquinas virtuais do Linux no Azure para atenuar problemas de ligação de SSH.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: genlin
manager: gwallace
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: cffed7949eff63484c84f385510baa8cd4244958
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710271"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Voltar a implementar a máquina virtual do Linux para o novo nó do Azure
Se tiver dificuldades, resolução de problemas de SSH ou acesso de aplicação a uma máquina virtual (VM) do Linux no Azure, a reimplementação da VM pode ajudar. Quando voltar a implementar uma VM, ele muda a VM para um novo nó dentro da infraestrutura do Azure e, em seguida, liga-o novamente. Todas as suas opções de configuração e os recursos associados são mantidos. Este artigo mostra-lhe como voltar a implementar uma VM com a CLI do Azure ou o portal do Azure.

> [!NOTE]
> Depois de Reimplementar uma VM, o disco temporário é perdido e endereços IP dinâmicos associados à interface de rede virtual são atualizados. 


## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure
Instalar a versão mais recente [CLI do Azure](/cli/azure/install-az-cli2) e inicie sessão na sua do Azure através da conta [início de sessão az](/cli/azure/reference-index).

A implementar a VM com [reimplementação da vm de az](/cli/azure/vm). O exemplo seguinte reimplementa a VM com o nome *myVM* no grupo de recursos com o nome *myResourceGroup*:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-classic-cli"></a>Utilizar a CLI clássica do Azure
Instalar o [mais recente CLI clássica do Azure](../../cli-install-nodejs.md) e inicie sessão na sua conta do Azure. Certifique-se de que está no modo Resource Manager (`azure config mode arm`).

O exemplo seguinte reimplementa a VM com o nome *myVM* no grupo de recursos com o nome *myResourceGroup*:

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Passos seguintes
Se estiver a ter problemas de ligação à sua VM, pode encontrar ajuda específica sobre [resolução de problemas de ligações SSH](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [detalhadas passos de resolução de problemas de SSH](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Se não conseguir aceder uma aplicação em execução na sua VM, pode ainda ler [resolução de problemas de aplicação](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).



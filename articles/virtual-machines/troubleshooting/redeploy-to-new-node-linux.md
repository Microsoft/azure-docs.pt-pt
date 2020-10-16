---
title: Reposicionar máquinas virtuais Linux em Azure Microsoft Docs
description: Como recolocar máquinas virtuais Linux em Azure para mitigar problemas de conexão SSH.
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
ms.openlocfilehash: 6b6abaf10f74b29685309ed5a24a5e6b9f261014
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87074436"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Reimplementar a máquina virtual do Linux no novo nó do Azure
Se tiver dificuldades em resolver problemas de SSH ou acesso a uma máquina virtual Linux (VM) em Azure, a recolocação do VM pode ajudar. Quando se recoloca um VM, move o VM para um novo nó dentro da infraestrutura Azure e, em seguida, volta a fazê-lo. Todas as suas opções de configuração e recursos associados são mantidos. Este artigo mostra-lhe como recolocar um VM utilizando o Azure CLI ou o portal Azure.

> [!NOTE]
> Depois de recolocar um VM, o disco temporário perde-se e os endereços IP dinâmicos associados à interface de rede virtual são atualizados. 


## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure
Instale o mais recente [Azure CLI](/cli/azure/install-az-cli2) e faça login na sua conta Azure utilizando [o login az](/cli/azure/reference-index).

Reimplantar o seu VM com [a az vm recolocado](/cli/azure/vm). O exemplo a seguir reimplantia o VM nomeado *myVM* no grupo de recursos denominado *myResourceGroup*:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-classic-cli"></a>Use o CLI clássico do Azure

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


Instale o mais recente CLI clássico do [Azure](/cli/azure/install-classic-cli) e faça login na sua conta Azure. Certifique-se de que está no modo Gestor de Recursos `azure config mode arm` ().

O exemplo a seguir reimplantia o VM nomeado *myVM* no grupo de recursos denominado *myResourceGroup*:

```console
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Passos seguintes
Se tiver problemas ligados ao seu VM, pode encontrar ajuda específica para [resolver as ligações SSH](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [para as etapas detalhadas de resolução de problemas do SSH](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Se não conseguir aceder a uma aplicação em execução no seu VM, também pode ler [problemas de resolução de problemas de aplicação](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

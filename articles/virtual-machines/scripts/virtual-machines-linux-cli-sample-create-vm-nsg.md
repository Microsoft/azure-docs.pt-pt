---
title: 'Amostra CLI: Criar dois VMs com um NSG interno e externo'
description: Crie dois VMs com NSG interno e externo para proteger o tráfego de rede utilizando o Azure CLI.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 3e3d1fe3bf464892934198d06b602a5b8bcafb67
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75458386"
---
# <a name="secure-network-traffic-between-virtual-machines-using-an-nsg"></a>Tráfego de rede seguro entre máquinas virtuais usando um NSG

Este script cria duas máquinas virtuais e protege o tráfego de entrada de ambas. Uma máquina virtual está acessível na Internet e tem um grupo de segurança de rede (NSG) configurado para permitir tráfego nas portas 22 e 80. A segunda máquina virtual não está acessível na Internet e tem um NSG configurado para permitir apenas o tráfego da primeira máquina virtual.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nsg/create-vm-nsg.sh "Create VM with NSG")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma máquina virtual e todos os recursos relacionados. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | Cria uma rede e sub-rede virtual do Azure. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet) | Cria uma sub-rede. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Cria a máquina virtual e liga-a à placa de rede, à rede virtual, à sub-rede e ao NSG. Este comando também especifica a imagem da máquina virtual a ser utilizada e as credenciais administrativas.  |
| [az network nsg rule list](https://docs.microsoft.com/cli/azure/network/nsg/rule) | Devolve informações sobre uma regra do grupo de segurança de rede. Neste exemplo, o nome da regra é armazenado numa variável para utilização posterior no script. |
| [az network nsg rule update](https://docs.microsoft.com/cli/azure/network/nsg/rule) | Atualiza uma regra NSG. Neste exemplo, a regra de back-end é atualizada para transmitir o tráfego apenas da sub-rede do front-end. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Pode ver exemplos do script da CLI da máquina virtual adicionais na [Documentação da VM Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
